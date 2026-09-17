# Sesión 4 — Streaming e inferencia en tiempo real

Cuarta capa de la plataforma: reaccionar a un **flujo continuo** de eventos (sin principio ni fin) en lugar de responder pedidos sueltos. Incluye el **Hito TP #1** (equipos + arquitectura).

---

## Parte teórica (`Teoria/`)

- **Datos en movimiento:** acotados (batch) vs ilimitados (streaming); datos en reposo (DBMS, *pull*) vs en movimiento (stream, *push*); por qué el valor de muchos datos decae con el tiempo.
- **Almacenar el flujo:** mensajería directa vs *brokers*; **Kafka** (log distribuido, *topics*, particiones, *offsets*), garantías (orden por partición, *at-least-once*), grupos de consumidores.
- **Procesar el flujo:** filtrar, enriquecer, agregar, detectar eventos; *event time* vs *processing time* y *watermarks*; ventanas **tumbling** vs **sliding**; operadores **stateless** vs **stateful**.
- **Streaming en MLOps:** inferencia **online** sobre el flujo, *features* en tiempo real por ventana, monitoreo continuo (throughput, p95, *drift*).

La teoría viene como **notebook-tutorial ejecutable**: `Teoria/streaming_tutorial.ipynb`.
Presentación: `Sesion4_Streaming_2026.pptx` (fuera del repo, junto a los pptx).

## Parte práctica — cómo correr

El curso usa **[uv](https://docs.astral.sh/uv/)**. Preparación (una vez, desde la raíz): `uv sync`. O, para esta sesión:
```bash
uv add scikit-learn joblib numpy kafka-python
```
Para correr los notebooks, registra el kernel de uv (ver *Puesta en marcha* del README raíz).

| Notebook | Carpeta | Qué hace | Cómo correr |
|---|---|---|---|
| `streaming_tutorial.ipynb` | `Teoria/` | **Tutorial de teoría + práctica guiada:** productor/consumidor en memoria (cola + hilos), **inferencia online**, **ventana deslizante** con throughput/p95/**drift**, y el mismo scoring sobre **Kafka/Redpanda en Docker**. Autocontenido (la parte de Kafka se omite sola si no hay broker). | seguir el notebook con el kernel de uv |
| `mini_tp4_actividad.ipynb` | `Practica/` | **Starter del Mini-TP 4** (trabajo individual) con celdas `# TODO` para puntuar *tu* modelo sobre un flujo. | completar y ejecutar el notebook |

**Kafka con Docker (opcional, para el camino de producción):**
```bash
docker run -d --name redpanda -p 9092:9092 \
  redpandadata/redpanda redpanda start --overprovisioned --smp 1 --check=false
# UI/estado: docker logs redpanda   ·   frenar/borrar: docker stop redpanda && docker rm redpanda
```
Redpanda habla la **API de Kafka** y es más liviano para clase; el código con `kafka-python` es idéntico contra un Kafka real.

## Qué se debe entregar

### Mini-TP 4 (individual, esta semana)
Puntúa **tu modelo** (el de la Sesión 1) sobre un flujo:
1. Un **flujo** de eventos con tus *features* (simulado o desde Kafka).
2. Un **consumidor** que puntúe tu modelo sobre **cada** evento (online).
3. **Métricas por ventana:** throughput (ev/s), latencia **p95** y un indicador simple de **drift**.
4. Una **alerta** al cruzar un umbral.
5. **Comparación** contra puntuar el mismo lote en **batch** + reflexión.

Starter: **`mini_tp4_actividad.ipynb`**. **Se evalúa:** que corra de punta a punta; inferencia online; métricas por ventana correctas; reflexión streaming vs batch.

### Hito TP #1 (grupal) — arranca el TP integrador
No es código: es **diseño**. Entregar equipo y roles, caso/dataset y objetivo del servicio, **diagrama de arquitectura de referencia** (Airflow + MLflow + FastAPI + MinIO/S3 sobre Docker), qué capa cubre cada sesión (REST → GraphQL → gRPC → streaming → Data Lake → seguridad) y el plan de hitos hasta la defensa. Próximos hitos: **checkpoint** evaluado en la Sesión 6 y **defensa** en la Sesión 8.
