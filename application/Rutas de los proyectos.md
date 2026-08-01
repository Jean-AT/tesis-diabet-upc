---
tags: [integrevida, rutas, index]
created: 2026-08-01
---

# 📂 Rutas de los proyectos

## Repositorios

| Proyecto | Ruta |
|----------|------|
| **Backend** | `/home/jean/IdeaProjects/IntegraVida-BackendServices` |
| **Frontend** | `/home/jean/WebstormProjects/Integravida-FrontendServices` |

## Obsidian Vault

| Nota | Ruta |
|------|------|
| Este mapa | `IntegraVida/IntegraVida - Mapa del Proyecto.md` |
| Grafo completo | [[Mapa de Contenidos]] |

---

## Backend (`IntegraVida-BackendServices`)

- **Stack:** Spring Boot 4.0.6, Java 21, Spring Data JPA, Spring Security, PostgreSQL 16 (AWS RDS), jjwt 0.11.5, Lombok, springdoc-openapi 3.0.2
- **Root package:** `com.integravida.IntegraVidaBackend`
- **Arquitectura:** Modular monolith con DDD organizado por bounded contexts (ver [[Arquitectura]] y [[Dominio y Modelo]])
- **Tests:** `src/test/java/` — JUnit + Spring WebMvc Test
- **Perfil de producción:** `src/main/resources/application.properties` (AWS RDS IAM, puerto 8096)

## Frontend (`Integravida-FrontendServices`)

- **Stack:** Angular 21.2.10 (standalone-only), Signals, Chart.js, jsPDF, Vitest
- **App root:** `src/app/` organizado por bounded contexts (ver [[Frontend]])
- **Deploy:** Firebase Hosting → `dist/Integravida-FrontendServices/browser`
- **Tests:** Vitest + jsdom (`ng test`)

---

## Servicios en la nube

- **API Gateway:** `https://7fjimjht3a.execute-api.us-east-1.amazonaws.com`
- **Firebase:** `https://integravida-appweb.web.app`
- **Render (backup/proxy dev):** `https://integravida-backendservices.onrender.com`
- Más detalle en [[Infraestructura AWS]]

## 📎 Relacionado

- [[IntegraVida - Mapa del Proyecto]]
- [[Mapa de Contenidos]]
