---
tags: [integrevida, patrones, diseño, gof, ddd]
created: 2026-08-01
---

# 🧩 Patrones y Diseño

> Patrones de diseño aplicados en backend (Spring/DDD) y frontend (Angular).

## Backend — Patrones de diseño

### Arquitecturales
| Patrón | Dónde | Detalle |
|--------|-------|---------|
| **Bounded Contexts (DDD)** | 6 módulos (`iam, profiles, patients, medical, monitoring, shared`) | Aislamiento por contexto con su propio modelo |
| **Ports & Adapters (Hexagonal)** | `application/ports/outbound/*` + `infrastructure/**/adapters/*` | Repositorios por interfaz, adaptadores JPA |
| **Anti-Corruption Layer (ACL)** | `iam/interfaces/acl/IamContextFacade`, `profiles/interfaces/acl/ProfilesContextFacade` | Facades que aislan contextos |
| **CQRS (ligero)** | `XxxCommandService` (write) + `XxxQueryService` (read-only) | Separación lectura/escritura sin read models |
| **Modular Monolith** | Deploy único | Spring Boot con paquetes aislados |

### Patrones de dominio (DDD)
| Patrón | Detalle |
|--------|---------|
| **Aggregate Root** | `Patient`, `Doctor`, `Treatment`, `GlucoseRecord`, `Profile`, etc. con invariantes |
| **Value Objects** | `record`s con validación (`EmailAddress`, `GlucoseValue`, `PersonName`, ...) |
| **Factory + Reconstitution** | `create()` para nuevos, `reconstitute()` para persistencia (mappers) |
| **Domain Events** | Vía Spring `AbstractAggregateRoot` → `GlucoseAlertTriggeredEvent` |
| **State Machine** | Guards en `Treatment`, `Appointment`, `Diagnosis`, `ClinicalReport` |

### Patrones de aplicación/API
| Patrón | Detalle |
|--------|---------|
| **Result Type** | `Result<T, E>` sealed interface (`Success/Failure`) en `shared/application/result` |
| **ApplicationError** | Códigos tipados: `VALIDATION_ERROR`, `*_NOT_FOUND`, `BUSINESS_RULE_VIOLATION`, `*_CONFLICT`, `UNEXPECTED_ERROR` |
| **ResponseEntityAssembler** | Traduce `Result` → `ResponseEntity` con el mapper de recurso |
| **ErrorResponseAssembler** | Mapea `ApplicationError` → HTTP status + mensaje i18n |
| **GlobalExceptionHandler** | `@RestControllerAdvice` unifica el formato de error |
| **Ownership checks** | `@PreAuthorize` con SpEL `@ownerShipService.isDoctorAssignedToPatient(#patientId)` |
| **JWT claims en contexto** | `JwtClaimsExtractor` lee `userId/profileId/patientId/doctorId/role` del SecurityContext |

### Persistencia
| Patrón | Detalle |
|--------|---------|
| **Repository (Spring Data)** | `XxxJpaRepository` interfaces + `XxxRepositoryAdapter` |
| **Mapper** | `PatientsJpaMapper`, `MonitoringJpaMapper`, `MedicalJpaMapper`, `ProfileJpaMapper` (domain ↔ JPA) |
| **PhysicalNamingStrategy** | `SnakeCaseWithPluralizedTablePhysicalNamingStrategy` (⚠️ no cableado — ver [[Notas de Desarrollo]]) |
| **AuditableAbstractPersistenceEntity** | `@MappedSuperclass` con `@CreatedDate/@LastModifiedDate` |
| **IAM DB Auth** | `aws-advanced-jdbc-wrapper` con plugin IAM para RDS |

## Frontend — Patrones de diseño

| Patrón | Detalle |
|--------|---------|
| **Standalone Components** | Sin NgModules; bootstrap con `bootstrapApplication` |
| **Signals State** | `signal()/computed()/effect()` en stores (sin NgRx) |
| **Store pattern** | `auth.store.ts`, `admin.store.ts`, `doctor.store.ts`, services de feature |
| **Lazy Loading + Guards** | `loadChildren/loadComponent` + `canActivate: [authGuard, roleGuard]` con roles |
| **HTTP Interceptor** | `authInterceptor` (Bearer token + redirect 401) |
| **Base classes genéricas** | `BaseApiEndpoint`, `BaseApi`, `BaseAssembler`, `BaseEntity` en `shared/infrastructure` |
| **i18n runtime** | `I18nService` + `I18nPipe` (impuro, con `effect()`), JSON en `assets/i18n/{en,es}.json` |
| **Facade por feature** | Cada contexto expone su propia API/endpoint |

## 📎 Relacionado

- [[Arquitectura]]
- [[Dominio y Modelo]]
- [[Frontend]]
- [[Autenticación y Seguridad]]
