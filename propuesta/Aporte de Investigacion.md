---
tags: [integravida, tesis, aporte, investigacion]
created: 2026-08-01
---

# Aporte de Investigacion

## Aporte principal

El aporte principal es diseñar e implementar una capa CDSS basada en Drools para gestionar reglas clinicas de farmacovigilancia dentro de IntegraVida, mejorando la mantenibilidad, trazabilidad y escalabilidad de la logica de alertas.

## Ejes del aporte

### 1. Escalabilidad de reglas clinicas

El sistema deja de depender exclusivamente de condicionales hardcodeados y pasa a representar reglas clinicas como artefactos declarativos.

### 2. Trazabilidad y explicabilidad

Cada alerta debe poder responder:

- Que regla se activo.
- Que datos participaron.
- Cuando se activo.
- Que severidad genero.
- Cual fue la razon clinica o de negocio.

### 3. Mantenibilidad medible

La tesis debe comparar la aproximacion tradicional contra la aproximacion con motor de reglas usando indicadores como:

- Complejidad ciclomática.
- Lineas de codigo por regla.
- Tiempo de modificacion.
- Defectos introducidos al agregar o cambiar reglas.
- Cobertura o regresion de pruebas sobre reglas.

### 4. Correlacion temporal multi-entidad

El caso estrella es evaluar reglas que crucen informacion de distintos contextos:

- Glucosa.
- Medicacion.
- Tomas de medicamentos.
- Sintomas u observaciones clinicas.
- Citas medicas.

## Diferenciacion frente a antecedentes

Los antecedentes UPC revisados se enfocan en monitoreo, telemedicina, wearables o modelos predictivos. Esta tesis se ubica en otra capa: la representacion y ejecucion mantenible del conocimiento clinico.

## Relacionado

- [[Propuesta de Tesis - MOC]]
- [[Problema y Justificacion]]
- [[Estado del Arte CDSS GlucoSmart]]
- [[Roadmap de Mejora CDSS]]
