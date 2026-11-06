---
layout: default
---

[Inicio](index.html) | [Contratos](contratos.html) | [Ejecución](ejecucion.html) | [Costos](costos.html)

# Contratos de eventos de PapaSmart U2

## Transporte

- Formato: JSON codificado en UTF-8.
- Kafka interno: kafka:9092.
- Topicos: papasmart-almacen y papasmart-clima-exterior.
- Configuracion del laboratorio: una particion y replicacion 1.
- Productores del almacen: complemento/scripts/almacen.py.
- Productores del clima: carpeta productores/.

## Evento empresarial: ingreso_lote

Topico: papasmart-almacen.

Campos obligatorios:
- evento_id: texto identificador del evento.
- tipo: ingreso_lote.
- fuente: simulador_almacen.
- simulado: true.
- ubicacion: texto identificador del almacen.
- lote_id: texto identificador del lote.
- fecha_evento: fecha y hora ISO 8601 con zona horaria.
- peso_kg: numero mayor que cero.
- variedad: texto.

El procesamiento acepta pesos entre 0.001 y 100000 kg.
Representa una entrada de producto; no equivale al inventario
disponible, porque no se modelaron salidas.

## Evento IoT: telemetria

Topico: papasmart-almacen.

Comparte evento_id, fuente, simulado, ubicacion, lote_id
y fecha_evento con el evento empresarial.

Campos especificos:
- tipo: telemetria.
- temperatura_c: numero entre -50 y 60.
- humedad_pct: numero entre 0 y 100.
- luz_lux: numero entre 0 y 200000.

Son lecturas SIMULADAS, no mediciones de sensores fisicos.

## Frecuencia y volumen de la demostracion

Comando utilizado:

    python complemento/scripts/almacen.py --cantidad 120 --semilla 11 --publicar --intervalo 1

Publica 120 eventos con un intervalo configurado de un segundo:
6 ingresos de lotes y 114 telemetrias.
Es una ejecucion finita; no un servicio continuo.

## Evento meteorologico exterior

Topico: papasmart-clima-exterior.

Campos:
- evento_id, fuente, tipo_dato y ubicacion: texto.
- fecha_dato_local: fecha y hora local de America/Lima.
- ingestado_utc: fecha y hora de ingesta en UTC.
- temperatura_c: numero.
- humedad_relativa_pct: numero entre 0 y 100.
- precipitacion_mm: numero mayor o igual que cero.

Fuente: estimaciones meteorologicas de Open-Meteo para
una referencia aproximada de Azangaro.
El productor consulta cada 10 minutos y publica cuando detecta cambios.
La consulta periodica no garantiza un evento nuevo en cada intento.

## Validacion y persistencia

- Spark valida los campos antes de almacenar.
- El almacen separa aceptados y rechazados en Parquet.
- PostgreSQL evita repetir eventos de almacen por evento_id.
- El clima utiliza deduplicacion con watermark de dos horas.
- Las ventanas del almacen son de un minuto, con watermark
  de dos minutos.
- La agregacion de ventanas no incorpora una deduplicacion
  independiente por evento_id.
- Los checkpoints permiten recuperar offsets y estado.

## Evidencias

- [Prueba de watermark](https://github.com/yurinscoaquira/PapaSmart-U2/blob/main/evidencias/prueba_watermark_f6f4cea3.json)
- [Recuperacion de checkpoint](https://github.com/yurinscoaquira/PapaSmart-U2/blob/main/evidencias/checkpoint_prueba_watermark_f6f4cea3.json)
- [Estado recuperado](https://github.com/yurinscoaquira/PapaSmart-U2/blob/main/evidencias/estado_recuperado_20261106.json)
