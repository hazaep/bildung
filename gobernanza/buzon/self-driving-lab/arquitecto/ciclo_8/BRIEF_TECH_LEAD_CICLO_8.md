# BRIEF — Tech Lead / self-driving-lab (Ciclo 8)

> **De:** Arquitecto (Capa 3)
> **Para:** Tech Lead (Capa 4)
> **Fecha:** Agosto 2026
> **Ciclo:** 8 — Saneamiento + Filesystem + Bildung Extendido

---

## Objetivo

Tres tareas, dos delegaciones en paralelo, una ejecución directa del Tech Lead.
Fase II del roadmap: saneamiento técnico más dos líneas de expansión que validan
dimensiones arquitectónicas complementarias.

```
Task 8 (Tech Lead)          Task 10a (Ejecutor A)     Task 10b (Ejecutor B)
Saneamiento                 Filesystem adapter         Bildung extendido
_infer_type fix             adapters/filesystem/       search + dependencias
.gitignore egg-info         tree, find, size           en adapters/bildung/
CHANGELOG.md                       │                          │
      │                            └──────────┬───────────────┘
      │                                       │
      └───────────────────────────────────────┘
                    REPORT_TECH_LEAD
```

**Task 8 va primero** (desbloquea CHANGELOG que documenta el estado pre-expansión).
**Tasks 10a y 10b en paralelo** (no comparten archivos, Ejecutores independientes).

---

## Task 8 — Saneamiento (Tech Lead directo, sin delegación)

### Qué

Tres cambios de ≤3 líneas cada uno. El Tech Lead los ejecuta manualmente.
No justifica preparar specs, contexto, ni delegación.

| # | Archivo | Acción | Líneas |
|---|---|---|---|
| 8a | `runtime.py` | Fix `_infer_type('-5')` → float en vez de int | 1 línea |
| 8b | `.gitignore` | Agregar `*.egg-info/` | 1 línea |
| 8c | `CHANGELOG.md` | Nuevo: documenta evolución de 7 ciclos | ~40 líneas |

### 8a — Fix `_infer_type` negativos

**Problema:** `_infer_type('-5')` cae al path float porque `'-5'.isdigit()` es False (el signo).

**Fix:** reorganizar el orden de chequeo. Los ints negativos deben detectarse antes del path float:

```python
# Línea 27-35 de runtime.py, reemplazar:
    if value.isdigit():
        return int(value)
    cleaned = value.replace('.', '', 1).lstrip('-')
    if cleaned.isdigit() and value.count('.') <= 1 and value.count('-') <= 1:
        try:
            return float(value)
        except ValueError:
            pass

# Por:
    if value.isdigit():
        return int(value)
    # Entero negativo: -5, -42
    if value.startswith('-') and value[1:].isdigit():
        return int(value)
    # Float: 3.14, -0.5
    cleaned = value.replace('.', '', 1).replace('-', '', 1)
    if cleaned.isdigit() and value.count('.') <= 1:
        try:
            return float(value)
        except ValueError:
            pass
```

**Validación:** `python3 -c "from runtime import _infer_type; assert _infer_type('-5') == -5"`

### 8b — `.gitignore` egg-info

Agregar una línea al final de `.gitignore`:

```
*.egg-info/
```

**Validación:** `git status` ya no muestra `eal.egg-info/` como untracked.

### 8c — CHANGELOG.md

Documentar la evolución de la EAL en orden cronológico inverso. Entrada por cada
ciclo completado (0-7), con: fecha, objetivo, output, métricas clave, agente usado.

Formato de referencia:

```markdown
# Changelog — EAL (Entropic Abstraction Layer)

## [0.1.0] — 2026-08-10

### Added (Ciclo 7)
- Runtime type coercion: `_infer_type()` infiere int, float, bool de args CLI
- Telemetry built-in: dominio interno del Runtime con captura pasiva a JSONL
- `telemetry summary`, `telemetry ratios`, `telemetry recent`

### Changed
- runtime.py: 157→344 líneas (+187)
- systemd/adapter.py: `int(limit)` defensivo en `_handle_logs`

### Removed
- Captura de métricas vía bildung-context. Métricas ahora en `~/.bildung/eal/metrics.jsonl`

## [0.0.6] — 2026-08-10
...
```

El Tech Lead extrae datos de `bitacora.md` y `specs/ciclo_N_*/REPORT_TECH_LEAD.md`.
No es necesario ser exhaustivo — una línea por ciclo alcanza. El valor es tener
un documento de referencia para cuando alguien nuevo lea el código.

**Criterios de éxito:**
- [ ] `python3 -c "from runtime import _infer_type; assert _infer_type('-5') == -5; assert _infer_type('20') == 20; assert _infer_type('nginx') == 'nginx'"`
- [ ] `git status` no muestra `eal.egg-info/`
- [ ] `CHANGELOG.md` existe con al menos 7 entradas (Ciclos 1-7)

---

## Task 10a — Filesystem adapter (Ejecutor A)

### Qué

Construir `adapters/filesystem/` — quinto adapter EAL. El más simple del
ecosistema: parsea outputs tabulares de herramientas estándar del sistema
operativo.

### Por qué ahora — y por qué es arquitectónicamente relevante

El roadmap original en `proximos_adapters.md` lo ponía tercero (después de
systemd). Se adelanta a esta fase porque:

1. **Es el canario del contrato.** Si `ls -la` no se puede normalizar a un
   modelo canónico con `ADAPTER_NAME + handle()`, algo está fundamentalmente
   roto en el diseño de la EAL. Filesystem es el dominio más trivial posible
   — si no funciona aquí, no funciona en ningún lado.
2. **Cero dependencias externas.** Solo `os.listdir()`, `os.stat()`, `fnmatch`.
   Sin subprocess. Sin parseo de texto. El adapter más puro del ecosistema.
3. **Métricas de compresión en dominio ubicuo.** `tree /home/user/bildung` puede
   producir fácilmente 5,000+ caracteres de output → el adapter lo reduce a
   un modelo canónico de ~400. Otra validación de la hipótesis EAL.

### Modelo canónico

```yaml
# eal filesystem tree --path . --depth 2
path: /home/user/bildung/Lab/3-dev/self-driving-lab/workdir/02-eal
total_files: 41
total_dirs: 15
total_size: "397 KB"
tree:
  - name: adapters/
    type: dir
    size: "156 KB"
    children:
      - name: bildung/
        type: dir
        files: 2
      - name: bildung-context/
        type: dir
        files: 6
      - name: git/
        type: dir
        files: 2
      - name: systemd/
        type: dir
        files: 2
  - name: eal/
    type: dir
    size: "142 KB"
    children: [...]

# eal filesystem find --path . --pattern "*.py"
path: /home/user/bildung/Lab/3-dev/self-driving-lab/workdir/02-eal
pattern: "*.py"
count: 19
matches:
  - path: adapters/bildung/adapter.py
    size: 13341
    modified: "2026-08-10T21:30:00"
  - path: adapters/engine.py
    size: 8521
    modified: "2026-08-09T18:00:00"
  - ...

# eal filesystem size --path . --depth 1
path: /home/user/bildung/Lab/3-dev/self-driving-lab/workdir/02-eal
entries:
  - name: adapters/
    size: "156 KB"
    files: 12
    dirs: 6
  - name: eal/
    size: "142 KB"
    files: 15
    dirs: 5
  - name: specs/
    size: "223 KB"
    files: 51
    dirs: 7
total:
  size: "552 KB"
  files: 81
  dirs: 15
```

### Arquitectura

```
adapters/filesystem/
├── __init__.py       ← ADAPTER_NAME = "filesystem" + handle()
└── adapter.py        ← tree, find, size (~120 líneas estimadas)
```

### Comandos

| Comando | Args | Implementación | Output |
|---|---|---|---|
| `tree` | `--path` (default `.`), `--depth` (default 2) | `os.listdir()` recursivo | Jerarquía con files, dirs, tamaño |
| `find` | `--path` (default `.`), `--pattern` (default `*`), `--type` (f/d, opcional) | `os.walk()` + `fnmatch` | Lista de paths con metadata |
| `size` | `--path` (default `.`), `--depth` (default 1) | `os.stat()` acumulativo | Tamaño por directorio + total |

### Reglas

- **Cero dependencias externas.** Solo stdlib: `os`, `fnmatch`, `json`, `pathlib`.
- **No usar `engine.run_subprocess()`.** Filesystem accede al sistema de archivos directamente vía `os`. Es el primer adapter sin subprocess — valida que el contrato funciona también con acceso directo a syscalls.
- **`handle()` nunca lanza excepciones.** Si el path no existe, `ok: false`.
- **Métricas vinculantes.** `time_ms`, `chars_processed`, `chars_returned`.
- **`chars_processed`:** tamaño en bytes de todos los `os.stat()` consultados. `chars_returned`: `len(json.dumps(data))`.
- **Depth protection:** `--depth` máximo 5. Evita recursión infinita en `/`.

### Criterios de éxito

- [ ] `adapters/filesystem/__init__.py` exporta `ADAPTER_NAME = "filesystem"` y `handle`
- [ ] `eal filesystem tree --path . --depth 2` → estructura jerárquica con nombres, tipos, tamaños
- [ ] `eal filesystem find --path . --pattern "*.py"` → lista de archivos con path, size, modified
- [ ] `eal filesystem size --path .` → resumen por directorio con total
- [ ] Path inexistente → `ok: false`, no lanza excepción
- [ ] `--depth 10` → cap automático a 5, sin error
- [ ] Métricas `chars_processed`, `chars_returned`, `time_ms` en toda respuesta
- [ ] `handle()` nunca lanza excepciones
- [ ] Cero dependencias externas

---

## Task 10b — Bildung extendido (Ejecutor B)

### Qué

Agregar dos comandos al adapter `bildung`: `search` y `dependencias`.
El adapter bildung es el más usado por agentes pero está limitado a 5 comandos
de parseo YAML↔MD. Estos dos nuevos comandos lo convierten en la interfaz
primaria de navegación de Bildung para agentes.

### Por qué ahora

1. **Es el adapter fundacional.** Construido en el Ciclo 2, refactorizado en el
   Ciclo 3. 337 líneas. Es el punto de entrada de todo agente que necesita
   entender Bildung.
2. **`search` semántico sobre archivos reales.** bildung-context busca en
   SQLite (entradas de bitácora). bildung busca en archivos MD/YAML en disco.
   Son dominios complementarios — no redundantes.
3. **`dependencias` desbloquea navegación cross-proyecto.** Un agente nuevo
   puede preguntar `eal bildung dependencias --path self-driving-lab` y saber
   qué otros proyectos dependen de este o de los que este depende.

### Comando: `search`

```yaml
# eal bildung search --path ../.. --query "hipotesis EAL" --limit 5
query: "hipotesis EAL"
results:
  - path: 02-eal/hipotesis.md
    score: 3
    snippet: "El principal cuello de botella de los agentes de IA..."
  - path: 01-branch-lab/hipotesis.md
    score: 2
    snippet: "..."
  - path: 03-delegacion/hipotesis.md
    score: 1
    snippet: "..."
```

**Implementación:**
- Usar `os.walk()` sobre `--path` (default: directorio del proyecto)
- Buscar en archivos `.md`, `.yaml`, `.py`
- Tokenizar la query en términos (casefold + split)
- Scoring: suma de ocurrencias de cada término por archivo
- Devolver top N resultados con snippet (primeros 120 chars del primer match)
- `chars_processed`: suma de tamaños de todos los archivos leídos

### Comando: `dependencias`

```yaml
# eal bildung dependencias --path ../../..
# (desde self-driving-lab, busca todos los proyectos)
proyecto: self-driving-lab
dependencias_directas:
  - 01-branch-lab
  - 03-delegacion
dependientes:
  []  # nadie depende de self-driving-lab todavía
proyectos_hermanos:
  - nombre: 01-branch-lab
    hipotesis: "Los agentes pueden operar con autonomía dentro de restricciones"
  - nombre: 03-delegacion
    hipotesis: "La delegación ontológica reduce la entropía de contexto"
```

**Implementación:**
- Parsear `hipotesis.md` de cada proyecto en `workdir/` bajo `--path`
- Extraer sección `## Dependencias` (ya existe el parser en `hipotesis_to_yaml`)
- Construir grafo dirigido: A → B si A menciona a B en sus dependencias
- `dependencias_directas`: proyectos que este proyecto lista como dependencia
- `dependientes`: proyectos que listan a este proyecto como dependencia (reverse lookup)
- `proyectos_hermanos`: todos los proyectos en el mismo workdir con su hipótesis

### Reglas compartidas

- **NO** modificar los comandos existentes (`yaml2md`, `hipotesis`, `hipotesis-json`, `bitacora`, `proyecto`).
- **NO** modificar la lógica de parseo existente. Los nuevos comandos usan `os.walk()`, `re.search()`, y las funciones engine (`read_file`, `extract_md_section`, `extract_md_list`).
- **NO** importar bildung-context. bildung accede a archivos en disco, no a SQLite.
- **NO** crear archivos nuevos. Todo va en `adapters/bildung/adapter.py`.
- **Métricas vinculantes.** `chars_processed` = bytes leídos de disco. `chars_returned` = len del JSON de respuesta.

### Criterios de éxito

- [ ] `eal bildung search --path ../.. --query "EAL entropia" --limit 3` → resultados con path, score, snippet
- [ ] `eal bildung search --query "inexistente123xyz"` → `ok: true`, lista vacía (no error)
- [ ] `eal bildung dependencias --path ../..` → `dependencias_directas`, `dependientes`, `proyectos_hermanos`
- [ ] `dependencias` detecta referencias en la sección `## Dependencias` de hipotesis.md
- [ ] Path inexistente → `ok: false`, no lanza excepción
- [ ] Los 5 comandos existentes (`yaml2md`, `hipotesis`, `bitacora`, `proyecto`, `hipotesis-json`) siguen funcionando igual
- [ ] Métricas `chars_processed`, `chars_returned`, `time_ms` en toda respuesta
- [ ] `handle()` nunca lanza excepciones
- [ ] Cero dependencias externas. Solo stdlib + engine (ya usado).

---

## Arquitectura de roles

| Rol | Responsable | Tareas |
|---|---|---|
| **Tech Lead** | Vos | Ejecutar Task 8 (saneamiento) → preparar specs para Ejecutores A y B → supervisar ambos → integrar → reportar |
| **Ejecutor A** | Agente | Task 10a: filesystem adapter |
| **Ejecutor B** | Agente | Task 10b: bildung extendido |

**No se usa Diseñador.** Las tareas están especificadas en este brief.

**No se usa Revisor.** Criterios de éxito ejecutables.

---

## Lo que ya existe (no reconstruir)

| Artefacto | Estado |
|---|---|
| `runtime.py` — 344 líneas, `_infer_type` en línea 26 | ✅ Necesita fix de 1 línea (negativos) |
| `.gitignore` — 9 líneas (pycache, data, TASK/REPORT) | ✅ Necesita `*.egg-info/` (1 línea) |
| `adapters/bildung/adapter.py` — 337 líneas, 5 comandos | ✅ Necesita `search` + `dependencias` (~80 líneas) |
| `adapters/bildung/__init__.py` — exporta ADAPTER_NAME + handle | ✅ No se modifica |
| `adapters/engine.py` — 12 funciones | ✅ Solo lectura |
| `adapters/git/`, `adapters/systemd/` | ✅ No se modifican |
| `adapters/bildung-context/` — 6 archivos | ✅ No se modifican |
| `eal/` — paquete pip | ✅ No se modifica |
| `eal-config.yaml` — 4 adapters | ✅ Se agrega `path: adapters/filesystem` |
| `specs/adapter_contract.md` | ✅ Solo lectura |
| `specs/proximos_adapters.md` §3 — spec original de filesystem | ✅ Solo lectura |

---

## Restricciones

- **No escribas código.** Excepto Task 8 (3 fixes manuales). El resto: delegá.
- **No modifiques bildung-context, git, systemd, engine.**
- **No modifiques el paquete pip `eal/`.**
- **No crees nuevos archivos en bildung/.** Todo va en `adapter.py`.
- **Filesystem adapter no usa `engine.run_subprocess()`.** Es el primer adapter post-subprocess.
- **No toques Gobernanza ni Codex.**
- **Máximo 2 iteraciones por Ejecutor.** Tasks 10a y 10b son independientes.
- **`CHANGELOG.md` debe estar listo antes de delegar Tasks 10a/10b** — documenta el baseline pre-expansión.

---

## Documentos de referencia

- `specs/adapter_contract.md` — contrato canónico
- `specs/proximos_adapters.md` §3 — spec original de filesystem
- `specs/roadmap_eal.md` — Fase II, Ciclos 8-10
- `docs/agent_hierarchy.md` — tu rol (Capa 4)
- `docs/agent_constitution.md` — 10 artículos vinculantes
- `docs/reporting_protocol.md` — formato de REPORT.md
- `adapters/bildung/adapter.py` — comandos existentes + `hipotesis_to_yaml()` (parser de dependencias)
- `adapters/systemd/adapter.py` — referencia de adapter con métricas completas
- `adapters/git/adapter.py` — referencia de adapter externo simple
- `runtime.py` línea 26 — `_infer_type()` actual
