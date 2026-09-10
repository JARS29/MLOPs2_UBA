# Sesión 3 — gRPC para microservicios de ML

Tercera capa de la plataforma: comunicación **interna** entre servicios con **latencia mínima** y un **contrato estricto**, cuando REST/GraphQL (texto sobre HTTP/1.1) se quedan cortos.

---

## Parte teórica (`Teoria/`)

- **Del problema a RPC:** por qué comunicar servicios es difícil (heterogeneidad, fallos); capas y sockets; RPC como la idea de "llamar a lo remoto como si fuera local" (stubs, dispatcher, binding).
- **Serialización y Protocol Buffers:** marshalling, orden de bytes, formatos (XML → JSON → Protobuf); el `.proto` como contrato tipado, compacto y binario.
- **gRPC:** HTTP/2 como transporte (multiplexado, binario, streaming); los **cuatro tipos** de RPC (unary, server/client streaming, bidireccional); arquitectura `.proto → protoc → stubs`.
- **Comparativa y MLOps:** gRPC vs REST vs GraphQL (cuándo cada uno); gRPC para inferencia interna de baja latencia, streaming de predicciones y microservicios de ML.

La teoría viene como **notebook-tutorial ejecutable**: `Teoria/grpc_tutorial.ipynb`.
Presentación: `Sesion3_gRPC_2026.pptx` (fuera del repo, junto a los pptx).

## Parte práctica — cómo correr

El curso usa **[uv](https://docs.astral.sh/uv/)**. Preparación (una vez, desde la raíz): `uv sync`. O, para esta sesión:
```bash
uv add grpcio grpcio-tools scikit-learn joblib fastapi "uvicorn[standard]" requests
```
Para correr los notebooks, registra el kernel de uv (ver *Puesta en marcha* del README raíz).

| Notebook | Carpeta | Qué hace | Cómo correr |
|---|---|---|---|
| `grpc_tutorial.ipynb` | `Teoria/` | **Tutorial de teoría + práctica guiada:** escribe `scoring.proto`, genera los stubs con `grpc_tools.protoc`, levanta un servidor (unary + server-streaming) en un hilo, lo llama desde un cliente y **compara latencia gRPC vs REST**. Autocontenido. | seguir el notebook con el kernel de uv |
| `mini_tp3_actividad.ipynb` | `Practica/` | **Starter del Mini-TP 3** (trabajo individual) con celdas `# TODO` para exponer *tu* modelo por gRPC. | completar y ejecutar el notebook |

**Generar los stubs (idea):**
```bash
uv run python -m grpc_tools.protoc -I. --python_out=. --grpc_python_out=. scoring.proto
# crea scoring_pb2.py (mensajes) y scoring_pb2_grpc.py (stub cliente + clase base servidor)
```
Los `*_pb2*.py` **no se editan a mano**: se regeneran si cambia el `.proto`. Puerto típico de gRPC: `50051`.

## Qué se debe entregar — Mini-TP 3 (individual, esta semana)

Expón **tu modelo** (el de la Sesión 1) por gRPC:

1. `scoring.proto` con un servicio para tu modelo (entrada y salida **tipadas**).
2. Stubs generados + **servidor** que carga tu modelo **una sola vez**.
3. **Cliente** que llama al servicio (**unary**).
4. Un método de **server-streaming** que puntúe un lote.
5. **Compara la latencia** contra tu endpoint REST de la Sesión 1 y anota una breve reflexión.

Tienes un **starter** listo para completar: **`mini_tp3_actividad.ipynb`**.

**Se evalúa:** que corra de punta a punta; que el `.proto` tipe entrada y salida; que funcionen unary y streaming; y la reflexión gRPC vs REST (latencia).
