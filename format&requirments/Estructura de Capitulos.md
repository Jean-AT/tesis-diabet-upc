---
tags: [integravida, tesis, capitulos, formato]
created: 2026-08-01
---

# Estructura de Capitulos

> Borrador de estructura para ordenar la tesis. Debe ajustarse a la plantilla oficial UPC cuando se confirme con el curso/asesor.

## Principio de estructura

La tesis debe contar una evolucion: sistema base existente -> problema de escalabilidad de reglas -> propuesta CDSS -> implementacion de mejora -> evaluacion experimental y validacion.

## Capitulos candidatos

### Capitulo 1 - Introduccion

- Contexto del problema.
- Problematica de diabetes y farmacovigilancia.
- Problema de mantenibilidad de reglas clinicas.
- Objetivo general y objetivos especificos.
- Alcance y limitaciones.

### Capitulo 2 - Marco teorico y estado del arte

- CDSS.
- Motores de reglas de negocio.
- Drools y algoritmo Rete.
- DDD, CQRS y arquitectura hexagonal.
- Trabajos relacionados UPC e internacionales.
- Brecha: antecedentes con diabetes, pero sin motor de reglas declarativo ni auditoria de reglas.

### Capitulo 3 - Analisis del sistema base

- Descripcion de IntegraVida.
- Arquitectura actual.
- Modulos existentes.
- Logica actual de alertas.
- Limitaciones identificadas.

### Capitulo 4 - Propuesta de solucion

- Diseño del modulo CDSS.
- Integracion con bounded contexts.
- Modelo de reglas.
- Modelo de auditoria.
- Seguridad y trazabilidad.

### Capitulo 5 - Implementacion

- Configuracion Drools.
- Reglas implementadas.
- Cambios backend.
- Cambios frontend si aplican.
- Pruebas automatizadas.

### Capitulo 6 - Evaluacion y resultados

- Diseño del experimento.
- Comparacion if/else vs Drools.
- Resultados de metricas.
- Validacion con experto.
- Discusion.

### Capitulo 7 - Conclusiones y trabajo futuro

- Conclusiones por objetivo.
- Limitaciones.
- Trabajo futuro: OCR, IoT, LSTM, reglas editables.

## Alternativa si UPC pide formato articulo

Si el curso pide formato articulo o short paper, compactar asi:

1. Introduccion y problema.
2. Estado del arte.
3. Metodo/propuesta.
4. Implementacion.
5. Evaluacion y resultados.
6. Conclusiones.

## Pendiente critico

- Conseguir plantilla oficial vigente del curso o asesor.
- Conseguir rubrica oficial de evaluacion.
- Confirmar si se exige tesis larga, articulo, Capstone Project, TSP o trabajo de investigacion.

## Relacionado

- [[Formato y Requisitos - MOC]]
- [[Propuesta de Tesis - MOC]]
- [[Roadmap de Mejora CDSS]]
- [[Estado del Arte CDSS GlucoSmart]]
- [[Rubrica de Evaluacion - Pendiente Oficial]]
