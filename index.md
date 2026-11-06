---
layout: default
---

[Inicio](index.html) | [Contratos](contratos.html) | [Ejecución](ejecucion.html) | [Costos](costos.html)

# PapaSmart U2

Pipeline académico para integrar eventos de almacén y telemetría
simulada mediante Kafka y Spark Structured Streaming.

Los resultados se almacenan en PostgreSQL y se visualizan en Grafana,
junto con una predicción de temperatura exterior a una hora.

## Documentación

- [Contratos de eventos empresariales e IoT](contratos.html)
- [Guía de ejecución y estado de reproducibilidad](ejecucion.html)
- [Costos y criterios de escalado](costos.html)

## Alcance

Los ingresos de lotes y sensores interiores son simulados.
El clima exterior procede de Open-Meteo.
Spark se ejecuta en modo local con dos hilos.
La reproducibilidad completa en un entorno nuevo sigue en verificación.

## Código y evidencias

El repositorio del proyecto es privado y requiere acceso autorizado:
[PapaSmart-U2](https://github.com/yurinscoaquira/PapaSmart-U2).
