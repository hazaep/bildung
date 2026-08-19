# BRIEF — Tech Lead / self-driving-lab (Ciclo 10)

> **De:** Arquitecto (Capa 3)
> **Para:** Tech Lead (Capa 4)
> **Fecha:** Agosto 2026
> **Ciclo:** 10 — Code + Deps (Fase III: Adapters de Código I)

---

## Objetivo

Fase III del roadmap. Dos adapters de código construidos sobre `ast.parse`.
Ambos sin subprocess. Ambos sobre el filesystem local. Cada uno valida una
dimensión arquitectónica nueva: `code` convierte estructura en estructura
(vía AST), `deps` modela un grafo (nodos y aristas) — el primer adapter
que devuelve algo que no es ni árbol ni lista.

```
Task A (Ejecutor A)              Task B (Ejecutor B)
Code adapter                     Deps adapter
adapters/code/                   adapters/deps/
structure, imports, summary      graph, impact
       │                                │
       └───────────────┬────────────────┘
                       │
                REPORT_TECH_LEAD
```

**Paralelo.** No comparten archivos. Ambos usan `ast` (stdlib), pero son
directorios independientes. Cero riesgo de conflicto.

---

## Task A — Code adapter (Ejecutor A)

### Qué

Construir `adapters/code/` — séptimo adapter EAL. Usa `ast.parse()` para
extraer la estructura interna de archivos Python: funciones, clases, imports,
métricas de líneas. Es el análogo exacto de filesystem para el dominio código:
filesystem ve bytes y paths, code ve funciones y clases.

### Por qué es arquitectónicamente nuevo

| Adapter | Dominio | Mecanismo |
|---|---|---|
| filesystem | Archivos y directorios | `os.listdir()`, `os.stat()` |
| **code** | **Funciones y clases** | **`ast.parse()`, `ast.walk()`** |

Es el segundo adapter post-subprocess. Valida que el patrón "stdlib parsing →
modelo canónico" es repetible en dominios distintos.

### Modelo canónico

```yaml
# eal code structure --path adapters/filesystem/adapter.py
path: adapters/filesystem/adapter.py
language: python
total_lines: 265
code_lines: 210
doc_lines: 18
blank_lines: 37
functions:
  - name: _fmt_size
    lineno: 20
    args: ["size_bytes"]
    returns: str
    docstring: "Format bytes to human-readable: B, KB, MB."
    decorators: []
  - name: handle
    lineno: 42
    args: ["command", "args"]
    returns: dict
    docstring: "Route command to handler. Never raises."
    decorators: []
  - name: _tree_walk
    lineno: 88
    args: ["p", "current_depth"]
    returns: tuple
    docstring: null
    decorators: []
classes: []
imports:
  - module: fnmatch
    alias: null
  - module: os
    alias: null
  - module: json
    alias: null

# eal code imports --path adapters/
files:
  - path: bildung/adapter.py
    imports_engine: true
    external_deps: ["sys", "re", "json", "pathlib", "yaml"]
  - path: docker/adapter.py
    imports_engine: true
    external_deps: ["json", "sys", "pathlib"]
  - path: filesystem/adapter.py
    imports_engine: false
    external_deps: ["fnmatch", "os", "json", "time", "pathlib"]

# eal code summary --path adapters/
files: 12
total_lines: 2864
total_functions: 67
total_classes: 2
avg_function_lines: 18
files_with_docstrings: 8
files_without_docstrings: 4
top_imports:
  - module: json (10 files)
  - module: time (8 files)
  - module: pathlib (7 files)
  - module: engine (4 files)
  - module: sys (4 files)
```

### Arquitectura

```
adapters/code/
├── __init__.py       ← ADAPTER_NAME = "code" + handle()
└── adapter.py        ← structure, imports, summary (~120 líneas)
```

### Comandos

| Comando | Args | Implementación |
|---|---|---|
| `structure` | `--path` (requerido) | `ast.parse()` + `ast.walk()` sobre UN archivo |
| `imports` | `--path` (directorio, default `.`) | Itera todos los `.py`, extrae `ast.Import`/`ast.ImportFrom` |
| `summary` | `--path` (directorio, default `.`) | Agrega stats de todos los `.py`: líneas, funciones, docstrings |

### Reglas

- **Solo stdlib.** `ast`, `os`, `pathlib`, `json`, `time`. Sin dependencias.
- **Solo archivos `.py`.** Si `--path` apunta a un directorio, itera recursivamente con `os.walk`.
- **Sintaxis inválida → skip.** Si `ast.parse()` lanza `SyntaxError`, el archivo se omite con warning en stderr, pero no interrumpe.
- **`handle()` nunca lanza excepciones.**
- **Métricas vinculantes.** `time_ms`, `chars_processed` (bytes leídos de disco), `chars_returned`.
- **Depth protection no necesaria** — `ast.parse()` opera sobre un archivo a la vez. `imports` y `summary` sobre directorios usan `os.walk()` sin límite de profundidad (igual que filesystem `find`).

### Criterios de éxito

- [ ] `adapters/code/__init__.py` exporta `ADAPTER_NAME = "code"` y `handle`
- [ ] `eal code structure --path adapters/filesystem/adapter.py` → funciones con name, lineno, args, returns, docstring
- [ ] `eal code structure --path adapters/bildung-context/eal_adapter.py` → detecta clase `BildungContextAdapter`, funciones, imports
- [ ] `eal code imports --path adapters/` → por archivo: imports_engine (bool), external_deps
- [ ] `eal code summary --path adapters/` → total_files, total_lines, total_functions, top_imports
- [ ] Archivo con sintaxis inválida → omitido, no crashea
- [ ] Path inexistente → `ok: false`
- [ ] Path a un `.md` (no Python) → `ok: false` con mensaje
- [ ] Métricas `chars_processed`, `chars_returned`, `time_ms` en toda respuesta
- [ ] `handle()` nunca lanza excepciones
- [ ] Cero dependencias externas (solo stdlib)

---

## Task B — Deps adapter (Ejecutor B)

### Qué

Construir `adapters/deps/` — octavo adapter EAL. Analiza imports entre archivos
Python y construye un grafo de dependencias. Primer adapter cuyo output no es
ni árbol ni lista: es un grafo con nodos y aristas.

### Por qué es arquitectónicamente nuevo

Todos los adapters hasta ahora devuelven listas, árboles, o agregaciones. `deps`
devuelve `{nodes: [...], edges: [...]}` — una estructura de grafo. Si el
contrato `ADAPTER_NAME + handle()` sobrevive a esto, sobrevive a cualquier
estructura de datos.

### Modelo canónico

```yaml
# eal deps graph --path adapters/
nodes:
  - file: bildung/adapter.py
    type: adapter
    imports_count: 5
    imported_by_count: 0
  - file: bildung-context/eal_adapter.py
    type: adapter
    imports_count: 4
    imported_by_count: 0
  - file: docker/adapter.py
    type: adapter
    imports_count: 3
    imported_by_count: 0
  - file: engine.py
    type: engine
    imports_count: 8
    imported_by_count: 4
edges:
  - from: bildung/adapter.py
    to: engine.py
    type: internal
  - from: docker/adapter.py
    to: engine.py
    type: internal
  - from: systemd/adapter.py
    to: engine.py
    type: internal
  - from: git/adapter.py
    to: engine.py
    type: internal
orphans:
  - filesystem/adapter.py
isolated: false

# eal deps impact --path adapters/ --file engine.py
file: engine.py
imported_by:
  - bildung/adapter.py
  - docker/adapter.py
  - systemd/adapter.py
  - git/adapter.py
total_dependents: 4
risk: high
risk_reason: "4 adapters depend on engine. Changes to engine.py affect 67% of adapters."
```

### Arquitectura

```
adapters/deps/
├── __init__.py       ← ADAPTER_NAME = "deps" + handle()
└── adapter.py        ← graph, impact (~150 líneas)
```

### Comandos

| Comando | Args | Implementación |
|---|---|---|
| `graph` | `--path` (directorio, default `.`) | Itera `.py`, extrae imports, resuelve paths relativos, construye grafo |
| `impact` | `--path` (directorio), `--file` (requerido) | Reverse lookup: ¿qué archivos importan a `--file`? |

### Resolución de imports

Este es el corazón del adapter y el edge case más interesante:

```python
# Caso 1: import engine  → busca engine.py en el mismo directorio o sys.path
# Caso 2: from .adapter import ADAPTER_NAME  → resuelve . al directorio del archivo
# Caso 3: import json  → stdlib, no es dependencia interna
# Caso 4: import yaml  → externo, no es dependencia interna
```

**Regla de resolución:**
1. Extraer todos los imports del AST.
2. Para cada import: si es `from .X import Y`, resolver `.` al directorio del archivo → buscar `X.py`.
3. Si es `import X` y `X.py` existe en el mismo directorio → dependencia interna.
4. Si es `import X` y `X` es stdlib (`sys`, `os`, `json`, `ast`, etc.) → ignorar.
5. Todo lo demás → dependencia externa (no genera arista en el grafo).

El edge case real: los adapters docker, systemd, bildung, git usan este patrón:
```python
_ENGINE_DIR = Path(__file__).parent.parent
if str(_ENGINE_DIR) not in sys.path:
    sys.path.insert(0, str(_ENGINE_DIR))
import engine
```
`ast.parse()` ve `import engine`. El adapter `deps` debe detectar que `engine.py`
existe en `adapters/engine.py` (directorio padre de los adapters) — no en el
mismo directorio que `docker/adapter.py`.

**Solución:** si un `import X` no se resuelve en el mismo directorio, buscar
`X.py` en el directorio padre y en el directorio del script. Si se encuentra,
es dependencia interna.

### Reglas

- **Solo stdlib.** `ast`, `os`, `pathlib`, `json`, `time`.
- **Grafo dirigido.** A→B significa "A importa a B".
- **`impact` calcula riesgo:** "high" si ≥3 dependents, "medium" si 1-2, "low" si 0.
- **`handle()` nunca lanza excepciones.**
- **Métricas vinculantes.** `chars_processed` = bytes de todos los `.py` leídos. `chars_returned` = len del JSON.

### Criterios de éxito

- [ ] `adapters/deps/__init__.py` exporta `ADAPTER_NAME = "deps"` y `handle`
- [ ] `eal deps graph --path adapters/` → nodos (file, type, imports_count, imported_by_count) + aristas (from, to, type)
- [ ] `eal deps graph --path adapters/` → detecta `engine.py` con `imported_by_count: 4`
- [ ] `eal deps graph --path adapters/` → `filesystem/adapter.py` en orphans (no importa engine)
- [ ] `eal deps impact --path adapters/ --file engine.py` → 4 dependents, `risk: high`
- [ ] `eal deps impact --path adapters/ --file filesystem/adapter.py` → 0 dependents, `risk: low`
- [ ] Path inexistente → `ok: false`
- [ ] Directorio sin `.py` → `ok: true`, grafo vacío (no error)
- [ ] Métricas `chars_processed`, `chars_returned`, `time_ms` en toda respuesta
- [ ] `handle()` nunca lanza excepciones
- [ ] Cero dependencias externas (solo stdlib)

---

## Arquitectura de roles

| Rol | Responsable | Tareas |
|---|---|---|
| **Tech Lead** | Vos | Preparar specs para Ejecutores A y B → supervisar en paralelo → integrar → reportar |
| **Ejecutor A** | Agente | Task A: code adapter |
| **Ejecutor B** | Agente | Task B: deps adapter |

---

## Lo que ya existe (no reconstruir)

| Artefacto | Estado |
|---|---|
| `adapters/filesystem/` — 265 líneas, `os.*` | ✅ Referencia de adapter post-subprocess |
| `adapters/docker/` — 258 líneas, `run_subprocess` | ✅ No se modifica |
| `runtime.py` — 469 líneas, test + telemetry | ✅ No se modifica |
| `adapters/engine.py` — 12 funciones | ✅ Sujeto de análisis de deps |
| `eal-config.yaml` — 6 adapters | ✅ Tech Lead agrega code + deps |
| `specs/adapter_contract.md` | ✅ Solo lectura |

---

## Restricciones

- **No escribas código.** Prepará specs, delegá, supervisá.
- **No modifiques runtime, engine, ni otros adapters.**
- **No uses subprocess.** Ambos adapters son post-subprocess. Solo `ast.*` + `os.*`.
- **No toques Gobernanza ni Codex.**
- **Máximo 2 iteraciones por Ejecutor.**
- **`eal test all` debe seguir pasando 8/8 después de integrar.**

---

## Documentos de referencia

- `specs/adapter_contract.md` — contrato canónico
- `specs/roadmap_eal.md` — Fase III, Ciclo 10
- `adapters/filesystem/adapter.py` — referencia post-subprocess
- `adapters/docker/adapter.py` — referencia con `engine.run_subprocess()` (sujeto de deps)
- `docs/agent_hierarchy.md` — tu rol (Capa 4)
- `docs/reporting_protocol.md` — formato de REPORT.md
