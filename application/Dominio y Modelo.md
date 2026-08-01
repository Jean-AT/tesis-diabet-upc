---
tags: [integrevida, dominio, ddd, agregados, value-objects, eventos]
created: 2026-08-01
---

# 🧬 Dominio y Modelo

> Modelo de dominio por bounded context: agregados, value objects, eventos y estado.

## Contextos y sus agregados

### `iam` — Identidad y Acceso
- **`User`** — Usuario de la plataforma. Campos: `Long id`, `Username`, `Password` (BCrypt), `email`, `Roles role`. Factories: `createPatient/createDoctor/createAdmin`. *⚠️ Es JPA @Entity directo (único en el sistema).*
- **`Roles`** — enum `PATIENT | DOCTOR | ADMIN`

### `profiles` — Datos personales
- **`Profile`** — `UUID id`, `PersonName name`, `EmailAddress email`, `PhoneNumber phoneNumber`, `DateOfBirth dateOfBirth`. Factory `create()` registra `ProfileCreatedEvent`; `reconstitute()` para mappers.

### `patients` — Registros médicos y asignaciones
- **`Patient`** — `PatientId id`, `profileId`, `medicalRecordNumber`, `notes`, `active`. Behaviors: `updateNotes`, `deactivate`, `reactivate`
- **`Doctor`** — `DoctorId id`, `profileId`, `doctorRecordNumber`, `notes`, `active`
- **`PatientDoctor`** — asignación `{patientId, doctorId, assignedAt}`, factory `assign()`
- **`Treatment`** — máquina de estados: `activate/pause/complete/cancel` con guards (`TreatmentStatus`: PLANNED, ACTIVE, PAUSED, COMPLETED, CANCELLED)
- **`Medication`** — `treatmentId`, `dosage`, `MedicationSchedule`, `discontinue()`
- **`MedicationIntake`** — registro inmutable de dosis tomada

### `medical` — Atención clínica
- **`Appointment`** — máquina de estados: `confirm/cancel/reschedule/updateReason` (`AppointmentStatus`: PENDING, CONFIRMED, CANCELLED)
- **`Diagnosis`** — `DiagnosisStatus`: DRAFT, CONFIRMED, ARCHIVED
- **`ClinicalReport`** — `ClinicalReportStatus`: DRAFT, ISSUED, ARCHIVED

### `monitoring` — Monitoreo de glucosa
- **`GlucoseRecord`** — registra valor, rango, `measuredAt`, severidad; en create/update clasifica contra el rango y emite `GlucoseAlertTriggeredEvent`
- **`GlucoseRange`** — `contains(value)`, `classify(value)` → `Optional<AlertSeverity>` (desviación → severidad)
- **`Alert`** — derivada del evento de glucosa, `markAsRead()`
- **`ClinicalObservation`** — `category/title/content/observedAt`

## Value Objects

Todos son `record` con validación en compact constructor y factories `of/fromString`:

| VO | Contexto | Validación |
|----|----------|------------|
| `PersonName`, `EmailAddress`, `PhoneNumber`, `DateOfBirth` | profiles | email normalizado lowercase, fecha no futura |
| `PatientId`, `DoctorId` | patients/medical/monitoring | UUID |
| `GlucoseValue` | monitoring | valor positivo |
| `Username`, `Password` | iam | no-blank |
| `MedicationSchedule` | patients | colecciones no vacías |
| `TreatmentStatus`, `AppointmentStatus`, `DiagnosisStatus`, `ClinicalReportStatus`, `AlertSeverity` | varios | enums con reglas |

## Eventos de dominio

| Evento | Origen | Consumidor |
|--------|--------|-----------|
| `ProfileCreatedEvent(profileId, email, fullName)` | `Profile.create()` | ❌ Nadie (se limpia sin publicar) |
| `GlucoseAlertTriggeredEvent(...)` | `GlucoseRecord.registerAlertIfNeeded()` | ✅ `GlucoseRecordCommandService.publishAlertIfTriggered()` → crea `Alert` |

**Mecanismo:** `AbstractDomainAggregateRoot` extiende Spring Data `AbstractAggregateRoot` con `registerDomainEvent()/domainEvents()/clearDomainEvents()`. La publicación es **manual** en los command services — no hay `@EventListener` ni outbox. Ver [[Notas de Desarrollo]].

## Estado y máquinas de estado

Los agregados con ciclo de vida (Treatment, Appointment, Diagnosis, ClinicalReport) implementan **state machine** con métodos guard que lanzan `IllegalStateException` si la transición no es válida.

## 📎 Relacionado

- [[Arquitectura]]
- [[Patrones y Diseño]]
- [[Funcionalidades]]
- [[Endpoints API]]
