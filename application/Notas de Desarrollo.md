---
tags: [integrevida, dev-notes, deuda-tecnica, roadmap]
created: 2026-08-01
---

# 📝 Notas de Desarrollo

> Problemas conocidos, deuda técnica, decisiones y roadmap.

## 🐛 Problemas conocidos

### Backend
- ⚠️ **`User` aggregate es JPA @Entity** — único agregado que mezcla dominio con persistencia (el resto usa POJOs + adapters). Inconsistente con la arquitectura hexagonal.
- ⚠️ **`findByEmail` roto en AdminController** — usaba `ProfileCommandService.findByEmail()` (no existe); el método correcto es `ProfileQueryService.getByEmail().toOptional()`. Arreglado temporalmente para build, luego revertido.
- ⚠️ **`SnakeCaseWithPluralizedTablePhysicalNamingStrategy` no cableado** — la clase existe pero no está referenciada en `application.properties` ni como bean. Solo se usan `@Table(name=...)` explícitos.
- ⚠️ **`ProfileCreatedEvent` nunca se publica** — `ProfileCommandService.create()` limpia eventos sin consumidor. No hay event bus.
- ⚠️ **`IamContextFacade` es stub** — hardcodea `1L`/`true`.
- ⚠️ **`FallbackExternalPatientServiceAdapter` vacío** — stub sin implementación.
- 📌 **`GET /admin/users` no devuelve recordNumber** — `UserResource` no incluye número de record médico/doctor. Requiere backend change.
- 📌 **`GET /doctors/patients` devuelve solo UUIDs** — sin nombres de paciente (requiere lookup vía Profile).
- 📌 **`medical-followup` es legacy** — usa API externa RxNav directamente.

### Frontend
- 📌 **`/api/v1/symtoms`** — typo en constante de endpoint.
- 📌 **Two auth styles coexisten** — interceptor global Bearer + llamadas explícitas `authHeaders(token)`. Unificar.
- 📌 **Dependencia `firebase` sin uso** — en package.json pero no se importa (solo hosting).

## 🚨 Incidentes de producción

### CORS 403 (Google login / todas las llamadas)
- **Síntoma:** Preflight OPTIONS → 403 "Invalid CORS request" desde el frontend en Firebase.
- **Causa raíz:** Doble problema:
  1. API Gateway (HTTP API v2) sin `CorsConfiguration` → preflight bloqueado.
  2. `SecurityConfig` no incluía `https://integravida-appweb.web.app` en `allowedOrigins`.
- **Fix:**
  1. `aws apigatewayv2 update-api` con `AllowOrigins` (Firebase + localhost) y `AllowMethods` con OPTIONS.
  2. Agregada la origin de Firebase a `SecurityConfig.corsConfigurationSource`.
- **Lección:** Ambos CORS layers (API Gateway + Spring Security) deben estar configurados; el frontend envía preflight con `Origin` = dominio real.

### AdminController /assignments "perdido"
- Al revertir el AdminController con `git checkout`, se perdió el `GET /assignments` agregado en sesión previa. Re-agregado manualmente.

### Google OAuth2 `invalid_client` (bloqueado)
- Google Cloud Console requiere `http://localhost:4200` en **Authorized JavaScript origins**. El usuario debe configurarlo. El API Gateway también debe estar en las URIs de redirect.

## 🗺️ Roadmap / Mejoras propuestas

- [ ] Unificar `User` a POJO + adapter (consistencia hexagonal)
- [ ] Publicar `ProfileCreatedEvent` (event bus / listener real)
- [ ] Implementar `IamContextFacade` real
- [ ] Cablear `PhysicalNamingStrategy` o eliminar la clase
- [ ] Devolver recordNumber en `GET /admin/users`
- [ ] Devolver nombres de paciente en `GET /doctors/patients`
- [ ] Añadir read models reales (CQRS completo) si escala
- [ ] Outbox pattern para eventos si crece el tráfico
- [ ] Corregir typo `symtoms`
- [ ] Eliminar dependencia `firebase` del frontend
- [ ] Migrar auth header a un solo estilo

## 📎 Relacionado

- [[IntegraVida - Mapa del Proyecto]]
- [[Arquitectura]]
- [[Testing y Load Tests]]
