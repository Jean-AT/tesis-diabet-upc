---
tags: [integrevida, api, rest, endpoints]
created: 2026-08-01
---

# 🔌 Endpoints API

> Los 72+ endpoints REST expuestos por el backend bajo `/api/v1`.

## Autenticación — públicos

| Método | Ruta | Body | Notas |
|--------|------|------|-------|
| POST | `/api/v1/authentication/sign-up` | `SignUpResource` | Solo rol PATIENT público |
| POST | `/api/v1/authentication/sign-in` | `SignInResource` | Devuelve JWT |
| POST | `/api/v1/auth/google/login` | `{email, firstName, lastName}` | Google OAuth2 |

## Admin — requiere `ROLE_ADMIN`

| Método | Ruta |
|--------|------|
| GET | `/api/v1/admin/users` |
| GET | `/api/v1/admin/doctors` |
| GET | `/api/v1/admin/patients` |
| POST | `/api/v1/admin/doctors` |
| POST | `/api/v1/admin/admins` |
| POST | `/api/v1/admin/assignments` |
| GET | `/api/v1/admin/assignments` |
| DELETE | `/api/v1/admin/assignments/{id}` |
| GET | `/api/v1/admin/dashboard` |

## Profiles

| Método | Ruta | Roles |
|--------|------|-------|
| POST | `/api/v1/profiles` | Público |
| GET | `/api/v1/profiles` | ADMIN |
| GET | `/api/v1/profiles/me` | PATIENT, DOCTOR, ADMIN |
| PUT | `/api/v1/profiles/me` | PATIENT, DOCTOR, ADMIN |

## Patients

| Método | Ruta | Roles |
|--------|------|-------|
| POST | `/api/v1/patients` | PATIENT, DOCTOR, ADMIN |
| GET | `/api/v1/patients` | DOCTOR, ADMIN |
| GET | `/api/v1/patients/{id}` | owner/assigned/ADMIN |
| GET | `/api/v1/patients/by-profile` | PATIENT, ADMIN |
| PUT | `/api/v1/patients/{id}` | owner/assigned/ADMIN |
| PATCH | `/api/v1/patients/{id}/deactivate` | assigned/ADMIN |
| PATCH | `/api/v1/patients/{id}/reactivate` | assigned/ADMIN |

## Doctors

| Método | Ruta | Roles |
|--------|------|-------|
| GET | `/api/v1/doctors` | ADMIN |
| POST | `/api/v1/doctors` | ADMIN |
| POST | `/api/v1/doctors/assign-patient` | ADMIN, DOCTOR |
| GET | `/api/v1/doctors/patients` | ADMIN, DOCTOR |
| GET | `/api/v1/doctors/by-patient/{patientId}` | assigned/ADMIN |

## DoctorPatient (`/api/v1/doctors/me/patients`) — requiere DOCTOR + assigned

| Método | Ruta |
|--------|------|
| GET | `/api/v1/doctors/me/patients` |
| GET | `/api/v1/doctors/me/patients/{patientId}/summary` |
| GET | `/api/v1/doctors/me/patients/{patientId}/glucose-records` |
| GET | `/api/v1/doctors/me/patients/{patientId}/treatments` |
| POST | `/api/v1/doctors/me/patients/{patientId}/treatments` |
| POST | `/api/v1/doctors/me/patients/{patientId}/medications` |
| POST | `/api/v1/doctors/me/patients/{patientId}/clinical-observations` |
| POST | `/api/v1/doctors/me/patients/{patientId}/diagnoses` |
| POST | `/api/v1/doctors/me/patients/{patientId}/clinical-reports` |
| POST | `/api/v1/doctors/me/patients/{patientId}/appointments` |

## Treatments / Medications / Intakes — PATIENT o ADMIN

| Método | Ruta |
|--------|------|
| POST | `/api/v1/treatments` |
| GET | `/api/v1/treatments` |
| GET | `/api/v1/treatments/{id}` |
| GET | `/api/v1/treatments/active` |
| PUT | `/api/v1/treatments/{id}` (DOCTOR/ADMIN) |
| POST | `/api/v1/medications` |
| GET | `/api/v1/medications` |
| GET | `/api/v1/medications/{id}` |
| POST | `/api/v1/medication-intakes` |
| GET | `/api/v1/medication-intakes` |

## Glucose Records — PATIENT o ADMIN

| Método | Ruta |
|--------|------|
| POST | `/api/v1/glucose-records` |
| GET | `/api/v1/glucose-records/{id}` |
| GET | `/api/v1/glucose-records` (con `from`/`to` para rango) |
| PUT | `/api/v1/glucose-records/{id}` |
| DELETE | `/api/v1/glucose-records/{id}` |

## Glucose Ranges — PATIENT o ADMIN

| Método | Ruta |
|--------|------|
| GET | `/api/v1/glucose-ranges` |
| PUT | `/api/v1/glucose-ranges` |

## Alerts — PATIENT o ADMIN

| Método | Ruta |
|--------|------|
| GET | `/api/v1/alerts/{id}` |
| GET | `/api/v1/alerts` |
| PATCH | `/api/v1/alerts/{id}/read` |

## Clinical Observations — PATIENT o ADMIN

| Método | Ruta |
|--------|------|
| POST | `/api/v1/clinical-observations` |
| GET | `/api/v1/clinical-observations/{id}` |
| GET | `/api/v1/clinical-observations` |
| PUT | `/api/v1/clinical-observations/{id}` |
| DELETE | `/api/v1/clinical-observations/{id}` |

## Diagnoses / Clinical Reports — DOCTOR o ADMIN

| Método | Ruta |
|--------|------|
| POST | `/api/v1/diagnoses` |
| GET | `/api/v1/diagnoses` |
| POST | `/api/v1/clinical-reports` |
| GET | `/api/v1/clinical-reports` |

## Appointments — variado

| Método | Ruta | Roles |
|--------|------|-------|
| POST | `/api/v1/appointments` | ADMIN, DOCTOR |
| GET | `/api/v1/appointments` | PATIENT, DOCTOR, ADMIN |
| GET | `/api/v1/appointments/{id}` | PATIENT, DOCTOR, ADMIN |
| PUT | `/api/v1/appointments/{id}` | DOCTOR, ADMIN |
| DELETE | `/api/v1/appointments/{id}` | DOCTOR, ADMIN |

---

## Resumen

- **Total:** 72 endpoints
- **POST:** 23 · **GET:** 31 · **PUT:** 8 · **DELETE:** 5 · **PATCH:** 3
- **Públicos:** 3 · **Protegidos:** 69

## 📎 Relacionado

- [[Funcionalidades]]
- [[Arquitectura]]
- [[Autenticación y Seguridad]]
