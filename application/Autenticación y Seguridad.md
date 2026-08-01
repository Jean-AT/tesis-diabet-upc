---
tags: [integrevida, seguridad, autenticacion, jwt, oauth2]
created: 2026-08-01
---

# 🔐 Autenticación y Seguridad

> JWT + Google OAuth2, roles, y autorización por recurso.

## Flujo de autenticación

### Email / Password
1. `POST /api/v1/authentication/sign-up` — crea User + Profile + Patient (solo rol PATIENT público)
2. `POST /api/v1/authentication/sign-in` — verifica credenciales, resuelve profile/patient/doctor, emite JWT
3. El JWT lleva claims: `userId, profileId, patientId, doctorId, role, sub(username)`

### Google OAuth2 (frontend-initiated)
1. El frontend carga `https://accounts.google.com/gsi/client`
2. `login-page.ts` inicializa `google.accounts.id`, decodifica el credential JWT de Google
3. `POST /api/v1/auth/google/login` con `{email, firstName, lastName}`
4. `GoogleOAuth2Service.handleGoogleLogin`:
   - Si el email existe → resuelve profile/patient
   - Si no → crea User (password aleatorio) + Profile + Patient (MRN generado)
5. Devuelve `{userId, token, email, role}` con JWT de la app

## Token JWT

| Componente | Detalle |
|------------|---------|
| **Algoritmo** | HS256 (HMAC) |
| **Secret** | `security.jwt.secret` (min 32 chars, env `JWT_SECRET`) |
| **Expiración** | `security.jwt.expiration-ms` (default 86400000 = 24h) |
| **Librería** | jjwt 0.11.5 (`TokenServiceImpl`) |
| **Claims extra** | `profileId`, `patientId`, `doctorId`, `role` para resolver el sujeto sin queries |

## Filtros y configuración

- **`JwtAuthenticationFilter`** — `OncePerRequestFilter`: parsea `Authorization: Bearer`, valida, crea `UsernamePasswordAuthenticationToken` con authority `ROLE_<role>` y guarda claims en `details`
- **`SecurityConfig`** — stateless, CSRF off, `permitAll` para `/api/v1/authentication/**`, `/api/v1/auth/google/**`, swagger; registra el filtro antes de `UsernamePasswordAuthenticationFilter`
- **`DefaultPasswordEncoder`** — BCrypt `PasswordEncoder`
- **`JwtClaimsExtractor`** — lee claims del SecurityContext para los controllers

## Autorización

### Nivel de clase/método
- `@PreAuthorize("hasRole('ADMIN')")` — AdminController completo
- `@PreAuthorize("hasRole('DOCTOR') and @ownerShipService.isDoctorAssignedToPatient(#patientId)")` — DoctorPatientController
- `@PreAuthorize("hasRole('PATIENT') and @ownerShipService.isOwnerPatient(#id)")` — PatientsController

### OwnerShipService
Verifica propiedad del recurso usando `JwtClaimsExtractor`:
- `isOwnerPatient`
- `isDoctorAssignedToPatient`
- `isOwnerDoctor`

## Endpoints públicos vs protegidos

- **Públicos:** sign-up, sign-in, google/login, swagger docs
- **Protegidos:** el resto (69 endpoints) requieren Bearer token

## CORS (configuración clave)

`SecurityConfig.corsConfigurationSource` permite origins:
- `http://localhost:4200`
- `https://7fjimjht3a.execute-api.us-east-1.amazonaws.com`
- `https://integravida-appweb.web.app`

⚠️ El API Gateway también maneja CORS para el preflight OPTIONS. Ver [[Notas de Desarrollo]] para el incidente del 403.

## 📎 Relacionado

- [[Arquitectura]]
- [[Patrones y Diseño]]
- [[Endpoints API]]
- [[Infraestructura AWS]]
