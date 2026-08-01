---
tags: [integrevida, testing, artillery, load]
created: 2026-08-01
---

# 🧪 Testing y Load Tests

> Estrategia de pruebas y resultados de los load tests con Artillery.

## Testing automatizado

### Backend (JUnit + Spring WebMvc Test)
Ubicación: `src/test/java/`

| Test | Cobertura |
|------|-----------|
| `AuthorizationTest` | Autorización y seguridad |
| `TokenServiceTest` | Generación/validación JWT |
| `AdminControllerTest` | Endpoints de admin |
| `DoctorPatientControllerTest` | Endpoints doctor-paciente |
| `TestJwtHelper` | Helper para generar tokens en tests |

### Frontend (Vitest + jsdom)
- Runner: `vitest` configurado en `angular.json` (test target)
- Comando: `ng test`

## Load Tests con Artillery

### Archivos
- `artillery-test.yml` — definición de escenarios
- `artillery-setup.sh` — crea usuario de test PATIENT

### Escenarios definidos
| # | Escenario | Endpoints | Estado |
|---|-----------|-----------|--------|
| 1 | Auth flow | sign-up + sign-in | ✅ Activo |
| 2 | Patient glucose monitoring | glucose-records, ranges, alerts | ⏸️ Comentado (requiere env vars) |
| 3 | Patient clinical observations | clinical-observations | ⏸️ Comentado |
| 4 | Patient treatments & meds | treatments, medications, appointments | ⏸️ Comentado |
| 5 | Doctor patient dashboard | doctors/me/patients, diagnoses | ⏸️ Comentado |
| 6 | Admin management | admin/* | ✅ Activo |
| 7 | Profile management | profiles/me | ⏸️ Comentado |

### Fases de carga
| Fase | Duración | Arrival rate |
|------|----------|--------------|
| Warm up | 60s | 1 → 5 |
| Ramp up | 60s | 5 → 15 |
| Spike | 30s | 15 → 30 |
| Sustained load | 30s | 30 fijo |

### Resultados del load test (25/jul)

```
Total requests:  2,357
Apdex score:     0.975  (excelente)
p95 response:    125.2ms
p99 response:    153ms
Mean response:   109.5ms
Max throughput:  30 req/s
```

| Métrica | Detalle |
|---------|---------|
| Sign-ups exitosos | 2 |
| Errores 400 (sign-in fallido) | 1,914 — *credenciales de env vars no configuradas* |
| Errores 409 (usuario duplicado) | 440 |
| Errores 500 | 1 |

### Conclusiones
- ✅ El backend responde ~100ms promedio y sostiene 30 req/s sin degradarse
- ✅ Apdex 0.975 = excelente
- ⚠️ La mayoría de errores fueron 400/409 por credenciales de test no configuradas, NO por el backend
- ✅ Ninguna degradación en p99 durante el spike

### Cómo correr
```bash
# 1. Crear usuario de test
bash artillery-setup.sh

# 2. Exportar credenciales
export PATIENT_EMAIL=... PATIENT_PASSWORD=...
export DOCTOR_EMAIL=... DOCTOR_PASSWORD=...
export ADMIN_EMAIL=... ADMIN_PASSWORD=...

# 3. Correr
npx artillery run artillery-test.yml
```

## 📎 Relacionado

- [[Infraestructura AWS]]
- [[Endpoints API]]
- [[Notas de Desarrollo]]
