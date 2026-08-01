---
tags: [integrevida, funcionalidades, roles]
created: 2026-08-01
---

# ✨ Funcionalidades de IntegraVida

> Todo lo que la plataforma puede hacer, organizado por rol de usuario.

## 👤 Paciente

### Monitoreo de glucosa
- 📊 Registrar lecturas de glucosa (nivel, fecha/hora, notas)
- 📈 Gráfica de tendencia con Chart.js + análisis SVG (TIR, hiperglucemia, hipoglucemia, coeficiente de variación)
- 🗓️ Filtros de historial: hoy, 7 días, 1 mes, 3 meses, 6 meses, rango personalizado
- 📄 **Exportar PDF** del historial con resumen: últimos 7 días, último mes o todo el historial
- 🎯 Configurar rango personalizado de glucosa (mín/máx) para clasificar Normal/Alto/Bajo

### Gestión de tratamientos
- 💊 Ver tratamientos activos
- 📋 Registrar tomas de medicación (medication intakes)
- 🔔 Alertas de medicación y efectos adversos

### Observaciones clínicas
- 📝 Crear, editar y eliminar observaciones clínicas por categoría (GENERAL, GLUCOSE, MEDICATION, SYMPTOMS, OTHER)
- 🏷️ Filtrar por categoría con pestañas

### Citas médicas
- 📅 Ver citas (solo lectura para paciente)
- 🩺 Seguimiento de historial de citas

### Alertas
- 🔔 Recibir alertas cuando la glucosa sale del rango configurado
- 📌 Marcar alertas como leídas

### Perfil
- 👤 Ver y editar perfil personal (nombre, datos, preferencias)

## 🩺 Doctor

- 👥 **Panel de pacientes:** ver lista de pacientes asignados
- 📋 **Detalle de paciente:** historial clínico completo en una vista
- 🩺 **Acciones clínicas** sobre cada paciente:
  - Crear tratamientos
  - Prescribir medicaciones
  - Registrar diagnósticos
  - Emitir reportes clínicos
  - Crear observaciones clínicas
  - Agendar citas
- 📊 Resumen del paciente (métricas y stats)
- 🧪 Ver registros de glucosa del paciente
- 🗓️ Gestionar citas (crear, editar, eliminar, confirmar, cancelar, reprogramar)

## 🛡️ Admin

- 📊 **Dashboard global:** stats de toda la plataforma
- 👥 **Gestión de usuarios:** listar usuarios, crear doctores y admins
- 🤝 **Asignaciones paciente-doctor:** crear y eliminar asignaciones
- 🏥 Ver todos los pacientes y doctores registrados

## 🔐 Transversal

- 🔑 Autenticación con email/password (JWT) o **Google OAuth2**
- 🌍 Internacionalización **ES / EN**
- 📱 Diseño responsive (mobile + desktop)
- 🎨 UI Glassmorphism con tema coherente

---

## 📎 Relacionado

- [[IntegraVida - Mapa del Proyecto]]
- [[Endpoints API]] — Cómo se exponen estas funcionalidades
- [[Frontend]] — Cómo se implementan en el frontend
- [[Dominio y Modelo]] — Lógica de dominio detrás de cada funcionalidad
