---
tags: [integravida, tesis, problema, justificacion]
created: 2026-08-01
---

# Problema y Justificacion

## Problema central

La version actual de IntegraVida genera alertas principalmente mediante comparaciones directas de glucosa contra rangos configurados. Esta aproximacion funciona para reglas simples, pero se vuelve dificil de mantener cuando las reglas clinicas empiezan a combinar multiples eventos y entidades.

Ejemplos de combinaciones que generan complejidad:

- Glucosa baja + toma reciente de medicamento.
- Sintoma reportado + medicacion activa.
- Valores fuera de rango + proximidad a cita medica.
- Multiples registros anormales dentro de una ventana temporal.
- Priorizacion de severidad segun contexto clinico.

## Problema de Ingenieria de Software

El problema no es solo clinico. Es un problema de arquitectura y mantenibilidad:

- Las reglas hardcodeadas aumentan la complejidad ciclomática.
- Agregar una nueva regla puede afectar reglas anteriores.
- No existe trazabilidad clara de por que se genero una alerta.
- Las reglas quedan mezcladas con servicios de aplicacion o logica de dominio.
- El conocimiento clinico no esta representado como artefacto independiente.

## Justificacion academica

El estado del arte UPC muestra que existen trabajos previos sobre diabetes, telemedicina, prediccion y monitoreo, pero no se identifico una tesis que aborde la gestion declarativa, auditable y mantenible de reglas clinicas mediante un motor de reglas.

Por ello, la tesis se diferencia al enfocarse en la capa de decision clinica y en medir la mejora desde atributos de calidad de software.

## Justificacion practica

En un sistema de salud digital, las reglas clinicas cambian con el tiempo. Si el sistema crece, las alertas no pueden depender solamente de condicionales dispersos en el codigo. Un motor de reglas permite representar, probar y auditar esas decisiones de forma mas clara.

## Relacionado

- [[Propuesta de Tesis - MOC]]
- [[Estado del Arte CDSS GlucoSmart]]
- [[Análisis Integración CDSS Drools]]
- [[Objetivos Hipotesis e Indicadores]]
