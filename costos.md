---
layout: default
---

[Inicio](index.html) | [Contratos](contratos.html) | [Ejecución](ejecucion.html) | [Costos](costos.html)

# Volumen, costos y criterios de escalado

## Medición del proyecto

Se verificaron 240 eventos del almacén simulado:
12 ingresos de lotes y 228 registros de telemetría.

Almacenamiento observado:
- Bronze: 0.268 MiB.
- Silver: 0.542 MiB.
- Modelos: 0.004 MiB.
- Checkpoints: 0.223 MiB.
- Base PostgreSQL completa: aproximadamente 8 MB.

Estas medidas corresponden al estado de la prueba, no a un mes de operación.

## Proyección mensual

Se proyectó operación continua durante 30 días, conservando
la proporción y el tamaño promedio de los registros observados.

| Eventos/s | Eventos/mes | JSON nuevo GB/mes | Total parcial USD/mes |
|---:|---:|---:|---:|
| 1 | 2,592,000 | 1.015 | 72.0117 |
| 10 | 25,920,000 | 10.146 | 72.1167 |
| 100 | 259,200,000 | 101.464 | 73.1668 |

GB corresponde a 1,000,000,000 bytes.

## Supuestos de costos

Se utilizaron tarifas hipotéticas para un ejercicio académico:
- Cómputo: USD 0.10 por hora, durante 720 horas.
- Almacenamiento: USD 0.023 por GB al mes.
- Primer mes inicialmente vacío, con crecimiento uniforme.
- Almacenamiento promedio del primer mes: la mitad del volumen final.

No son precios comerciales verificados ni una factura.
El costo de cómputo permanece fijo para comparar escenarios;
no demuestra capacidad para soportar todas las tasas.

Se excluyen PostgreSQL e índices, Parquet y otras copias,
Kafka, réplicas, checkpoints, red, respaldos, impuestos
y consumo eléctrico del equipo local.

## Criterios para aumentar recursos

1. Investigar retraso Kafka superior a 30 offsets durante 2 minutos.
2. Si los microlotes alcanzan repetidamente su intervalo de ejecución,
   revisar consultas, recursos y acumulación de eventos.
3. Medir CPU, memoria y espacio disponible antes de aumentar recursos.
4. Revisar las particiones de Kafka para aumentar lectores en paralelo:
   el tópico utilizado tiene una sola partición.
5. Ejecutar pruebas de carga para verificar cada escenario.

Las tasas de 10 y 100 eventos/s son proyecciones, no capacidad validada.
La publicación de almacén se probó con 120 eventos a un intervalo
configurado de un segundo; no se realizó una prueba mensual continua.

## Evidencias

- https://github.com/yurinscoaquira/PapaSmart-U2/blob/main/evidencias/volumen_y_escalado.json
- https://github.com/yurinscoaquira/PapaSmart-U2/blob/main/evidencias/costos_mensuales_proyectados.json
