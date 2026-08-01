---
tags: [integrevida, aws, infraestructura, deploy, docker]
created: 2026-08-01
---

# ☁️ Infraestructura AWS y Deploy

> Arquitectura de despliegue: ECS + RDS + API Gateway + ECR + Firebase.

## Diagrama

```
            ┌──────────────────────────────────────┐
            │           AWS (us-east-1)            │
┌────────┐  │  ┌──────────────┐   ┌──────────────┐  │
│ Firebase│──┼─▶│API Gateway   │──▶│ ECS Cluster  │  │
│ Hosting │  │  │ integravida- │   │ IntegraVida- │  │
│ (webapp)│  │  │ proxy        │   │ Cluster      │  │
└────────┘  │  └──────────────┘   │   (task)     │  │
            │         ▲           └──────┬───────┘  │
            │         │                  │          │
            │         │           ┌──────▼──────┐   │
            │         │           │ RDS PostgreSQL│  │
            │         │           │ (IAM auth)   │  │
            │         │           └─────────────┘  │
            │  ┌──────┴──────┐                     │
            │  │ ECR Registry │  ← Docker images   │
            │  └─────────────┘                     │
            └──────────────────────────────────────┘
```

## Componentes

### AWS RDS (PostgreSQL)
- **Endpoint:** `database-2-instance-1.cifwceq8iptd.us-east-1.rds.amazonaws.com:5432/integravida`
- **Auth:** IAM (`aws-advanced-jdbc-wrapper` con `wrapperPlugins=iam`)
- **DDL:** `ddl-auto=none` (migraciones manuales)

### AWS ECS (Fargate)
- **Cluster:** `IntegraVida-Cluster`
- **Service:** `integravida-backend-task-service-1xnylbsg`
- **Task definition:** `integravida-backend-task`
- **Env vars de tarea:** `DB_PASSWORD`, `JWT_SECRET`, `GOOGLE_CLIENT_ID`, `GOOGLE_CLIENT_SECRET`
- **IP pública:** cambia con cada deploy (se consulta tras desplegar)

### AWS ECR
- **Registry:** `750906968356.dkr.ecr.us-east-1.amazonaws.com/integravida-backend`
- **Tag:** `latest`

### AWS API Gateway (HTTP API)
- **ID:** `7fjimjht3a`
- **Endpoint:** `https://7fjimjht3a.execute-api.us-east-1.amazonaws.com`
- **Nombre:** `integravida-proxy`
- **Ruta:** catch-all `ANY /{proxy+}` → proxy al backend ECS
- **CORS:** configurado con `AllowOrigins` (Firebase + localhost), `AllowMethods` incluye OPTIONS

### Firebase Hosting
- **URL:** `https://integravida-appweb.web.app`
- **Config:** `firebase.json` con SPA rewrite a `/index.html`

### Google Cloud Console
- **Authorized JS origins:** `http://localhost:4200`, API Gateway, `https://integravida-appweb.web.app`
- **Authorized redirect URIs:** API Gateway `/login/oauth2/code/google`, Firebase

## Deploy del backend (flujo manual)

```bash
# 1. Build
docker build -t integravida-backend .

# 2. Login + push a ECR
aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 750906968356.dkr.ecr.us-east-1.amazonaws.com
docker tag integravida-backend:latest 750906968356.dkr.ecr.us-east-1.amazonaws.com/integravida-backend:latest
docker push 750906968356.dkr.ecr.us-east-1.amazonaws.com/integravida-backend:latest

# 3. Forzar nuevo deployment en ECS
aws ecs update-service --cluster IntegraVida-Cluster --service integravida-backend-task-service-1xnylbsg --force-new-deployment --region us-east-1

# 4. Verificar estado
aws ecs describe-services --cluster IntegraVida-Cluster --services integravida-backend-task-service-1xnylbsg --region us-east-1
```

## Deploy del frontend

```bash
cd /home/jean/WebstormProjects/Integravida-FrontendServices
npx ng build
firebase deploy --only hosting
```

## Incidentes conocidos

- **CORS 403:** El API Gateway no tenía CORS configurado → preflight OPTIONS fallaba. Se configuró en el API y en `SecurityConfig`. Ver [[Notas de Desarrollo]].

## 📎 Relacionado

- [[Rutas de los proyectos]]
- [[Stack Tecnológico]]
- [[Arquitectura]]
- [[Testing y Load Tests]]
