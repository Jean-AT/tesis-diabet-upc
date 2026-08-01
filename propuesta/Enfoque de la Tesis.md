---
tags: [integravida, tesis, enfoque, cdss, drools]
created: 2026-08-01
---

# Enfoque de la Tesis

## Tesis como mejora evolutiva

IntegraVida ya existe como plataforma desplegada para gestion integral de pacientes con diabetes. Por ello, la tesis no debe plantearse como si el aporte fuera construir toda la aplicacion desde cero.

El enfoque correcto es presentar la tesis como una **mejora evolutiva de un sistema existente**, orientada a convertir la logica de alertas clinicas en un modulo CDSS basado en reglas declarativas.

La distincion clave es esta:

- No se hara la implementacion integral de IntegraVida como producto nuevo.
- Si se hara la implementacion de una mejora especifica sobre IntegraVida: el modulo CDSS basado en Drools y su evaluacion.

En otras palabras, el sistema base es el contexto; la mejora es la tesis.

## Formulacion corta

> Sobre una plataforma web ya implementada para el monitoreo y seguimiento de pacientes con diabetes, se diseña e implementa una mejora basada en un motor de reglas de negocio para incrementar la mantenibilidad, trazabilidad y escalabilidad de las reglas clinicas de farmacovigilancia.

## Que no estamos diciendo

- No se afirma que la tesis sea todo IntegraVida.
- No se defiende la tesis como una simple aplicacion CRUD de salud.
- No se plantea como una reconstruccion completa del backend o frontend.
- No se promete OCR, IoT o LSTM como parte central del alcance.

## Que si estamos diciendo

- IntegraVida es el caso de estudio real y desplegado.
- El problema esta en la escalabilidad de la logica clinica.
- La mejora propuesta es un CDSS con Drools.
- El aporte se evalua con criterios de Ingenieria de Software: mantenibilidad, complejidad, trazabilidad y capacidad de evolucion.

## Frase de defensa ante jurado

> La plataforma base ya existia; mi tesis se enfoca en evolucionarla para resolver un problema concreto de escalabilidad de reglas clinicas. El aporte no es la existencia de la app, sino la incorporacion de una capa de decision clinica declarativa, auditable y medible sobre una arquitectura DDD/CQRS real.

## Relacionado

- [[Propuesta de Tesis - MOC]]
- [[Resumen Definición Proyecto Tesis]]
- [[Aporte de Investigacion]]
- [[Alcance y Exclusiones]]
