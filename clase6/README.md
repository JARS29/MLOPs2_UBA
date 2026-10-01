# Sesión 6 — Nube y Data Lakes para MLOps

Sexta capa de la plataforma: **dónde viven los datos, los artefactos y los modelos**. Cuando el dato en movimiento (REST, GraphQL, gRPC, streaming) y el cómputo distribuido (federado) ya están, falta el **dato en reposo**: un almacenamiento descentralizado, barato y escalable que sostenga a todo lo anterior. Ese lugar es el **Data Lake**.

> **Nota de rediseño.** Esta sesión responde de lleno al pedido recurrente de "más despliegue en la nube". Trae material propio unificado y un **tutorial que corre de punta a punta** con **MinIO** (Data Lake compatible con S3, en local), integrándolo con Airflow/MLflow y con los protocolos ya vistos. Incluye el **Hito TP #2** (checkpoint del integrador, evaluado en clase).

---

## Parte teórica (`Teoria/`)

- **El dato hoy:** los dolores que motivan un lake (calidad, obsolescencia, volumen, escala, costo).
- **Data Warehouse vs Data Lake:** *top-down* vs *bottom-up*; **schema-on-write/ETL** vs **schema-on-read/ELT**; para qué es mejor cada uno.
- **Beneficios del Data Lake para ML:** conserva el dato crudo, admite cualquier formato, storage barato y elástico, un solo origen de verdad para datasets, artefactos y modelos.
- **El riesgo del *data swamp*** y su antídoto: **zonas** (`raw` → `staged` → `curated`), Parquet en lo curado, versionado y gobernanza. El **Lakehouse** como convergencia de lake + warehouse.
- **Lakes en la nube:** Amazon S3, Azure Data Lake Storage, Google Cloud Storage; **MinIO como Data Lake S3 en local**; despliegue gestionado (SageMaker, Vertex AI, Azure ML — conceptual, con foco en *trade-offs* y costos).
- **El Data Lake y los protocolos vistos:** cómo streaming, gRPC, GraphQL y federado se apoyan en el lake.

La teoría viene como **notebook-tutorial ejecutable**: `Teoria/datalake_tutorial.ipynb`.
Presentación: `Sesion6_DataLakes_2026.pptx` (fuera del repo, junto a los pptx).

## Parte práctica — cómo correr

El curso usa **[uv](https://docs.astral.sh/uv/)**. Preparación (una vez, desde la raíz): `uv sync`. O, para esta sesión:
```bash
uv add boto3 pandas pyarrow scikit-learn mlflow joblib
# (para usuarios de Poetry: poetry add boto3 pandas pyarrow scikit-learn mlflow joblib)
```
Además hace falta **Docker** para levantar MinIO:
```bash
docker run -d --name minio \
  -p 9000:9000 \
  -p 9001:9001 \
  -e MINIO_ROOT_USER=minio \
  -e MINIO_ROOT_PASSWORD=minio123 \
  cgr.dev/chainguard/minio:latest \
  server /data --console-address ":9001"
```
En **Windows (PowerShell)** el mismo comando va en una sola línea, sin las barras `\`. La consola web queda en <http://localhost:9001> y la API S3 en <http://localhost:9000>. Para correr los notebooks, registra el kernel de uv (ver *Puesta en marcha* del README raíz).

| Notebook | Carpeta | Qué hace | Cómo correr |
|---|---|---|---|
| `datalake_tutorial.ipynb` | `Teoria/` | **Tutorial de teoría + práctica:** levanta un Data Lake en MinIO, crea zonas (`raw`/`staged`/`curated`/`models`), entrena y **sube dataset y modelo**, registra el experimento con **MLflow → MinIO**, **sirve el modelo cargándolo desde el lake**, y muestra el lake en acción con **streaming → lake** y con **gRPC/GraphQL** leyendo del lake. Autocontenido. | seguir el notebook con el kernel de uv (con MinIO levantado) |
| `mini_tp6_actividad.ipynb` | `Practica/` | **Starter del Mini-TP 6** (trabajo individual) con celdas `# TODO`. | completar y ejecutar el notebook |

**Qué se ve en la práctica:** el mismo `boto3` que corre contra MinIO en tu máquina corre igual contra S3 en la nube (solo cambian endpoint y credenciales); el modelo se **versiona en el lake** (`models/v1/…`) y el servicio lo **descarga al arrancar** en lugar de hornearlo en la imagen; y los eventos de un stream **aterrizan en la zona `raw`** particionados por fecha, dándole memoria durable al stream.

## Qué se debe entregar — Mini-TP 6 (individual, esta semana)

Sube **tu modelo** a un Data Lake (MinIO/S3) y **sírvelo desde allí**:

1. Crea un bucket con **zonas** (`raw`, `curated`, `models`).
2. Sube el artefacto de tu modelo **versionado** (`models/v1/…`).
3. Implementa una función que **cargue el modelo desde el lake** y prediga.
4. *(Opcional)* Registra el experimento con **MLflow apuntando a MinIO/S3**.
5. Responde la reflexión: ¿por qué servir desde el lake y no hornear el modelo en la imagen?

Starter: **`mini_tp6_actividad.ipynb`**. **Se evalúa:** que corra de punta a punta; uso correcto de la API S3 (zonas y versión); que el modelo se cargue desde el lake para servir; y (opcional) la integración con MLflow.

## Conexión con lo anterior

El Data Lake es el **piso común** de toda la plataforma: **Airflow** hace ETL y escribe datasets; **MLflow** registra artefactos y modelos; el **serving** (FastAPI/gRPC) lee el modelo; el **streaming** aterriza eventos; **GraphQL** consulta features y predicciones; y en **federado** el modelo global se versiona en MinIO/MLflow. La próxima sesión (Seguridad, operación y gobernanza) protege todo eso.
