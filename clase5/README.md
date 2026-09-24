# Sesión 5 — Aprendizaje Federado

Quinta capa de la plataforma: **entrenar sin centralizar los datos**. Cuando el dato no puede (ni conviene) salir de donde está —por privacidad, regulación, latencia o costo— el modelo viaja a los datos, no al revés.

---

## Parte teórica (`Teoria/`)

- **El dilema del dato:** por qué a veces no se centraliza (privacidad y regulación, latencia/offline, costo/batería, relevancia del dato local).
- **Qué es el aprendizaje federado:** mover el cómputo al dato; el modelo viaja y solo se comparten **parámetros**, nunca los datos brutos.
- **Arquitectura y FedAvg:** clientes, servidor de agregación y **rondas de comunicación**; la fórmula del promedio ponderado `w_global = Σ (n_k/n) w_k`; cross-device vs cross-silo.
- **Desafíos:** datos **non-IID** (heterogeneidad), costo de comunicación, clientes intermitentes, y por qué **federado no es automáticamente privado**.
- **Privacidad:** privacidad diferencial a nivel de usuario (**DP-FedAvg**: recortar el update a norma L2 + agregar ruido) y, a nivel conceptual, DP local vs central vs distribuida y agregación segura.
- **Seguridad:** ataques de **envenenamiento de datos** y defensas (agregación robusta, monitoreo por clase), que conectan con la Sesión 7.
- **Trade-off privacidad vs performance** y frameworks (**Flower**, TensorFlow Federated, PySyft).

La teoría viene en dos archivos PDFs: `Teoria/Federated_Learning_UBA.pdf` y `Teoria/MLOps2_Sesion5_Federado.pdf`.
---

## Los tres notebooks de la sesión

Tres tutoriales que cuentan la misma historia con distinto foco. El primero es para **entender**; los otros dos son **trabajo adicional** que suma reproducibilidad (Docker) y **seguridad** (un ataque de envenenamiento y sus defensas). Aparte está el **Mini-TP 5**, que es la entrega individual.

### 1. `Teoria/federated_tutorial.ipynb` — sin Docker (entender FedAvg)

Implementa **FedAvg desde cero** en numpy (un clasificador softmax cuyos pesos son justo lo que se promedia). Reparte el dataset `digits` entre varios clientes y muestra:

- **IID vs non-IID:** con datos IID el federado llega muy cerca del centralizado (≈0.97) **sin mover un dato**; con non-IID la curva **oscila** y termina algo peor (≈0.95) — el costo de la heterogeneidad.
- **Privacidad diferencial (DP-FedAvg):** recorta el update de cada cliente a una norma L2 `S`, promedia y agrega ruido gaussiano; al subir el ruido baja la accuracy (el **trade-off privacidad vs performance**, medible).
- Cómo se ve el mismo patrón con **Flower**.

Autocontenido, con semilla fija; corre en segundos. Es la base conceptual para los otros dos.

### 2. `Practica/federated_envenenamiento.ipynb` — sin Docker + seguridad (numpy)

El **mismo FedAvg** (numpy/scikit-learn) pero con el **plus de seguridad**: un **ataque de envenenamiento de datos** en el que un cliente malicioso intercambia las etiquetas **3 ↔ 7** (*label flipping*). Se entrena **sin** y **con** ataque y se compara:

- **Global:** ≈0.97 → ≈0.95 (caída sutil).
- **Por clase:** las clases **3 y 7 se desploman** (≈1.00 → ≈0.88), el resto se mantiene — la firma del ataque dirigido.

Cierra con las **defensas** (agregación robusta, detección de anomalías, clipping + DP, monitoreo por clase). Mínimo de dependencias.

### 3. `Practica/federated_docker_flower.ipynb` — con Docker + Flower + seguridad

El mismo experimento con **[Flower](https://flower.ai/) (`flwr`)**, el **framework de producción** para federado: un `NumPyClient` que entrena local y una **estrategia `FedAvg`** en el servidor que agrega y evalúa por ronda. Reproduce el ataque de envenenamiento (3 ↔ 7) con idéntico resultado, y explica cómo varias **defensas** se implementan cambiando la **estrategia** del servidor (mediana / trimmed mean / Krum, DP). 

> **Por qué no TensorFlow Federated.** La versión original de estos notebooks usaba TFF, pero **ya no instala desde PyPI** (fija `jaxlib`/`farmhashpy` retirados del índice) y el `docker build` falla. Por eso el stack es numpy/scikit-learn y, para el framework, **Flower** —que sí instala y es hoy el estándar—. El concepto de FedAvg y del ataque es idéntico.

---

## Cómo correr

El curso usa **[uv](https://docs.astral.sh/uv/)**. Preparación (una vez, desde la raíz): `uv sync`. O, para esta sesión:
```bash
uv add scikit-learn numpy matplotlib
uv add flwr          # solo para la parte de Flower
```
Para correr los notebooks, registra el kernel de uv (ver *Puesta en marcha* del README raíz). El notebook **con Docker** se ejecuta construyendo la imagen y corriendo el contenedor (comandos dentro del notebook, con las variantes de Windows PowerShell/CMD); necesitas **Docker Desktop**.

| Notebook | Carpeta | Foco | Cómo correr |
|---|---|---|---|
| `federated_tutorial.ipynb` | `Practica/` | FedAvg desde cero · IID/non-IID · **DP** | kernel de uv (sin Docker) |
| `federated_envenenamiento.ipynb` | `Practica/` | Mismo modelo · **envenenamiento** (numpy) | kernel de uv (sin Docker) |
| `federated_docker_flower.ipynb` | `Practica/` | Docker · **Flower** · envenenamiento | `docker build` + `docker run` |
| `mini_tp5_federado_actividad.ipynb` | `Practica/` | **entrega** individual (starter) | kernel de uv |

---

## Qué se debe entregar — Mini-TP 5 (individual, esta semana)

Federa **tu modelo** (o el dataset provisto) y **mide el trade-off**:

1. Reparte el dataset entre **K clientes** y corre una simulación federada con **FedAvg**.
2. Compara la accuracy **federada** contra la **centralizada**.
3. Repite con una partición **non-IID** y grafica accuracy vs ronda.
4. **Analiza el trade-off privacidad vs performance:** ¿cuánto cuesta no mover el dato?
5. *(Opcional)* Agrega ruido (privacidad diferencial simple) y mide el impacto.

Starter: **`mini_tp5_federado_actividad.ipynb`**. **Se evalúa:** que corra de punta a punta; FedAvg bien implementado; comparación IID / non-IID / centralizado; y la reflexión sobre el trade-off.
