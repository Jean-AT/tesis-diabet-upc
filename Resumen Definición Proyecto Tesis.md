# Resumen del Proyecto de Tesis — IntegraVida / GlucoSmart

> Nota de uso: este documento resume el proceso de decisiones tomado hasta ahora. Es un documento vivo de seguimiento, no el informe final. El Estado del Arte ya redactado está en un archivo aparte (`Estado_del_Arte_CDSS_GlucoSmart.md`).

## 1. Punto de partida

- Proyecto base: **GlucoSmart / IntegraVida**, sistema ya implementado en el curso de Desarrollo de Aplicaciones Open Source (UPC), con:
  - Backend Spring Boot, arquitectura **DDD + CQRS**, 5 bounded contexts (IAM, Profiles, Patients, Monitoring, Medical)
  - Frontend Angular desplegado
  - Base de datos PostgreSQL
  - Módulos existentes: registro de glucosa, farmacovigilancia, gestión de citas médicas, tratamiento/medicación
- Objetivo inicial: usar este sistema como base para una **tesis de Ingeniería de Software** en la UPC.
- Propuesta original (muy amplia): OCR de glucómetros + modelo predictivo LSTM + arquitectura IoT (BLE/MQTT) + motor de reglas Drools, todo evaluado con Clarke Error Grid y SUS.

## 2. Diagnóstico de la propuesta original

**Problema identificado:** alcance demasiado amplio para una tesis de pregrado (4 componentes de investigación distintos, cada uno tesis-worthy por sí solo).

**Riesgos señalados:**
- No queda claro cuál es *el* aporte principal.
- Falta de fuente de datos real para entrenar el LSTM (no hay dataset propio de series temporales de glucosa; usar datasets públicos como OhioT1DM requeriría trámites/tiempo no disponibles).
- Mezcla de resultados citados de otros papers con resultados propios sin distinguir claramente.
- Faltan objetivos/hipótesis formales, cronograma, alcance explícito de exclusiones.
- Ambigüedad entre modalidad Tesis vs. Trabajo de Suficiencia Profesional (TSP) — pendiente de confirmar con asesor.

## 3. Decisión de alcance: reducir a un solo componente

Se evaluaron los 4 componentes por facilidad de implementación/justificación:

| Componente                    | Datos necesarios                                | Complejidad | Riesgo de no llegar a tiempo |
| ----------------------------- | ----------------------------------------------- | ----------- | ---------------------------- |
| Motor de reglas (Drools/CDSS) | Ninguno externo (reglas clínicas propias)       | Baja-media  | Bajo                         |
| OCR de glucómetro             | Fotos propias                                   | Media       | Medio                        |
| IoT (BLE/MQTT)                | Hardware real o simulado                        | Media-alta  | Medio-alto                   |
| LSTM predictivo               | Series temporales reales (difícil de conseguir) | Alta        | Alto                         |

**Decisión: enfocar la tesis en el Motor de Reglas de Negocio (Drools) como Sistema de Soporte a Decisiones Clínicas (CDSS).**

Razones clave:
- No depende de datos externos difíciles de conseguir.
- Se integra directamente sobre la arquitectura DDD/CQRS ya construida (capa `application` del bounded context Monitoring/Medical).
- Es un problema genuino de ingeniería de software (mantenibilidad, acoplamiento), no solo "cambiar de lenguaje".
- Permite mantener OCR e IoT como trabajo futuro / alcance opcional menor, sin comprometer el cronograma.

## 4. Qué es exactamente el aporte (para no repetir el error de "solo migré if/else")

El aporte **no** es sintáctico. Es la solución a un problema real de **explosión combinatoria de condicionales** cuando crece el número de reglas clínicas (ej. cruces entre glucosa + medicación + síntomas + ventanas temporales + proximidad a citas).

### Ejes de defensa del aporte:
1. **Trazabilidad/explicabilidad clínica**: registrar qué regla se activó, con qué datos y por qué (auditoría de alertas) — no solo generar la alerta.
2. **Experimento empírico de mantenibilidad**: comparar cuantitativamente (antes = if/else vs. después = Drools):
   - Complejidad ciclomática
   - Líneas de código por regla añadida
   - Tiempo real para agregar una regla sin romper las anteriores
   - Tasa de defectos introducidos al modificar
3. **Correlación temporal multi-entidad**: reglas que cruzan eventos de distintos bounded contexts dentro de una ventana de tiempo (ej. mareo reportado dentro de las 2h posteriores a toma de metformina) — caso genuinamente difícil de mantener con SQL ad-hoc o condicionales anidados.

### Formulación de la pregunta de investigación (borrador):
> "¿En qué medida un CDSS basado en motor de reglas (Drools) mejora la mantenibilidad, trazabilidad clínica y capacidad de escalar reglas de farmacovigilancia, en comparación con lógica condicional tradicional, dentro de un sistema real con arquitectura DDD/CQRS?"

## 5. Cómo se integra en la arquitectura existente

```
monitoring/ (o medical/)
├── domain/          (aggregates existentes: GlucoseRecord, Alert, GlucoseRange, MedicationIntake)
├── application/
│   ├── internal/commandservices/   ← aquí se invoca el motor Drools (KieSession)
│   └── rules/                       ← NUEVO
│       ├── hypoglycemia-rules.drl
│       ├── adverse-effects-rules.drl
│       └── DroolsConfig.java
├── infrastructure/
└── interfaces/
```

- Los aggregates y value objects ya existentes en GlucoSmart se reutilizan como "Hechos" (Facts) que Drools consume — no se crea un modelo de datos paralelo.
- Las reglas clínicas se derivan directamente de las entrevistas ya documentadas en el informe base del curso (Jorge, Virgilia, Andy, Dr. Walter Gómez) — trazabilidad directa entre investigación de usuarios y reglas de negocio.

## 6. Estado de la revisión del repositorio UPC (viabilidad)

Se revisaron tesis previas de UPC en el dominio diabetes + tecnología. Hallazgo: **el dominio está muy transitado, pero ningún antecedente usa un motor de reglas declarativo** — todas implementan la lógica de alertas como condicionales embebidos en código o como modelos de predicción estadística (SAP Predictive Analytics, Auto-Classification).

### Tesis identificadas (detalle completo en archivo de Estado del Arte):
1. Ramos Pérez & Monzón Salvador (2014) — Plataforma móvil, monitoreo con reglas de rango en SP
2. Nuñovero Paiva & Rodríguez Acosta (2021) — Predicción de riesgo con SAP Predictive Analytics + wearable
3. Zapata Salazar & Bravo Bustamante (2024) — Wearables IoT, estimación de glucosa vía frecuencia cardiaca
4. Díaz Marcos & Huaman Romero (2020) — Telehomecare (telemedicina + videoconsulta)
5. Ordóñez Barrios & Vizcarra Infantes (2018) — Modelo predictivo con benchmarking de 12 herramientas de Predictive Analytics

**Conclusión de viabilidad:** tema viable, con diferenciación clara respecto a los 5 antecedentes. No hay tesis "hermana" de la que copiar estructura de motor de reglas — habrá que apoyarse en literatura internacional para el marco teórico de CDSS/Drools.

## 7. Entregables ya generados

- [x] `Estado_del_Arte_CDSS_GlucoSmart.md` — sección de estado del arte con tabla comparativa y justificación del vacío de investigación, lista para insertar en el informe (ajustar a plantilla UPC).
- [x] Este resumen de seguimiento.

## 8. Pendientes / próximos pasos sugeridos

- [ ] Confirmar con el asesor/coordinador la modalidad: **Tesis vs. TSP**.
- [ ] Redactar formalmente: objetivo general, objetivos específicos, indicadores de éxito, alcance y exclusiones (siguiendo el molde común de las 5 tesis revisadas: Project Charter, EDT, Student Outcomes ABET).
- [ ] Diseñar el conjunto completo de reglas clínicas de farmacovigilancia basado en las entrevistas ya documentadas (Jorge, Virgilia, Andy, Dr. Walter Gómez) — pendiente de trabajar en detalle.
- [ ] Definir el diseño del experimento de comparación if/else vs. Drools (qué métricas, cuántas reglas, cómo medir tiempo de modificación).
- [ ] Buscar contacto con un endocrinólogo para validación por juicio de experto (patrón repetido en todas las tesis UPC revisadas).
- [ ] Escribir marco teórico de CDSS y motor de reglas (Drools, algoritmo Rete) con literatura internacional, ya que no hay antecedente UPC directo.
- [ ] Elaborar cronograma / plan de trabajo por sprints, siguiendo el patrón visto en las tesis (Inception + Sprints, EDT, RAM, matriz de comunicaciones).
- [ ] Considerar si se mantiene OCR/IoT como "trabajo futuro" mencionado en el documento o se elimina totalmente del alcance.

## 9. Palabras clave útiles para seguir buscando

- Motor de reglas de negocio / business rules engine
- Sistema de soporte a la decisión clínica / CDSS
- Farmacovigilancia digital
- Complejidad ciclomática / mantenibilidad de software
- Domain-Driven Design + reglas de negocio
- Drools / algoritmo Rete

---

## 📎 Relacionado

- [[Propuesta de Tesis - MOC]] — Base de conocimiento de la propuesta reformulada como mejora evolutiva
- [[format&requirments/Formato y Requisitos - MOC]] — Requisitos formales UPC/ABET por completar
- [[format&requirments/Malla Curricular y Ruta de Tesis]] — Plan de evolucion academica desde 5to/6to ciclo
- [[format&requirments/Fuentes Bibliograficas Iniciales]] — Fuentes iniciales agregadas al grafo
- [[Análisis Integración CDSS Drools]] — Cómo llevar esta propuesta al código real
- [[Estado del Arte CDSS GlucoSmart]] — Los 5 antecedentes UPC revisados
- [[IntegraVida - Mapa del Proyecto]]
