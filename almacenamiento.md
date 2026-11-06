---
layout: default
title: Almacenamiento
---

# PapaSmart U2: almacenamiento de papa nativa

**Responsable:** Mijael Yurins Coaquira Justo.

## Objetivo y alcance

Supervisar las condiciones ambientales de almacenamiento por lote,
detectar valores fuera de rangos configurados y visualizar un
pronostico experimental de temperatura y humedad a 30 minutos.

El prototipo utiliza telemetria simulada y un modelo entrenado con
datos exclusivamente sinteticos. El catalogo de 20 variedades
permanece pendiente. No se ha validado el deterioro real del producto.

## Pipeline implementado

| Componente | Funcion |
|---|---|
| Productor Python | Publica ingresos de lotes y telemetria simulada |
| Kafka | Transporta los eventos en papasmart-almacen |
| Spark Structured Streaming | Valida eventos, procesa ventanas y persiste resultados |
| Parquet | Conserva datos procesados |
| PostgreSQL | Almacena eventos y sirve consultas de condiciones y pronosticos |
| Servicio de pronostico | Reutiliza el modelo guardado y actualiza resultados |
| Grafana | Presenta KPIs, graficos, condiciones y metricas operativas |

Spark se ejecuta en local[2]. No se ha verificado un cluster multinodo.

## Eventos empresariales e IoT

El evento empresarial ingreso_lote registra ubicacion, lote,
variedad y peso ingresado.

La telemetria registra temperatura en grados Celsius, humedad
porcentual y luz en lux. Ambos eventos comparten identificador,
ubicacion, lote y fecha del evento.

Una demostracion de 40 eventos publica 2 ingresos y 38 telemetrias.
Es una ejecucion finita con intervalo configurado de un segundo.
Las lecturas no provienen de sensores fisicos.

El peso ingresado representa entradas de producto.
No equivale al inventario disponible, porque no se modelaron salidas.

## Procesamiento y recuperacion

- Ventanas de almacenamiento de un minuto.
- Watermark de dos minutos para las ventanas.
- Checkpoints para recuperar progreso y estado.
- Persistencia de eventos de almacen con identificador unico en PostgreSQL.

La agregacion de ventanas no incorpora una deduplicacion
independiente por evento_id.

Las pruebas guardadas incluyen integracion, eventos tardios y
recuperacion. Su demostracion en vivo forma parte de la sustentacion.

## Tablero BI y observabilidad

El dashboard PapaSmart - Almacenamiento - Yurins incluye:

- Lotes con lectura vigente, sin evaluacion y con lectura no vigente.
- Lotes fuera del rango demostrativo configurado.
- Peso ingresado y cantidad de ingresos en el periodo seleccionado.
- Temperatura y humedad por lote en graficos separados.
- Condiciones actuales y pronostico ambiental por lote.
- Filas de entrada, duracion del trigger y atraso de offsets reportado.

Los KPIs de ultimo estado distinguen lecturas vigentes de antiguas.
El criterio de vigencia utilizado es de 20 minutos.

Los paneles historicos utilizan el rango temporal seleccionado.
Las filas de diferentes consultas Spark no deben sumarse como
eventos unicos, porque un evento puede atravesar varias etapas.

Un atraso cero describe las capturas disponibles.
La ausencia de datos no equivale a atraso cero.

## Modelo y validacion temporal

Horizonte: 30 minutos.

El conjunto contiene 3426 filas y seis lotes sinteticos.
Se utilizaron 2718 filas para entrenamiento y 690 para prueba.
La separacion temporal excluye 18 filas para evitar que los
objetivos de entrenamiento crucen el corte de evaluacion.

El modelo compara regresion lineal contra persistencia.
Utiliza temperatura, humedad, sus cambios recientes y hora UTC ciclica.

| Variable | RMSE persistencia | RMSE regresion | Reduccion RMSE |
|---|---:|---:|---:|
| Temperatura, grados Celsius | 0.2987 | 0.2317 | 22.45 % |
| Humedad, puntos porcentuales | 0.9787 | 0.8463 | 13.53 % |

El guardado y carga del modelo se verificaron.
Los mismos seis lotes aparecen en ambos periodos.
Estas metricas no demuestran generalizacion a variedades o lotes reales.

La inferencia requiere dos lecturas separadas aproximadamente
por diez minutos. Las lecturas demasiado antiguas o futuras
no producen un pronostico apto.

## Condiciones y alertas demostrativas

Las pruebas de software utilizan limites de 7 a 10 grados Celsius
y de 70 a 80 por ciento de humedad.
Estos limites no constituyen recomendaciones agronomicas.

Se verifico la activacion de la alerta actual con una lectura
simulada de 11 grados y su recuperacion con una lectura de 8 grados.

La condicion prevista compara el pronostico con los limites
configurados. Es diferente de la alerta sobre la condicion actual.

La entrega externa de notificaciones no esta verificada.

## Costos y escalado

La estimacion y sus supuestos se encuentran en [Costos](costos.md).

La publicacion finita del simulador debe distinguirse de una
proyeccion de operacion continua. El escalado debe justificarse
con volumen, tamano de eventos, duracion de procesamiento y
atraso sostenido medidos.

## Evidencias y reproducibilidad

Los scripts, contratos, modelo, dashboard exportado y evidencias
se conservan en el repositorio principal PapaSmart-U2.

- Dashboard con KPIs y observabilidad: commit 157f9e3.
- Criterio demostrativo reproducible: commit 7199ef9.
- Prueba de pronostico: prueba_pronostico_74619f464d.json.
- Preparacion de tablas y vistas: preparar_almacenamiento.py.

La prueba indicada produjo aproximadamente 8.40 grados Celsius
y 75.09 por ciento, dentro del rango previsto experimental.
Es un resultado historico de prueba, no una lectura actual.

## Limitaciones y siguiente etapa

Permanecen pendientes el catalogo de 20 variedades, sensores
fisicos, limites con fundamento agronomico y validacion contra
deterioro observado. La inferencia ambiental no demuestra por
si sola prediccion de deterioro.

[Volver al inicio](index.md)
