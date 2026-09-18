[![CC BY-NC-SA 4.0][cc-by-nc-sa-shield]][cc-by-nc-sa]

# Operaciones de Aprendizaje Automático II

Material de clases de **Operaciones de Aprendizaje Automático II** — CEIA · FIUBA.
Docente: Jaime A. Riascos-Salas.

> El material se publica **semana a semana**. Cada carpeta `claseN/` se completa al llegar a esa sesión. Hoy están disponibles las **Sesiones 1 a 4**.

Criterios de aprobación: ver [CriteriosAprobacion.md](CriteriosAprobacion.md).

### Objetivo

MLOps II es la continuación de [Operaciones de Aprendizaje Automático I](https://github.com/FIUBA-Posgrado-Inteligencia-Artificial/aprendizaje_maquina_II). Se centra en **cómo se comunica y opera un modelo en producción**: flujo de datos, protocolos (REST, GraphQL, gRPC), streaming, nube y Data Lakes, aprendizaje federado, y seguridad/operación/gobernanza. Son **8 encuentros de ~3 horas**.

### Hilo conductor

Todo el bimestre construye la **plataforma de predicción en tiempo real de "ML Models & Something More Inc."** (la empresa del trabajo práctico final). Cada sesión **agrega una capa** a la misma arquitectura de referencia (Airflow + MLflow + FastAPI + MinIO/S3 sobre Docker). El **mini-TP** de cada clase agrega esa capa sobre el modelo propio del estudiante.

### Evaluación

- **Mini-TP por clase (Sesiones 1–7):** actividad corta, individual y evaluable sobre el modelo propio.
- **TP integrador grupal (2–6)** con hitos: arquitectura en la **Sesión 4**, checkpoint evaluado en la **Sesión 6**, defensa en la **Sesión 8**. Niveles: local (6–8) y contenedores (8–10).

Ver [PROYECTOS_ANTERIORES.md](PROYECTOS_ANTERIORES.md) para tener algunas ideas de lo presentado en bimestres anteriores.

### Organización

```
claseN/
    teoria/     # material teórico
    practica/   # notebooks, código y datos (ejecutable de punta a punta)
    README.md   # teoría, práctica (cómo correr) y qué entregar
```
Las **guías del docente** (notas del orador y guía de la práctica) viven fuera del repositorio, junto a las presentaciones.

## Programa

| Sesión | Tema | Carpeta | Estado |
|---|---|---|---|
| 1 | Del modelo al servicio: APIs REST productivas | [clase1](clase1/README.md) | ✅ Disponible |
| 2 | GraphQL en MLOps + grafos de linaje | [clase2](clase2/README.md) | ✅ Disponible |
| 3 | gRPC para microservicios de ML | [clase3](clase3/README.md) | ✅ Disponible |
| 4 | Streaming e inferencia en tiempo real · Hito TP #1 | [clase4](clase4/README.md) | ✅ Disponible |
| 5 | Nube y Data Lakes para MLOps | clase5 | 🔒 Próximamente |
| 6 | Aprendizaje Federado · Hito TP #2 | clase6 | 🔒 Próximamente |
| 7 | Seguridad, operación y gobernanza | clase7 | 🔒 Próximamente |
| 8 | Taller integrador y defensa | clase8 | 🔒 Próximamente |

### Requerimientos

Python ≥ 3.10 y **[uv](https://docs.astral.sh/uv/)** para gestionar el entorno. En la raíz, `uv sync` instala las dependencias del curso desde `pyproject.toml` / `uv.lock`. También se usan MLflow, Jupyter, GitHub, Docker y Apache Airflow. IDE: VS Code o PyCharm Community.

> **Nota para quienes usan Poetry.** El repositorio migró de Poetry a **uv**: el `pyproject.toml` está en formato estándar PEP 621 y hay un `uv.lock` (se eliminó `poetry.lock`). Si prefieres seguir con Poetry, usa Poetry ≥ 2.0 (que lee proyectos PEP 621) con `poetry install`, o instala con `pip install -e .`. Los comandos de cada clase muestran la variante `uv` con su equivalente. Al migrar se ajustaron pins incompatibles del original (Airflow ↔ SQLAlchemy y algunos proveedores de Airflow).

## Puesta en marcha (leer antes de la primera clase)

### 1. Clona el repo FUERA de servicios de sincronización

Pon el repositorio en una carpeta local (por ejemplo `C:\Users\<usuario>\Documents\GitHub\`). **No lo ubiques dentro de OneDrive, Google Drive o Dropbox:** esos servicios sincronizan los archivos internos de `.git` y **corrompen el repositorio** (pasó en una sesión: git dejó de poder hacer commit). Si ya lo tienes en OneDrive, clona de nuevo afuera y copia tus archivos **excluyendo `.git` y `.venv`**:

```powershell
robocopy "<repo en OneDrive>" "<repo en Documents\GitHub>" /MIR /XD ".git" ".venv" "__pycache__"
```

### 2. Un solo entorno con uv (en la raíz)

No hace falta un entorno por sesión. Desde la raíz del repo, una sola vez:

```bash
uv sync      # crea .venv con TODAS las dependencias del curso (incluye Jupyter e ipykernel)
```

### 3. Correr los notebooks: registrar el kernel de uv

Si al abrir un `.ipynb` Jupyter o VS Code te pide `ipykernel` o no encuentra el kernel del entorno, regístralo una vez:

```bash
uv run python -m ipykernel install --user --name mlops2 --display-name "Python (MLOps2 · uv)"
```

Después elige el kernel **"Python (MLOps2 · uv)"** en el notebook. Alternativas: en VS Code, selecciona directamente el intérprete `.venv` de la carpeta; o levanta Jupyter desde el entorno con `uv run jupyter lab`. (Si armaste el entorno con `uv pip install -r requirements.txt` en vez de `uv sync`, agrega el kernel con `uv pip install ipykernel jupyter` y vuelve a registrarlo.)

### 4. Qué NO se versiona

`.env` (variables de entorno y **secretos**) y `.venv/` (el entorno, pesado) están en `.gitignore` y **nunca se commitean**. Si copias la carpeta con `robocopy`, excluye `.venv`, `.git` y `__pycache__` para que sea rápida. Para compartir qué variables hacen falta, versiona un `.env.example` (sin valores reales).

## Bibliografía

- *Designing Machine Learning Systems* — Chip Huyen (O’Reilly)
- *Machine Learning Engineering with Python* — Andrew P. McMahon (Packt)
- *Practical MLOps* — Noah Gift, Alfredo Deza (O’Reilly)
- *Introducing MLOps* — Treveil, Omont, Stenac et al. (O’Reilly)
- *Machine Learning Engineering* — Andriy Burkov (True Positive Inc.)

---
Obra bajo [Licencia Creative Commons Atribución-NoComercial-CompartirIgual 4.0][cc-by-nc-sa].

[![CC BY-NC-SA 4.0][cc-by-nc-sa-image]][cc-by-nc-sa]

[cc-by-nc-sa]: https://creativecommons.org/licenses/by-nc-sa/4.0/deed.es
[cc-by-nc-sa-image]: https://licensebuttons.net/l/by-nc-sa/4.0/88x31.png
[cc-by-nc-sa-shield]: https://img.shields.io/badge/License-CC%20BY--NC--SA%204.0-lightgrey.svg
