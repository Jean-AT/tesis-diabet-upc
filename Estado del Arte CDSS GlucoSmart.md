# Estado del Arte: Soluciones Tecnológicas para Diabetes Mellitus en el Repositorio Académico UPC

## 1. Introducción

Con el propósito de validar la viabilidad y el aporte diferencial de la presente propuesta de tesis —el diseño e implementación de un módulo de soporte a decisiones clínicas (CDSS) basado en un motor de reglas de negocio (Drools) integrado a la arquitectura DDD/CQRS del sistema GlucoSmart—, se realizó una revisión sistemática de los trabajos de investigación previos desarrollados en la Universidad Peruana de Ciencias Aplicadas (UPC) que abordan la problemática de la Diabetes Mellitus desde una perspectiva de Ingeniería de Software y Sistemas de Información. Esta revisión permite, por un lado, confirmar que el dominio de aplicación (diabetes y salud digital) cuenta con tradición y aceptación dentro de la Facultad de Ingeniería, y por otro, delimitar con precisión el vacío de investigación que la presente propuesta busca cubrir.

Se identificaron cinco tesis previas, desarrolladas entre 2014 y 2024, cuyo análisis comparativo se presenta a continuación.

## 2. Trabajos previos identificados

### 2.1 Plataforma móvil para tratamiento preventivo y monitoreo de pacientes con enfermedades crónicas (Ramos Pérez & Monzón Salvador, 2014)

Desarrollada en la Facultad de Ingeniería Electrónica (Carrera de Ingeniería de Telecomunicaciones y Redes), esta tesis propone una arquitectura cliente-servidor de tres capas para el monitoreo de pacientes con Diabetes Mellitus Tipo 1, con roles diferenciados de paciente, médico, familiar y administrador. El sistema permite el registro manual de niveles de glucosa desde una aplicación Android y valida dichos valores contra un rango configurado por el médico, generando notificaciones push (Google Cloud Messaging) cuando el valor se encuentra fuera de rango.

La lógica de alertas descrita en el documento se implementa mediante procedimientos almacenados (`sp_ValidarRangoGluc`) que ejecutan comparaciones condicionales directas entre el valor ingresado y los límites almacenados, sin que exista una capa de reglas de negocio desacoplada del código de la aplicación.

### 2.2 Solución tecnológica para identificar el nivel de predisposición a ser diagnosticado con Diabetes Mellitus Tipo 2 (Nuñovero Paiva & Rodríguez Acosta, 2021)

Este trabajo se centra en la prevención primaria mediante un modelo de análisis predictivo (algoritmo *Auto-Classification* de SAP Predictive Analytics) que, a partir de un cuestionario de factores de riesgo no invasivos, estima el porcentaje de predisposición de una persona a desarrollar DMT2. La solución se complementa con el monitoreo de actividad física mediante un dispositivo wearable, validándose con una muestra de 660 pacientes en una institución pública de salud en Lima.

El foco de esta tesis es el diagnóstico presuntivo *antes* de la aparición de la enfermedad, no el acompañamiento clínico de pacientes ya diagnosticados, y su núcleo técnico es un modelo estadístico de clasificación automática, no un sistema de reglas de negocio editable por un experto de dominio.

### 2.3 Solución tecnológica para controlar la Diabetes Tipo 1 y Tipo 2 en personas de 55 a 64 años usando wearables basados en IoT (Zapata Salazar & Bravo Bustamante, 2024)

Tesis desarrollada en el Programa Académico de Ingeniería de Sistemas de Información, publicada también como artículo en ARTIIS 2023 (Springer). Propone la estimación no invasiva de glucosa a partir de la frecuencia cardiaca capturada por un smartwatch, mediante un modelo de conversión matemática (frecuencia cardiaca → hemoglobina → glucosa estimada) y arquitectura en la nube (Azure). La validación se realizó con 15 pacientes y 5 endocrinólogos mediante encuestas de satisfacción basadas en la escala de Likert y el coeficiente Alfa de Cronbach.

Al igual que en los casos anteriores, la lógica de generación de alertas (frecuencia cardiaca fuera de rango) no se desacopla en un motor de reglas independiente; se limita a comparaciones de rango directamente en el código de la aplicación.

### 2.4 Solución de Telehomecare que brinda atención médica para adultos mayores diagnosticados con Diabetes Mellitus tipo 2 (Díaz Marcos & Huaman Romero, 2020)

Este trabajo aborda la problemática desde la telemedicina, integrando una aplicación móvil de autocontrol para el paciente con una plataforma web para el endocrinólogo, incluyendo videoconsultas periódicas. Se apoya en la arquitectura analítica de Big Data en salud propuesta por Wang, Kung y Byrd (2016). La validación se realizó con un caso de estudio de tres pacientes adultos mayores y un médico endocrinólogo, midiendo constancia de registro, tiempo de respuesta médica y reducción de complicaciones (14%).

El aporte de esta tesis es arquitectónico y de proceso asistencial (telemedicina), no de modelado del conocimiento clínico: las decisiones de intervención siguen recayendo enteramente en el criterio manual del médico durante la videoconsulta, sin ningún componente de inferencia automatizada.

### 2.5 Modelo Predictivo para el diagnóstico de la Diabetes Mellitus Tipo 2 soportado por SAP Predictive Analytics (Ordóñez Barrios & Vizcarra Infantes, 2018)

Precedente directo del trabajo de 2021 (sección 2.2), desarrollado también en Ingeniería de Sistemas de Información. Realiza un benchmarking exhaustivo de 12 herramientas de *Predictive Analytics* (Angoss, Alpine Data, Alteryx, Dell Statistica, FICO, IBM, KNIME, Microsoft Azure ML, Predixion, RapidMiner, SAP, SAS) bajo criterios de Forrester, seleccionando SAP Predictive Analytics como soporte del modelo. Evalúa además tres familias de algoritmos (árboles de decisión, regresión, redes neuronales/clasificación) sobre datos recopilados mediante encuesta propia (656 pacientes), obteniendo un 91.7% de precisión con el algoritmo *Auto Classification*.

Es, de las cinco tesis revisadas, la más rigurosa en su tratamiento metodológico de algoritmos, pero su objetivo es el diagnóstico probabilístico de la enfermedad a partir de datos estáticos de una encuesta, no la gestión dinámica de reglas clínicas de farmacovigilancia sobre datos longitudinales de un paciente en tratamiento.

## 3. Análisis comparativo

| Dimensión | Ramos & Monzón (2014) | Nuñovero & Rodríguez (2021) | Zapata & Bravo (2024) | Díaz & Huaman (2020) | Ordóñez & Vizcarra (2018) | **Propuesta actual** |
|---|---|---|---|---|---|---|
| Enfoque principal | Monitoreo remoto | Predicción de riesgo | Monitoreo IoT no invasivo | Telemedicina | Diagnóstico predictivo | **Soporte a decisión clínica (CDSS)** |
| Momento de intervención | Paciente diagnosticado | Pre-diagnóstico | Paciente diagnosticado | Paciente diagnosticado | Pre-diagnóstico | Paciente en tratamiento activo |
| Núcleo técnico | Comparación condicional en BD | ML estadístico (Auto-Classification) | Estimación matemática + IoT | Videollamada + reglas de rango | ML estadístico (Auto-Classification) | **Motor de reglas declarativo (Drools / algoritmo Rete)** |
| Lógica de negocio desacoplada del código | No | No | No | No | No | **Sí** |
| Trazabilidad/explicabilidad de la alerta | No reportada | No aplica | No reportada | No reportada | No aplica | **Sí (auditoría de reglas activadas)** |
| Correlación de eventos multi-entidad (glucosa + medicación + síntomas) | No | No | No | No | No | **Sí** |
| Métrica de mantenibilidad del software evaluada | No | No | No | No | No | **Sí (complejidad ciclomática, tiempo de cambio)** |
| Arquitectura base | Cliente-servidor 3 capas | Cloud (Azure) | Cloud (Azure) | Big Data analítico | Cloud/on-premise | DDD + CQRS (GlucoSmart) |
| Validación con expertos | No reportada | No reportada | 5 endocrinólogos (Likert) | 1 endocrinólogo | No reportada | Por definir (endocrinólogo(s)) |

## 4. Identificación del vacío de investigación

De la revisión anterior se desprenden dos conclusiones que sustentan la viabilidad y pertinencia de la propuesta:

**Primero**, el dominio de aplicación —soluciones tecnológicas para el control y diagnóstico de la Diabetes Mellitus— cuenta con amplio precedente y aceptación en la Facultad de Ingeniería de la UPC, lo que reduce el riesgo de rechazo del tema por parte de un comité evaluador.

**Segundo**, y de manera más relevante, **ninguna de las cinco tesis identificadas aborda el problema de la gestión del conocimiento clínico como un problema de ingeniería de software independiente del algoritmo de predicción o del dispositivo de captura de datos**. En los cinco casos, la lógica que determina cuándo se genera una alerta, qué combinación de factores constituye un riesgo, o cómo deben correlacionarse eventos de distintas fuentes (registro de glucosa, toma de medicación, síntomas reportados) se encuentra *hardcodeada* dentro de la capa de aplicación —ya sea como comparaciones condicionales en procedimientos almacenados (Ramos & Monzón, 2014; Zapata & Bravo, 2024) o como una capa de predicción estadística de caja relativamente cerrada (Nuñovero & Rodríguez, 2021; Ordóñez & Vizcarra, 2018)—, sin que exista una capa de reglas de negocio declarativa, editable y auditable de forma independiente del ciclo de despliegue del software.

Esta ausencia es significativa porque, conforme el número de reglas clínicas de farmacovigilancia crece (interacciones medicamento-síntoma, ventanas temporales de correlación, priorización de alertas según proximidad a citas médicas, niveles de severidad diferenciados), la lógica condicional tradicional documentada en estos antecedentes enfrentaría una explosión combinatoria de mantenibilidad que ninguno de los trabajos previos mide ni aborda.

La presente propuesta no compite, por tanto, con los enfoques de predicción estadística (Nuñovero & Rodríguez, 2021; Ordóñez & Vizcarra, 2018) ni con los de captura de datos vía wearables (Zapata & Bravo, 2024), sino que se ubica en una capa distinta y complementaria del mismo dominio: la de la representación, ejecución y mantenibilidad del conocimiento clínico que actúa sobre los datos ya capturados, aportando además —a diferencia de los cinco antecedentes revisados— una validación cuantitativa explícita de la calidad interna del software (complejidad ciclomática, esfuerzo de modificación) como parte de los resultados experimentales de la tesis.

## 5. Referencias

- Ramos Pérez, M. R., & Monzón Salvador, C. A. (2014). *Plataforma móvil para tratamiento preventivo y monitoreo de pacientes con enfermedades crónicas* [Tesis de pregrado, Universidad Peruana de Ciencias Aplicadas].
- Nuñovero Paiva, D. A., & Rodríguez Acosta, E. S. (2021). *Solución tecnológica para identificar el nivel de predisposición a ser diagnosticado con Diabetes Mellitus Tipo 2* [Tesis de pregrado, Universidad Peruana de Ciencias Aplicadas].
- Zapata Salazar, D. A., & Bravo Bustamante, S. de F. (2024). *Solución tecnológica para controlar la diabetes Tipo 1 y Tipo 2 en personas de 55 a 64 años usando wearables basados en IoT en clínicas particulares de Lima Metropolitana* [Tesis de pregrado, Universidad Peruana de Ciencias Aplicadas].
- Díaz Marcos, D. V., & Huaman Romero, J. (2020). *Solución de Telehomecare que brinda atención médica para adultos mayores diagnosticados con Diabetes Mellitus tipo 2* [Tesis de pregrado, Universidad Peruana de Ciencias Aplicadas].
- Ordóñez Barrios, D. A., & Vizcarra Infantes, E. R. (2018). *Modelo Predictivo para el diagnóstico de la Diabetes Mellitus Tipo 2 soportado por SAP Predictive Analytics* [Tesis de pregrado, Universidad Peruana de Ciencias Aplicadas].

---

## 📎 Relacionado

- [[Resumen Definición Proyecto Tesis]] — Decisión de alcance que sustenta este estado del arte
- [[Análisis Integración CDSS Drools]] — Aplicación de la propuesta al código real
- [[IntegraVida - Mapa del Proyecto]]
