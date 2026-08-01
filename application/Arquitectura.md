---
tags: [integrevida, arquitectura, ddd, hexagonal]
created: 2026-08-01
---

# 🏗️ Arquitectura de IntegraVida

> Enfoque: **Modular Monolith** con **DDD** (Domain-Driven Design) organizado por bounded contexts, **Hexagonal / Ports & Adapters** en el backend, y **bounded contexts + Signals** en el frontend.

## Backend — Modular Monolith + DDD

La aplicación Spring Boot está dividida en **6 bounded contexts**, cada uno con capas `domain / application / infrastructure / interfaces`:

```
src/main/java/com/integravida/IntegraVidaBackend/
├── iam/          → Identidad y acceso (users, roles, JWT, Google OAuth2, admin)
├── profiles/     → Datos personales (Profile aggregate + eventos)
├── patients/     → Pacientes, doctores, asignaciones, tratamientos, medicinas
├── medical/      → Citas, diagnósticos, reportes clínicos
├── monitoring/   → Registros de glucosa, rangos, alertas, observaciones clínicas
└── shared/       → Result, ApplicationError, naming strategy, OpenAPI, i18n, CORS
```

### Capas por contexto

| Capa | Rol |
|------|-----|
| `domain/` | Agregados, value objects, eventos, reglas de negocio (POJOs puros) |
| `application/` | Servicios de uso (Command/Query), puertos outbound (interfaces de repositorio) |
| `infrastructure/` | Adaptadores JPA, mappers, integración REST entre contextos |
| `interfaces/` | Controladores REST, recursos, assemblers, ACL |

### Puntos clave

- **Modular monolith:** un solo deployable, pero el código está aislado por contexto (evita acoplamiento accidental)
- **Hexagonal:** los contextos dependen de *puertos* (interfaces) no de implementaciones concretas
- **Integración entre contextos:**
  - En-proceso (ACL): `profiles` → `iam` y `patients` vía facades
  - REST: `medical` y `monitoring` → `patients` vía `RestClient` (con fallback local)
- **CQRS ligero:** cada contexto separa `XxxCommandService` (escritura, `@Transactional`) de `XxxQueryService` (lectura, `readOnly = true`)

### Inconsistencia conocida
⚠️ El agregado `User` del contexto `iam` es un `@Entity` JPA directo en `domain/model`, a diferencia del resto que usa POJOs + adapters. Ver [[Notas de Desarrollo]].

## Frontend — Angular por bounded contexts

```
src/app/
├── account-management/      → Auth (login, register, guards, auth.store)
├── admin/                   → Panel admin
├── appointment-management/  → Citas
├── clinical-observations/   → Observaciones clínicas
├── dashboard/               → Widgets del dashboard
├── doctor/                  → Portal médico
├── glucose-monitoring/      → Registro e historial de glucosa
├── integravida/             → Dashboard, alertas, efectos adversos (contexto mayor)
├── medical-followup/        → Búsqueda de medicamentos + history chart
├── patient-profile-management/ → Perfil
└── shared/                  → BaseApi, interceptor, i18n
```

Cada contexto de frontend sigue `application / domain / infrastructure / presentation`. Detalle en [[Frontend]].

## Diagrama de flujo general

```
┌──────────────┐     ┌─────────────────┐     ┌─────────────────────┐
│  Angular SPA │────▶│  AWS API Gateway │────▶│  Spring Boot (ECS)  │
│  (Firebase)  │     │  (CORS + proxy)  │     │  (6 bounded contexts)│
└──────────────┘     └─────────────────┘     └──────────┬──────────┘
                                                        │
                                                 ┌──────▼──────┐
                                                 │ PostgreSQL  │
                                                 │ (AWS RDS)   │
                                                 └─────────────┘
```

## 📎 Relacionado

- [[Dominio y Modelo]]
- [[Patrones y Diseño]]
- [[Frontend]]
- [[Infraestructura AWS]]
- [[Endpoints API]]
