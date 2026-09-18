# Proyectos anteriores — referencia

Trabajos integradores de **ediciones anteriores** de Operaciones de Aprendizaje Automático II. Sirven como **referencia de alcance**: qué caso eligieron, qué capas del curso integraron y cómo. Míralos como inspiración, no como plantilla a copiar; cada equipo partió de **su propio modelo** y le sumó las capas del bimestre.

> Recordar la idea del integrador: **tu modelo + las capas de MLOps II (REST, GraphQL, gRPC, streaming, cloud/Data Lake, federado, seguridad), bien integradas y justificadas.** No todas las capas tienen que ser “orgánicas”: algunas pueden ser forzadas para fines educativos.

## Cobertura por capa

Según lo declarado en cada repositorio. **✓** = lo integra · **—** = no lo integra / no mencionado. Todos usan la **base** de MLOPs1 (Airflow + MLflow + MinIO + PostgreSQL + Docker), pero no es necesario hacerlo tal cual.

| Proyecto | REST · S1 | GraphQL · S2 | gRPC · S3 | Streaming · S4 | Cloud/Data Lake · S5 | Federado · S6 | Monitoreo/Seguridad · S7 |
|---|:--:|:--:|:--:|:--:|:--:|:--:|:--:|
| G1 · Propiedades inmobiliarias | ✓ | ✓ | ✓ | ✓ (Kafka) | ✓ (MinIO) | — | — |
| G2 · Burn rate de empleados | ✓ | ✓ | ✓ | ✓ (Redis streams) | ✓ (MinIO) | — | — |
| G3 · Clasificación estelar | ✓ | ✓ | ✓ | ✓ (Kafka) | ✓ (MinIO) | — | TODO |
| G4 · Calidad de vino + monitoreo | ✓ | ✓ | ✓ | ✓ (Kafka) | ✓ (MinIO)* | — | ✓ (drift/OOD) |
| G5 · Búsqueda por similitud | ✓ | ✓ | ✓ | ✓ (Kafka) | ✓ (MinIO + pgvector) | — | — |

\* G4 usa MLflow + MinIO pero **no** Airflow (orquesta distinto).

**Dos observaciones útiles:** los **cuatro protocolos** (REST, GraphQL, gRPC, streaming) aparecen en casi todos — es el corazón del integrador. En cambio, **ninguno** usó **aprendizaje federado (S6)**: es una capa que suele quedar “forzada”, y una buena oportunidad para diferenciarse. La dimensión de **seguridad/gobernanza (S7)** solo aparece con fuerza en G4 (monitoreo de drift, poisoning y OOD con alertas).

---

## G1 · Detección y valuación de propiedades inmobiliarias

**Caso / modelo:** valuación de propiedades en Buenos Aires e identificación de oportunidades subvaluadas; datos obtenidos por **web scraping** de portales inmobiliarios. Modelo de regresión **XGBoost**.

**Resumen:** pipeline de punta a punta que parte del scraping de listados, entrena y versiona el modelo, y **sirve la predicción por los cuatro protocolos** para poder comparar REST vs GraphQL vs gRPC. Suma Kafka para analizar listados en tiempo real y funciones serverless (Nuclio) para inferencia escalable.

**Temas del curso que integra:** REST (S1) · GraphQL (S2) · gRPC (S3) · Streaming con Kafka (S4) · MinIO/S3 (S5) · base Airflow + MLflow + Docker. **Extras:** web scraping como ingesta, Nuclio (serverless).

**Repositorio:** https://github.com/denardifabricio/MIA_01c_MLOps2

---

## G2 · Pipeline MLOps — “Burn rate de empleados”

**Caso / modelo:** predicción del **agotamiento/attrition** de empleados a partir de datos de comportamiento y desempeño. Varios modelos (**LightGBM, KNN, SVM**) con optimización de hiperparámetros (Optuna).

**Resumen:** pipeline completo con **ETL**, entrenamiento, exposición por **APIs** y despliegue de infraestructura, bien documentado en la presentación. Desacopla el entrenamiento (Airflow + MLflow) del servicio (FastAPI que consume el Model Registry) y agrega procesamiento asincrónico con **Redis streams** para predicciones en tiempo real.

**Temas del curso que integra:** REST (S1) · GraphQL (S2) · gRPC (S3) · Streaming con Redis streams (S4) · MinIO/S3 como Data Lake (S5) · base Airflow + MLflow + PostgreSQL + Docker.

**Repositorio:** https://github.com/ferkrodriguez98/mlops2_ceia

---

## G3 · API multiprotocolo para clasificación estelar

**Caso / modelo:** clasificación de objetos astronómicos (**galaxias, quásares o estrellas**) con datos del Sloan Digital Sky Survey (Kaggle). Modelo **Random Forest**.

**Resumen:** el ejemplo más claro de “**un mismo modelo, los cuatro protocolos**”: REST para integraciones simples, GraphQL con playground interactivo, gRPC de alto rendimiento (Protocol Buffers) y Kafka para streaming productor–consumidor en tiempo real. Todo orquestado con Airflow y MLflow, con Optuna para tuning y un frontend en React.

**Temas del curso que integra:** REST (S1) · GraphQL (S2) · gRPC (S3) · Streaming con Kafka (S4) · MinIO/S3 (S5) · base Airflow + MLflow + Docker. **Nota:** la seguridad quedó marcada como *TODO* — un buen recordatorio de que S7 suele postergarse.

**Repositorio:** https://github.com/jorgeceferinovaldez/tp_mlops2

---

## G4 · Servidor de inferencia con monitoreo — calidad de vino

**Autores:** Gaspar Acevedo, Jonathan Borda y Carlos Villalobos.
**Caso / modelo:** servicio de inferencia para calidad de vino (Wine Quality) con **detección de anomalías**: *data drift*, *data poisoning* y muestras fuera de distribución (OOD) vía distancia de Mahalanobis. Modelo **Regresión Logística**.

**Resumen:** además de servir el modelo por REST, GraphQL (Strawberry) y gRPC, destaca por la **capa de monitoreo/seguridad de datos**: Kafka con *topics* separados para eventos de inferencia y para **alertas de seguridad**, un detector de drift con Spark y dashboards de alertas en Grafana. Es la mejor referencia para la dimensión de **gobernanza/monitoreo (S7)**.

**Temas del curso que integra:** REST (S1) · GraphQL (S2) · gRPC (S3) · Streaming con Kafka (S4) · MinIO/S3 (S5) · **Monitoreo/seguridad de datos (S7)** · MLflow + Docker. **Nota:** no usa Airflow (orquesta de otra forma). **Extras:** Spark (drift), Grafana (alertas).

**Repositorio:** https://github.com/gasper-az/mlops2-trabajo-final

---

## G5 · Sistema de búsqueda de productos por similitud

**Caso / modelo:** búsqueda **multimodal** (texto e imagen) de productos de moda con embeddings **CLIP-ViT** (fine-tuning).

**Resumen:** evolución de un sistema local a una arquitectura MLOps completa. Descarga el dataset con **DAGs de Airflow**, guarda imágenes en **MinIO**, y almacena los **embeddings en PostgreSQL con pgvector**. Los modelos se sirven por **gRPC** y se exponen por **REST** y **GraphQL**; **Kafka** procesa de forma asincrónica la subida de nuevas imágenes para hacer inferencia en tiempo real. UI en Streamlit.

**Temas del curso que integra:** REST (S1) · GraphQL (S2) · gRPC (S3) · Streaming con Kafka (S4) · MinIO/S3 + **pgvector** como Data Lake / índice vectorial (S5) · base Airflow + MLflow + Docker. **Extras:** embeddings multimodales (CLIP), pgvector, ValKey (cache), Streamlit.

**Repositorio:** https://github.com/AlexBarria/amq2-service-ml

---

*Los enlaces apuntan a repositorios de estudiantes de ediciones anteriores; su contenido y disponibilidad pueden cambiar. La cobertura por capa refleja lo declarado en cada repo al momento de armar esta referencia — conviene revisar cada proyecto para el detalle.*
