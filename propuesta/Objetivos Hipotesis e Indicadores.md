---
tags: [integravida, tesis, objetivos, hipotesis, indicadores]
created: 2026-08-01
---

# Objetivos Hipotesis e Indicadores

## Pregunta de investigacion borrador

> En que medida un CDSS basado en motor de reglas mejora la mantenibilidad, trazabilidad clinica y capacidad de escalar reglas de farmacovigilancia, en comparacion con logica condicional tradicional, dentro de un sistema real con arquitectura DDD/CQRS?

## Objetivo general borrador

Diseñar e implementar una mejora basada en un motor de reglas de negocio para optimizar la mantenibilidad, trazabilidad y escalabilidad de las reglas clinicas de farmacovigilancia en la plataforma IntegraVida.

## Objetivos especificos borrador

1. Analizar la logica actual de alertas y reglas clinicas en IntegraVida.
2. Diseñar una arquitectura de integracion de Drools compatible con DDD/CQRS y arquitectura hexagonal.
3. Implementar un conjunto de reglas clinicas de farmacovigilancia con trazabilidad de activacion.
4. Evaluar la mantenibilidad de la solucion comparando reglas condicionales contra reglas declarativas.
5. Validar la utilidad y claridad de las alertas generadas mediante juicio de experto.

## Hipotesis borrador

La incorporacion de un motor de reglas de negocio en IntegraVida mejora la mantenibilidad y trazabilidad de las reglas clinicas de farmacovigilancia frente a una implementacion basada en condicionales hardcodeados.

## Indicadores candidatos

| Dimension | Indicador | Metodo |
|---|---|---|
| Mantenibilidad | Complejidad ciclomática | Analisis estatico antes/despues |
| Mantenibilidad | LOC por regla agregada | Conteo por cambio |
| Evolucion | Tiempo de modificacion | Experimento controlado |
| Calidad | Defectos introducidos | Suite de pruebas de regresion |
| Trazabilidad | Porcentaje de alertas con auditoria | Revision funcional |
| Validez clinica | Aceptacion de experto | Encuesta Likert |

## Pendiente

- Ajustar redaccion a la plantilla oficial UPC.
- Alinear objetivos con capitulos y entregables ABET.
- Definir reglas clinicas exactas del experimento.

## Relacionado

- [[Propuesta de Tesis - MOC]]
- [[Problema y Justificacion]]
- [[Aporte de Investigacion]]
- [[format&requirments/ABET - Student Outcomes]]
