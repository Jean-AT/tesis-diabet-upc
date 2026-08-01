---
tags: [integravida, tesis, roadmap, cdss, drools]
created: 2026-08-01
---

# Roadmap de Mejora CDSS

## Fase 1 - Base tecnica

- Agregar dependencias Drools al backend.
- Crear configuracion inicial de `KieContainer` y `KieSession`.
- Probar una regla minima de integracion.

## Fase 2 - Migracion controlada de regla existente

- Replicar con Drools la clasificacion actual de glucosa fuera de rango.
- Mantener el comportamiento funcional equivalente.
- Crear pruebas de regresion para asegurar que no se rompe la alerta actual.

## Fase 3 - Reglas clinicas multi-entidad

- Incorporar hechos de glucosa, medicacion, tomas y observaciones.
- Implementar reglas con ventanas temporales.
- Priorizar el caso de farmacovigilancia: sintoma posterior a toma de medicamento.

## Fase 4 - Auditoria y explicabilidad

- Crear modelo de auditoria de activacion de reglas.
- Persistir regla activada, severidad, paciente, hechos involucrados y timestamp.
- Exponer endpoint de consulta de auditoria.

## Fase 5 - Evaluacion de mantenibilidad

- Definir version base con condicionales.
- Definir version mejorada con Drools.
- Medir complejidad, LOC, tiempo de cambio y defectos.
- Documentar resultados.

## Fase 6 - Validacion academica y clinica

- Preparar encuesta de juicio de experto.
- Validar claridad y utilidad de alertas.
- Vincular resultados con objetivos, hipotesis y ABET.

## Relacionado

- [[Propuesta de Tesis - MOC]]
- [[Análisis Integración CDSS Drools]]
- [[Testing y Load Tests]]
- [[format&requirments/Estructura de Capitulos]]
