---
tags: [integrevida, moc, index]
created: 2026-08-01
---

# 🏥 IntegraVida — Mapa del Proyecto

> Plataforma web de gestión integral de salud para pacientes con **diabetes**, conectando pacientes, doctores y administradores en una sola aplicación.

## 📋 Enlaces de archivo

- 📂 [[Rutas de los proyectos]] — Ubicación de los repositorios
- 🧭 [[Mapa de Contenidos]] — Notas que conforman el proyecto

## 🏗️ Notas principales (grafo)

| Nota | Descripción |
|------|-------------|
| [[Funcionalidades]] | Todo lo que la plataforma puede hacer (por rol) |
| [[Stack Tecnológico]] | Backend, frontend, base de datos, infraestructura |
| [[Arquitectura]] | DDD modular monolith + Hexagonal + Angular por bounded contexts |
| [[Patrones y Diseño]] | Patrones de diseño usados en backend y frontend |
| [[Dominio y Modelo]] | Bounded contexts, agregados, value objects, eventos |
| [[Autenticación y Seguridad]] | JWT + Google OAuth2, roles, autorización |
| [[Endpoints API]] | Los 72+ endpoints REST del backend |
| [[Frontend]] | Angular 21: signals, i18n, standalone, glassmorphism |
| [[Infraestructura AWS]] | RDS, ECS, ECR, API Gateway, Firecbase Hosting |
| [[Testing y Load Tests]] | Artillery, escenarios, resultados |
| [[Notas de Desarrollo]] | Problemas conocidos, deuda técnica, roadmap |
| [[Resumen Definición Proyecto Tesis]] | Alcance de la tesis: CDSS con Drools sobre DDD/CQRS |
| [[Propuesta de Tesis - MOC]] | Propuesta viva: IntegraVida como sistema base y tesis como mejora evolutiva |
| [[format&requirments/Formato y Requisitos - MOC]] | Requisitos formales: ABET, capitulos, referencias y entregables |
| [[format&requirments/Malla Curricular y Ruta de Tesis]] | Ruta desde 5to/6to ciclo hasta Trabajo de Investigacion I/II |
| [[format&requirments/Fuentes Bibliograficas Iniciales]] | Fuentes base para problema, carrera, APA, Drools y validacion |
| [[Estado del Arte CDSS GlucoSmart]] | 5 tesis UPC del dominio diabetes analizadas |
| [[Análisis Integración CDSS Drools]] | Pasos para integrar Drools en el código real |

---

## 🔑 Resumen ejecutivo

- **Propósito:** Gestión integral de diabetes (monitoreo de glucosa, tratamientos, citas, alertas clínicas)
- **Roles:** 👤 Paciente · 🩺 Doctor · 🛡️ Admin
- **Backend:** Java 21 · Spring Boot 4 · PostgreSQL · Arquitectura Hexagonal + DDD
- **Frontend:** Angular 21 · Signals · i18n EN/ES · Diseño Glassmorphism
- **Deploy:** AWS (ECS + RDS + API Gateway) · Firebase Hosting

## 📎 Relacionado

- [[Mapa de Contenidos]]
- [[Rutas de los proyectos]]
