---
tags: [integrevida, frontend, angular, signals, i18n]
created: 2026-08-01
---

# 💻 Frontend (Angular 21)

> SPA standalone-only, organizada por bounded contexts, con estado basado en Signals y diseño Glassmorphism.

## Organización

```
src/app/
├── account-management/      → Auth (login, register, guards, auth.store)
├── admin/                   → Panel admin (users, doctors, assignments, dashboard)
├── appointment-management/  → Citas de pacientes
├── clinical-observations/   → Observaciones clínicas CRUD
├── dashboard/               → Widgets del dashboard
├── doctor/                  → Portal médico (pacientes, acciones clínicas)
├── glucose-monitoring/      → Registro + historial de glucosa
├── integravida/             → Dashboard principal, alertas, efectos adversos
├── medical-followup/        → Búsqueda de medicamentos + history chart (legacy)
├── patient-profile-management/ → Perfil del paciente
└── shared/                  → BaseApi, interceptor, i18n (infra transversal)
```

Cada contexto sigue: `application / domain/model / infrastructure / presentation`.

## Estado (Signals, sin NgRx)

Patrón consistente en todos los stores/servicios:

```ts
private readonly xxxSignal = signal<T>([]);
readonly xxx = this.xxxSignal.asReadonly();
private readonly loadingSignal = signal(false);
readonly loading = this.loadingSignal.asReadonly();
```

- `computed()` para estado derivado
- `effect()` para side effects con `takeUntilDestroyed(this.destroyRef)`
- Stores clave: `auth.store`, `admin.store`, `doctor.store`, `glucose.service`, `alert.service`, `appointment.service`, `dashboard.service`, `profile.service`

## Autenticación (frontend)

- **`AuthStore`** — signals para `currentUser`, `token`, `loading`, `error`; hidrata desde localStorage; valida forma del JWT; `signIn/register/signOut/signInWithGoogle`
- **`UserEntity.fromJwt(token)`** — decodifica claims (sub, role, profileId, patientId, doctorId) con getters `isPatient/isDoctor/isAdmin`
- **Guards funcionales:** `authGuard` + `roleGuard` (`CanActivateFn`)
- **Interceptor:** `authInterceptor` agrega `Authorization: Bearer` y redirige a `/login` en 401
- **Storage:** `auth-session.storage.ts` (localStorage keys `auth_token`/`auth_user`)
- **Google One Tap:** `login-page.ts` inicializa `google.accounts.id`, decodifica el JWT y llama `signInWithGoogle`

## i18n (ES/EN)

- `I18nService` — signals, `SupportedLang = 'en'|'es'`, persistido en localStorage `iv_lang` (default `es`)
- `I18nPipe` — pipe impuro que re-renderiza con `effect()`
- Archivos: `public/assets/i18n/en.json` y `es.json` (~444 líneas cada uno)
- Toggle de idioma en el topbar

## Routing

- Lazy loading: `loadChildren/loadComponent`
- Rutas protegidas con `canActivate: [authGuard, roleGuard]` + `data: { roles: [...] }`
- Wildcard `**` → `/login`

## UI / Diseño

- **Design tokens** en `src/styles.css`: CSS custom properties (`--primary: #3476d4`, `--accent: #35b9a4`, `--background: #f6f8fb`, etc.) estilo Shadcn
- **Glassmorphism:** clase `.iv-glass` (backdrop blur + rgba) aplicada a sidebar, topbar, cards, dropdowns
- **Componentes globales:** `iv-card`, `iv-page`, `iv-btn-*`, `iv-field`, `iv-table`, `iv-badge-*`, `iv-stat-*`, `iv-alert-*`
- **Fuentes:** Inter + Material Symbols Outlined
- **Responsive:** breakpoints 980px/720px, tablas con `min-width` + `overflow-auto`

## Integraciones clave

- **Chart.js** — gráfica de tendencia de glucosa en dashboard
- **jsPDF** — exportación de reportes de glucosa (dashboard + health-history con dropdown semana/mes/todo)
- **RxNav (NIH)** — búsqueda de medicamentos en `medical-followup`
- **Google OAuth2** — login social

## Configuración de entorno

- `src/environments/environment.ts` (prod) y `.development.ts` — ambos apuntan a `https://7fjimjht3a.execute-api.us-east-1.amazonaws.com`
- `proxy.conf.json` mapea `/api/v1` → Render (dev)
- Endpoints por contexto: `XxxApiEndpoint extends BaseApiEndpoint`

## Build y testing

- `ng build` → `dist/Integravida-FrontendServices/browser`
- `ng test` → Vitest + jsdom (sin Karma/Jasmine)
- Deploy a Firebase Hosting

## 📎 Relacionado

- [[Arquitectura]]
- [[Patrones y Diseño]]
- [[Stack Tecnológico]]
- [[Endpoints API]]
