---
layout: default
---

[Inicio](index.html) | [Contratos](contratos.html) | [Ejecución](ejecucion.html) | [Costos](costos.html)

# Ejecución de PapaSmart U2

## Entorno original de la demostración

- Jupyter: http://localhost:4489
- Grafana: http://localhost:43000
- Kafka UI: http://localhost:48085
- Proyecto dentro de Jupyter: /home/jovyan/PapaSmart-U2
- Notebook: notebooks/PapaSmart_U2_final.ipynb

No ejecutar Run All: el notebook conserva ejercicios anteriores.
Las salidas guardadas no garantizan que las consultas sigan activas
después de reiniciar Docker o el kernel.

## Instalación en otro equipo

Requisitos: Git, Docker Desktop con contenedores Linux e Internet.

1. Clonar el repositorio y entrar en su carpeta.
2. Copiar .env.example a .env.postgres.
3. Editar .env.postgres y establecer una contraseña propia.
4. Construir e iniciar:

    docker compose -f compose.repro.yml build pyspark
    docker compose -f compose.repro.yml up -d
    docker compose -f compose.repro.yml ps -a

El arranque crea los tópicos papasmart-clima-exterior
y papasmart-almacen, prepara permisos e inicializa las tablas
cuando PostgreSQL utiliza un volumen vacío.

Los servicios crear-topicos y preparar-permisos deben terminar
con Exited (0). Esto es normal.

## Accesos del entorno reproducible

- Jupyter: http://127.0.0.1:4490
- Grafana: http://localhost:43001
- Spark UI: http://localhost:4042, mientras Spark esté activo.

Consultar el token de Jupyter:

    docker compose -f compose.repro.yml exec -T pyspark jupyter server list

Usar el token del servidor nuevo con el puerto 4490.
No publicar tokens ni contraseñas en GitHub.

Abrir notebooks/Arranque_PapaSmart_U2.ipynb.
Este notebook es una recuperación en preparación:
todavía depende de celdas del notebook fuente y NO debe
ejecutarse completo sin revisar.

Grafana del entorno nuevo requiere configurar PostgreSQL
e importar los tableros. No queda configurado automáticamente.

## Estado de verificación

Verificado en el entorno nuevo:
- Contenedores y tópicos.
- PySpark 3.5.0 y procesamiento de prueba.
- Conexión PostgreSQL y creación manual de cinco tablas.
- Permisos de escritura.
- Inicio de las dos consultas de clima sin errores iniciales.

Pendiente de verificar:
- Inicialización SQL automática sobre un volumen vacío.
- Recorrido completo de eventos en el entorno nuevo.
- Predicción, almacén, ventanas y monitor en ese entorno.
- Configuración e importación de Grafana del entorno nuevo.

El pipeline completo se recuperó y verificó en el entorno original
el 6 de noviembre de 2026. Consultar:
https://github.com/yurinscoaquira/PapaSmart-U2/blob/main/evidencias/estado_recuperado_20261106.json

## Persistencia

El entorno nuevo usa volúmenes separados para Kafka,
PostgreSQL, Grafana, Silver y checkpoints.
Los dos entornos no comparten esas bases ni esos checkpoints.

Para detener conservando los datos:

    docker compose -f compose.repro.yml stop

No utilizar down -v si se desean conservar los volúmenes.

## Alcance

Telemetría interior e ingresos de almacén simulados.
Clima exterior procedente de Open-Meteo.
Spark ejecutado en local[2], sin clúster de varias máquinas.
Costos académicos con tarifas hipotéticas.
Envío de correo pendiente de comprobar.
