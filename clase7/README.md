# Sesión 7 — Seguridad, operación y gobernanza

Última capa de la plataforma: **proteger, operar y gobernar** todo lo construido. Cada sesión sumó una capacidad (servir, streaming, federado, data lake) y, con ella, una **superficie de ataque**. Aquí la aseguramos de punta a punta usando **SAIF** (Secure AI Framework) como marco.

> **Nota de rediseño.** Esta sesión responde al pedido explícito de "gobernanza, operación y CI/CD y seguridad real". Trae material propio unificado, un **notebook integrador** que endurece un servicio capa por capa y **seis notebooks guiados** (uno por protocolo visto) que corren de punta a punta sin servicios externos. Es el **último Mini-TP individual**: cierra el recorrido de los Mini-TPs 1–7.

---

## Parte teórica (`Teoria/`)

- **Por qué importa:** el dilema oportunidad vs. riesgo; qué está en juego (fuga de datos, decisiones manipuladas, robo del modelo, incumplimiento).
- **Conocer el sistema = asegurarlo:** la superficie de ataque (API, ingesta/almacenamiento, modelo, entrenamiento) y las **amenazas específicas de IA** (envenenamiento, evasión adversaria, extracción/robo, inversión/fuga de privacidad, manipulación del artefacto, abuso/inyección).
- **SAIF:** sus **6 elementos** y su **mapa de riesgos** (componentes × riesgos × controles).
- **Defensa en profundidad:** las capas de seguridad sobre nuestra plataforma — autenticación/autorización, validación y *rate limiting*, secretos, datos y modelo, entrenamiento robusto, y operación/gobernanza.
- **SAIF por cada capa vista:** el riesgo y el control concreto de REST, GraphQL, gRPC, streaming, federado y data lake.
- **Operación** (observabilidad, *drift*, SLAs, alertas) y **gobernanza** (model cards, auditoría, cumplimiento, ética/XAI); **introducción a CI/CD para ML**.

La teoría viene como **notebook-tutorial ejecutable**: `Teoria/seguridad_tutorial.ipynb`.
Presentación: `Sesion7_Seguridad_2026.pptx` (fuera del repo, junto a los pptx).

## Parte práctica — cómo correr

El curso usa **[uv](https://docs.astral.sh/uv/)**. Preparación (una vez, desde la raíz): `uv sync`. O, para esta sesión:
```bash
uv add fastapi httpx pyjwt pydantic scikit-learn numpy joblib
uv add strawberry-graphql grpcio grpcio-tools boto3   # según el notebook que elijas
# (para usuarios de Poetry: poetry add <los mismos paquetes>)
```
Solo el notebook de **data lake** necesita **MinIO** levantado con Docker (ver *Guía de la práctica* o el README de la Sesión 6). Los demás corren **en proceso**, sin servicios externos. Para correr los notebooks, registra el kernel de uv (ver *Puesta en marcha* del README raíz).

### Notebook integrador (teoría)

| Notebook | Carpeta | Qué hace |
|---|---|---|
| `seguridad_tutorial.ipynb` | `Teoria/` | Toma un modelo servido como API y lo **endurece capa por capa**: autenticación (API key + JWT), validación con Pydantic, *rate limiting*, secretos, **integridad por checksum**, **ataque adversario y su mitigación** (adversarial training), **logging de auditoría** y **model card**. Cada control se **mapea a un elemento de SAIF**. |

### Mini-TP 7 — un notebook guiado por capa (elige uno)

| Notebook | Capa | Estrategia SAIF que implementa |
|---|---|---|
| `mini_tp7_rest_actividad.ipynb` | REST | auth (API key/JWT) + validación + *rate limiting* |
| `mini_tp7_graphql_actividad.ipynb` | GraphQL | límite de profundidad + sin introspección + authz por campo |
| `mini_tp7_grpc_actividad.ipynb` | gRPC | interceptor de token en *metadata* + límite de tamaño (+ TLS conceptual) |
| `mini_tp7_streaming_actividad.ipynb` | Streaming | validación de esquema + cuarentena (DLQ) + firma HMAC del productor |
| `mini_tp7_federado_actividad.ipynb` | Federado | agregación robusta (mediana) + *clipping* + privacidad diferencial |
| `mini_tp7_datalake_actividad.ipynb` | Data Lake | acceso mínimo + versionado/checksum + URLs prefirmadas |

Cada notebook sigue la misma estructura: **versión vulnerable → control principal (guiado) → `# TODO` sobre tu modelo → reflexión**.

**Qué se ve en la práctica (corridas de referencia):** el servicio sin auth responde a cualquiera; con los controles aparece 401/422/429 según corresponda. Un **ataque adversario** baja la accuracy del modelo base de ~0.99 a ~0.64, y el modelo con *adversarial training* la sostiene en ~0.95. En federado, un solo **cliente malicioso** derrumba FedAvg (de ~0.95 a ~0.10), mientras que la **agregación robusta** aguanta (~0.94).

## Qué se debe entregar — Mini-TP 7 (individual, esta semana)

1. **Elige una capa** (la que use tu modelo, o la que prefieras) y abre su notebook guiado.
2. **Completa los `# TODO`**: aplica el control de seguridad principal sobre **tu modelo**.
3. **Agrega una medida de gobernanza**: una *model card* o un *logging* de auditoría.
4. **Reflexión**: qué amenaza mitigas y a qué **elemento de SAIF** corresponde.

**Se evalúa:** que corra de punta a punta; que el control esté bien aplicado sobre el modelo propio; la medida de gobernanza; y la reflexión SAIF.

> **Cierra el ciclo.** Al terminar este Mini-TP, tu modelo ya está servido por REST/GraphQL/gRPC, integrado a streaming, entrenable en federado, guardado en un data lake y **asegurado**. Todo listo para el **Taller integrador y la defensa** (Sesión 8).

## Conexión con lo anterior

La seguridad es **transversal**: toca la API (REST/GraphQL/gRPC), el dato en movimiento (streaming), el entrenamiento distribuido (federado) y el almacenamiento (data lake). SAIF da el marco; la defensa en profundidad lo vuelve capas concretas de controles.
