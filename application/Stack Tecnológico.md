---
tags: [integrevida, stack, tecnologias]
created: 2026-08-01
---

# 🧰 Stack Tecnológico

> Todas las tecnologías y dependencias usadas en IntegraVida.

## Backend (`IntegraVida-BackendServices`)

| Tecnología | Versión | Uso |
|------------|---------|-----|
| **Java** | 21 | Lenguaje principal |
| **Spring Boot** | 4.0.6 | Framework base |
| **Spring Data JPA** | (Boot 4) | Persistencia ORM |
| **Spring Security** | (Boot 4) | Autenticación + autorización |
| **Spring MVC** | (Boot 4) | API REST (`spring-boot-starter-webmvc`) |
| **PostgreSQL** | 16 | Base de datos (AWS RDS) |
| **aws-advanced-jdbc-wrapper** | 2.3.5 | Conexión RDS con auth IAM |
| **jjwt** | 0.11.5 | Generación/validación de JWT |
| **Lombok** | (Boot 4) | Reducción de boilerplate |
| **springdoc-openapi** | 3.0.2 | Documentación Swagger/OpenAPI |
| **pluralize** | 1.0.0 | Pluralización en naming strategy |
| **H2** | (test) | DB en memoria para tests |
| **RestClient** | (Spring 6) | Integración REST entre contextos |

### Perfiles
- **application.properties:** AWS RDS IAM, `ddl-auto=none`, `open-in-view=false`, puerto `8096`
- **application-test.yml:** H2, JWT secret de test

## Frontend (`Integravida-FrontendServices`)

| Tecnología | Versión | Uso |
|------------|---------|-----|
| **Angular** | 21.2.10 | Framework SPA (standalone-only, sin NgModules) |
| **TypeScript** | ~5.9.2 | Lenguaje |
| **Signals** | Angular 21 | Estado reactivo (sin NgRx) |
| **RxJS** | ~7.8.0 | Programación reactiva |
| **Chart.js** | ^4.5.1 | Gráficas de glucosa |
| **jsPDF** | ^4.2.1 | Exportación de reportes PDF |
| **Vitest** | ^4.0.8 | Testing (runner por defecto en `ng test`) |
| **jsdom** | — | Entorno DOM para tests |
| **Prettier** | ^3.8.1 | Formato de código |

## Infraestructura

| Servicio | Uso |
|----------|-----|
| **AWS RDS** | PostgreSQL en la nube (`database-2-instance-1.cifwceq8iptd.us-east-1.rds.amazonaws.com:5432/integravida`) |
| **AWS ECS** | Orquestación de contenedores (cluster `IntegraVida-Cluster`) |
| **AWS ECR** | Registro de imágenes Docker (`750906968356.dkr.ecr.us-east-1.amazonaws.com/integravida-backend`) |
| **AWS API Gateway** | Proxy HTTP hacia el backend (`7fjimjht3a.execute-api.us-east-1.amazonaws.com`) |
| **Firebase Hosting** | Frontend en producción (`integravida-appweb.web.app`) |
| **Docker** | Build de la imagen del backend |

## APIs externas

- **RxNav (NIH):** búsqueda de medicamentos — `https://rxnav.nlm.nih.gov/REST/drugs.json?name=...`
- **Google OAuth2:** login con Google (`accounts.google.com/gsi/client`)
- **Google Client ID:** `571714655324-3809t7m3p9v2ms5df23o3a6p8rrp1agq.apps.googleusercontent.com`

---

## 📎 Relacionado

- [[Rutas de los proyectos]]
- [[Arquitectura]]
- [[Infraestructura AWS]]
- [[Frontend]]
