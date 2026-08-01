---
tags: [integravida, tesis, cddss, drools, roadmap]
created: 2026-08-01
---

# 🧠 Análisis de Integración: CDSS con Drools

> Análisis de viabilidad técnica para integrar el **motor de reglas de negocio Drools** (propuesta de tesis) en la arquitectura real de IntegraVida. Este documento mapea el estado actual del código, el vacío que cubre el CDSS y los pasos concretos para hacerlo realidad.

## 🎯 Contexto de tesis

- La propuesta (ver [[Resumen Definición Proyecto Tesis]]) plantea un **CDSS basado en Drools** sobre farmacovigilancia, con tres ejes de aporte: trazabilidad/explicabilidad, experimento empírico de mantenibilidad y correlación temporal multi-entidad.
- El [[Estado del Arte CDSS GlucoSmart]] confirma que **ninguna de las 5 tesis UPC revisadas usa un motor de reglas declarativo** — todas hardcodean las alertas.

## 🗺️ Estado actual del código (lo que existe hoy)

### La lógica clínica está hardcodeada en el dominio de `monitoring`

| Pieza                                                   | Ubicación                                                             | Qué hace                                                                            |
| ------------------------------------------------------- | --------------------------------------------------------------------- | ----------------------------------------------------------------------------------- |
| `GlucoseRange.classify()`                               | `monitoring/domain/model/aggregates/GlucoseRange.java:75`             | Compara el valor contra `[min,max]` y calcula desviación relativa                   |
| `AlertSeverity.fromDeviationRatio()`                    | `monitoring/domain/model/valueobjects/AlertSeverity.java:11`          | Umbrales hardcodeados: ≤10% LOW, ≤25% MEDIUM, ≤50% HIGH, >50% CRITICAL              |
| `GlucoseRecord.registerAlertIfNeeded()`                 | `monitoring/domain/model/aggregates/GlucoseRecord.java:97`            | Registra `GlucoseAlertTriggeredEvent` si hay severidad                              |
| `GlucoseRecordCommandService.publishAlertIfTriggered()` | `monitoring/application/services/GlucoseRecordCommandService.java:86` | Extrae el evento y persiste `Alert` (materialización imperativa, sin Spring Events) |

### Vacíos críticos detectados (oportunidad de tesis)

1. **Solo se evalúa glucosa vs. rango.** No existe correlación con medicación activa, intakes recientes, síntomas ni citas.
2. **`ClinicalObservation` no tiene lógica de alerta** — es un agregado "mudo" (`monitoring/domain/model/aggregates/ClinicalObservation.java`).
3. **`MedicationIntake` no dispara nada** — solo valida pertenencia al paciente (`patients/application/services/MedicationIntakeCommandService.java:27`).
4. **Sin correlación cross-context.** La única integración medical↔monitoring es `hasGlucoseRecordsByPatientId` para precondición de diagnóstico (`medical/application/ports/outbound/ExternalMonitoringService.java`).
5. **Los eventos de dominio NO se publican vía Spring** — se extraen manualmente con `domainEvents()`. No hay `ApplicationEventPublisher` ni `@TransactionalEventListener`.
6. **No existe auditoría de reglas activadas** — la alerta guarda mensaje pero no *qué regla* la disparó ni *con qué facts*.

### Lo que ya está disponible como "Facts" para Drools

- `GlucoseRecord`, `GlucoseRange`, `ClinicalObservation` (monitoring)
- `Medication`, `MedicationIntake`, `Treatment` (patients)
- `Appointment`, `Diagnosis`, `ClinicalReport` (medical)

## 🔧 Lo que se necesita para integrar Drools

### Fase 0 — Dependencias y config

| Paso        | Detalle                                                                                                 |
| ----------- | ------------------------------------------------------------------------------------------------------- |
| Dependencia | Agregar a `pom.xml`: `org.drools:drools-engine` (o `drools-core` + `drools-compiler` + `drools-mvel`)   |
| Versión     | Compatible con Java 21 / Spring Boot 4: usar **Drools 9.x+** (org.kie)                                  |
| Bean        | `DroolsConfig` con `KieContainer` (lectura de `/rules/*.drl`) y `KieSession` (stateful, por invocación) |

### Fase 1 — Capa de reglas en el bounded context

Estructura propuesta (alineada con el borrador de tesis):

```
monitoring/
├── application/
│   ├── internal/commandservices/   ← invoca KieSession
│   └── rules/                       ← NUEVO
│       ├── hypoglycemia-rules.drl
│       ├── adverse-effects-rules.drl
│       ├── temporal-correlation-rules.drl
│       └── DroolsConfig.java
├── domain/
│   ├── model/aggregates/            ← se reutilizan como Facts
│   └── model/events/                ← nuevo RuleExecutionEvent / RuleActivationRecord
├── infrastructure/
│   └── persistence/jpa/             ← nueva entidad RuleAudit
└── interfaces/rest/                 ← nuevo endpoint de auditoría
```

**Decisión clave de diseño**: ¿drools actúa en la capa de aplicación (el command service carga facts → dispara `fireAllRules()` → recoge resultados) o dentro del aggregate de dominio? Recomendación: **capa de aplicación**, para no acoplar Drools al dominio (el dominio no debe conocer el motor). El aggregate expone datos, Drools decide.

### Fase 2 — Reglas de farmacovigilancia (de las entrevistas)

Reglas iniciales derivadas de las entrevistas ya documentadas (Jorge, Virgilia, Andy, Dr. Walter Gómez):

1. **Hipoglucemia aguda**: glucosa < 70 + horario próximo a dosis de insulina/antidiabético → alerta HIGH con indicación.
2. **Hiperglucemia persistente**: 2+ valores > 180 en las últimas 6h → alerta MEDIUM.
3. **Correlación temporal multi-entidad** (el caso estrella de la tesis): síntoma reportado (ej. mareo) dentro de las 2h posteriores a un intake de metformina → alerta con trazabilidad de ambas entidades.
4. **Proximidad a cita**: alerta CRITICAL/recordatorio si hay valores fuera de rango 24h antes de una cita confirmada.
5. **Severidad compuesta**: combinación glucosa fuera de rango + medicación activa → escalar severidad.

Cada regla se escribe en `.drl` con nombre, condición declarativa y `insert` de un `RuleActivationRecord` (auditoría).

### Fase 3 — Trazabilidad / auditoría (eje de aporte 1)

- Nuevo value object `RuleActivationRecord` con: `ruleName`, `ruleGroup`, `severity`, `facts` involucrados (ids de glucose record, intake, observación), `timestamp`, `patientId`.
- Persistir en nueva tabla `rule_audit` (JPA entity + adapter, siguiendo el patrón `XxxRepository`/`XxxRepositoryAdapter` del proyecto).
- Nuevo endpoint: `GET /api/v1/rule-audit?patientId=` (rol PATIENT/DOCTOR/ADMIN) — visible en frontend para explicabilidad.
- Opcional: reemplazar la materialización imperativa actual (`publishAlertIfTriggered`) por `ApplicationEventPublisher` + `@TransactionalEventListener`, para que el CDSS reaccione de forma desacoplada.

### Fase 4 — Experimento empírico de mantenibilidad (eje de aporte 2)

El experimento debe comparar **antes (if/else)** vs. **después (Drools)**:

| Métrica | Cómo medirla |
|---|---|
| Complejidad ciclomática | SonarQube / jQAssistant (o plugin jacoco). Nota: hoy **no existe** plugin jacoco ni sonar en el pom — hay que agregarlo. |
| LOC por regla añadida | Contar líneas de una regla nueva en `if/else` vs. una regla `.drl` |
| Tiempo real de modificación | Script cronometrado: agregar una regla nueva sin romper las 5 existentes (antes y después) |
| Tasa de defectos | Número de reglas preexistentes que fallan tras cada cambio (suite de tests) |

Requisito previo: **suite de tests de regresión** sobre las reglas (JUnit + H2, ya existe `spring-boot-starter-test`). Ideal: tabla de decisión parametrizada con los casos clínicos.

### Fase 5 — Validación con experto (endocrinólogo)

- Patrón repetido en las tesis UPC: validación por juicio de experto con encuesta Likert + Alfa de Cronbach (como Zapata & Bravo 2024).
- Preparar cuestionario de aceptación de alertas y de utilidad clínica de la trazabilidad.

## ⚠️ Riesgos y decisiones pendientes

| Decisión | Opciones | Recomendación |
|---|---|---|
| Versión Drools | 8.x (kie) vs 9.x/10.x | Usar la última estable compatible con Java 21 |
| Dónde vive el motor | `monitoring` vs nuevo bounded context `clinical/` | Empezar en `monitoring`; si crece, extraer a context propio |
| DRL estático vs KieContainer runtime | Reglas compiladas vs editables | DRL en el build (estático) para la tesis; mencionar edición runtime como trabajo futuro |
| Eventos | Mantener extracción manual vs Spring Events | Migrar a Spring Events: es requisito para que el CDSS reaccione desacoplado |
| OCR / IoT / LSTM | Trabajo futuro vs eliminar del alcance | Mantener solo como "trabajo futuro" (ver [[Resumen Definición Proyecto Tesis]]) |

## 🏃 Secuencia de implementación sugerida

1. Agregar dependencias Drools + `DroolsConfig` con una regla "hola mundo" → verificar KieSession arranca.
2. Portar `GlucoseRange.classify` + `AlertSeverity.fromDeviationRatio` a una primera `.drl` (mismo comportamiento, tests idénticos en verde).
3. Implementar la regla de correlación temporal multi-entidad (intake + síntoma + glucosa) con auditoría.
4. Agregar tabla `rule_audit` + endpoint + materialización desacoplada vía Spring Events.
5. Construir la suite de regresión y ejecutar el experimento de mantenibilidad.
6. Validación con endocrinólogo + redacción de resultados.

## 📎 Relacionado

- [[Resumen Definición Proyecto Tesis]]
- [[Estado del Arte CDSS GlucoSmart]]
- [[Arquitectura]]
- [[Dominio y Modelo]]
- [[Patrones y Diseño]]
- [[Endpoints API]]
- [[Testing y Load Tests]]
- [[Notas de Desarrollo]]
- [[Mapa de Contenidos]]
