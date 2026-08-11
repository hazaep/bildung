# BRIEF — Tech Lead / self-driving-lab (Ciclo 6)

> **De:** Arquitecto (Capa 3)
> **Para:** Tech Lead (Capa 4)
> **Fecha:** Agosto 2026
> **Ciclo:** 6 — Métricas + Validación + Empaquetado

---

## Objetivo

Cerrar el roadmap EAL original. Tres tareas, dos delegaciones a Ejecutor, una validación manual del Tech Lead. Las tareas son secuenciales: el output de la Tarea 1 desbloquea la Tarea 3. La Tarea 2 es independiente.

```
Tarea 1 (Ejecutor)     Tarea 2 (Tech Lead)      Tarea 3 (Ejecutor)
Métricas + Runtime     Validación systemd        Empaquetado
       │                  en RPi4B                    │
       │                     │                        │
       └─────────────────────┴────────────────────────┘
                             │
                      REPORT_TECH_LEAD
```

---

## Tarea 1 — Métricas retroactivas + Runtime fix (Ejecutor)

### Qué

Tres adapters necesitan `chars_processed` + `chars_returned`. Uno tiene bug estructural. El Runtime tiene un bug transversal.

| Archivo | Acción | Riesgo |
|---|---|---|
| `adapters/git/adapter.py` | Agregar `chars_processed` + `chars_returned` a todos los returns | Bajo. ~10 líneas. |
| `adapters/bildung/adapter.py` | Agregar `chars_processed` + `chars_returned` a todos los returns | Bajo. ~8 líneas. |
| `adapters/bildung-context/eal_adapter.py` | Agregar `chars_processed` + `chars_returned`. **Usa clase + closures, no funciones planas.** | Medio. ~10 líneas. |
| `runtime.py` | **(a)** Eliminar `print(f"ERROR: {result['error']}", file=sys.stderr)` de la línea ~139. **(b)** Agregar captura automática de métricas vía bildung-context. | Medio. Primera modificación del Runtime desde Ciclo 3. |

### Reglas para bildung-context

El adapter bildung-context usa una clase `BildungContextAdapter` con closures `_ok`/`_err` dentro de `handle()`. El patrón es **distinto** a git y systemd (que usan funciones planas), pero igualmente válido: también exporta `ADAPTER_NAME` + `handle()`.

**Lo que SÍ debe hacer:**
- Agregar `chars_processed` y `chars_returned` a las closures `_ok` y `_err`.
- Para `write`: `chars_processed = len(entrada) + len(json.dumps(metadata or {}))`. `chars_returned = len(json.dumps(result))`.
- Para `read`: `chars_processed = bytes retornados por SQLite (estimar desde len(json.dumps(entries)))`. `chars_returned = len(json.dumps(data))`.
- Para `stats`: `chars_processed = chars_returned` (son datos ya estructurados).
- Para `search`: similar a `read`.
- Para `import`: `chars_processed = bytes leídos del filesystem`. `chars_returned = len(json.dumps({"importadas": count}))`.

**Lo que NO debe hacer:**
- **NO** cambiar la estructura de clases. `BildungContextAdapter` se queda como está.
- **NO** refactorizar a funciones planas como git/systemd.
- **NO** tocar `db.py`, `models.py`, `parser_bridge.py`, `cli.py`.

### Reglas para el Runtime

**Fix del ERROR duplicado:**
```python
# Línea ~139, reemplazar:
    if not result.get("ok", False):
        if result.get("error"):
            print(f"ERROR: {result['error']}", file=sys.stderr)
        sys.exit(1)

# Por:
    if not result.get("ok", False):
        sys.exit(1)
```
El adapter ya devuelve `error` dentro del JSON. El Runtime no debe repetirlo.

**Captura automática de métricas:**
Después de ejecutar `handle(command, args)`, el Runtime debe escribir las métricas a bildung-context:

```python
# Pseudocódigo:
metrics = result.get("metrics", {})
if "bildung-context" in adapters:
    adapters["bildung-context"]("write", {
        "proyecto": "self-driving-lab",
        "tipo": "metrica",
        "entrada": json.dumps({
            "domain": domain,
            "command": command,
            "ok": result.get("ok"),
            "metrics": metrics,
        }),
    })
```

Esto debe ser **silencioso**: si bildung-context falla, la métrica se pierde pero el comando principal no se interrumpe.

### Criterios de éxito

- [ ] `eal git status` → métricas incluyen `chars_processed` y `chars_returned` (números enteros > 0)
- [ ] `eal bildung proyecto self-driving-lab` → ídem
- [ ] `eal bildung-context stats --proyecto self-driving-lab` → ídem
- [ ] `eal systemd status --service nginx` → sin `ERROR:` duplicado en stderr
- [ ] Todas las invocaciones subsecuentes de `eal` escriben entrada en bildung-context
- [ ] `eal bildung-context read --proyecto self-driving-lab --tipo metrica` → devuelve las métricas capturadas

---

## Tarea 2 — Validación systemd real en RPi4B (Tech Lead)

### Qué# BRIEF — Tech Lead / self-driving-lab (Ciclo 6)

> **De:** Arquitecto (Capa 3)
> **Para:** Tech Lead (Capa 4)
> **Fecha:** Agosto 2026
> **Ciclo:** 6 — Métricas + Validación + Empaquetado

---

## Objetivo

Cerrar el roadmap EAL original. Tres tareas, dos delegaciones a Ejecutor, una validación manual del Tech Lead. Las tareas son secuenciales: el output de la Tarea 1 desbloquea la Tarea 3. La Tarea 2 es independiente.

```
Tarea 1 (Ejecutor)     Tarea 2 (Tech Lead)      Tarea 3 (Ejecutor)
Métricas + Runtime     Validación systemd        Empaquetado
       │                  en RPi4B                    │
       │                     │                        │
       └─────────────────────┴────────────────────────┘
                             │
                      REPORT_TECH_LEAD
```

---

## Tarea 1 — Métricas retroactivas + Runtime fix (Ejecutor)

### Qué

Tres adapters necesitan `chars_processed` + `chars_returned`. Uno tiene bug estructural. El Runtime tiene un bug transversal.

| Archivo | Acción | Riesgo |
|---|---|---|
| `adapters/git/adapter.py` | Agregar `chars_processed` + `chars_returned` a todos los returns | Bajo. ~10 líneas. |
| `adapters/bildung/adapter.py` | Agregar `chars_processed` + `chars_returned` a todos los returns | Bajo. ~8 líneas. |
| `adapters/bildung-context/eal_adapter.py` | Agregar `chars_processed` + `chars_returned`. **Usa clase + closures, no funciones planas.** | Medio. ~10 líneas. |
| `runtime.py` | **(a)** Eliminar `print(f"ERROR: {result['error']}", file=sys.stderr)` de la línea ~139. **(b)** Agregar captura automática de métricas vía bildung-context. | Medio. Primera modificación del Runtime desde Ciclo 3. |

### Reglas para bildung-context

El adapter bildung-context usa una clase `BildungContextAdapter` con closures `_ok`/`_err` dentro de `handle()`. El patrón es **distinto** a git y systemd (que usan funciones planas), pero igualmente válido: también exporta `ADAPTER_NAME` + `handle()`.

**Lo que SÍ debe hacer:**
- Agregar `chars_processed` y `chars_returned` a las closures `_ok` y `_err`.
- Para `write`: `chars_processed = len(entrada) + len(json.dumps(metadata or {}))`. `chars_returned = len(json.dumps(result))`.
- Para `read`: `chars_processed = bytes retornados por SQLite (estimar desde len(json.dumps(entries)))`. `chars_returned = len(json.dumps(data))`.
- Para `stats`: `chars_processed = chars_returned` (son datos ya estructurados).
- Para `search`: similar a `read`.
- Para `import`: `chars_processed = bytes leídos del filesystem`. `chars_returned = len(json.dumps({"importadas": count}))`.

**Lo que NO debe hacer:**
- **NO** cambiar la estructura de clases. `BildungContextAdapter` se queda como está.
- **NO** refactorizar a funciones planas como git/systemd.
- **NO** tocar `db.py`, `models.py`, `parser_bridge.py`, `cli.py`.

### Reglas para el Runtime

**Fix del ERROR duplicado:**
```python
# Línea ~139, reemplazar:
    if not result.get("ok", False):
        if result.get("error"):
            print(f"ERROR: {result['error']}", file=sys.stderr)
        sys.exit(1)

# Por:
    if not result.get("ok", False):
        sys.exit(1)
```
El adapter ya devuelve `error` dentro del JSON. El Runtime no debe repetirlo.

**Captura automática de métricas:**
Después de ejecutar `handle(command, args)`, el Runtime debe escribir las métricas a bildung-context:

```python
# Pseudocódigo:
metrics = result.get("metrics", {})
if "bildung-context" in adapters:
    adapters["bildung-context"]("write", {
        "proyecto": "self-driving-lab",
        "tipo": "metrica",
        "entrada": json.dumps({
            "domain": domain,
            "command": command,
            "ok": result.get("ok"),
            "metrics": metrics,
        }),
    })
```

Esto debe ser **silencioso**: si bildung-context falla, la métrica se pierde pero el comando principal no se interrumpe.

### Criterios de éxito

- [ ] `eal git status` → métricas incluyen `chars_processed` y `chars_returned` (números enteros > 0)
- [ ] `eal bildung proyecto self-driving-lab` → ídem
- [ ] `eal bildung-context stats --proyecto self-driving-lab` → ídem
- [ ] `eal systemd status --service nginx` → sin `ERROR:` duplicado en stderr
- [ ] Todas las invocaciones subsecuentes de `eal` escriben entrada en bildung-context
- [ ] `eal bildung-context read --proyecto self-driving-lab --tipo metrica` → devuelve las métricas capturadas

---

## Tarea 2 — Validación systemd real en RPi4B (Tech Lead)

### Qué

El adapter systemd se probó solo con graceful degradation (PID 1 = tini). Esta tarea es la **primera ejecución con systemd real**.

### Cómo

El Tech Lead accede al RPi4B vía SSH y ejecuta: solicitar direccion a gobernanza

```bash
# 1. Verificar que systemd está operativo
systemctl --version && echo "PID 1: $(cat /proc/1/comm)"

# 2. Listar servicios activos para elegir targets de prueba
systemctl list-units --type=service --state=running | head -20

# 3. Ejecutar los 3 comandos del adapter systemd contra servicios reales
eal systemd status --service n8n
eal systemd status --service docker    # si Docker está instalado
eal systemd status --service ssh

# 4. Errores reales (journalctl con datos)
eal systemd errors --service n8n --since "24h"
eal systemd errors --service docker --since "24h"

# 5. Logs reales
eal systemd logs --service ssh --since "1h" --limit 10
```

### Qué medir

Por cada comando, registrar:

| Métrica | Ejemplo |
|---|---|
| `chars_processed` | 48231 |
| `chars_returned` | 2147 |
| **Ratio de compresión** | 22.5:1 |
| `time_ms` | 47 |

### Criterios de éxito

- [ ] Al menos 3 servicios devuelven `ok: true` con `active`, `enabled`, `pid`, `memory` poblados
- [ ] `errors` devuelve entradas reales si hay errores en el journal (o `ok: true` con lista vacía si no hay)
- [ ] `logs` devuelve entradas con timestamps, level, unit, message correctos
- [ ] Ratio de compresión documentado para al menos 3 comandos
- [ ] Ningún `handle()` lanza excepción

---

## Tarea 3 — Empaquetado (Ejecutor)

### Qué

La EAL debe ser instalable con `pip install eal` desde cualquier entorno.

### Estructura objetivo

```
02-eal/
├── pyproject.toml          ← nuevo
├── setup.cfg               ← nuevo (o solo pyproject.toml)
├── eal/                     ← nuevo: paquete Python
│   ├── __init__.py
│   ├── runtime.py           ← renombrado/movido del actual
│   ├── engine.py            ← movido de adapters/engine.py
│   └── adapters/            ← movidos aquí
│       ├── bildung/
│       ├── bildung-context/
│       ├── git/
│       └── systemd/
├── adapters/                ← se preserva para desarrollo local
├── eal-config.yaml          ← se preserva para desarrollo local
└── specs/                   ← no se empaqueta
```

### Entry point

```bash
# Después de pip install:
$ eal git status
$ eal systemd status --service nginx
```

El entry point se define en `pyproject.toml`:
```toml
[project.scripts]
eal = "eal.runtime:main"
```

### Reglas

- **NO** modificar `runtime.py` (ya se modificó en Tarea 1). Solo moverlo.
- **NO** modificar los adapters. Solo reubicarlos.
- **NO** modificar `engine.py`. Solo moverlo de `adapters/engine.py` a `eal/engine.py`.
- El `eal-config.yaml` del sistema instalado debe buscar adapters en `~/.bildung/eal/adapters/` por defecto.
- La instalación debe funcionar en modo desarrollo también: `pip install -e .`

### Instalación en RPi4B (post-empaquetado)

```bash
# En el RPi4B:
cd ~/bildung/Lab/3-dev/self-driving-lab/workdir/02-eal
pip install -e .
# Luego:
cd /tmp && eal systemd status --service n8n
```

### Criterios de éxito

- [ ] `pip install -e .` desde el directorio de la EAL completa sin errores
- [ ] `eal` está disponible en el PATH (p. ej. `which eal` resuelve)
- [ ] `eal systemd status --service nginx` funciona desde un directorio cualquiera
- [ ] `eal bildung proyecto self-driving-lab` funciona
- [ ] `eal bildung-context stats --proyecto self-driving-lab` funciona
- [ ] `eal git status` funciona (dentro de un repo git)
- [ ] `eal-config.yaml` se lee desde el directorio de instalación o `~/.bildung/eal/`
- [ ] Cero dependencias externas nuevas (solo stdlib + PyYAML que ya es dependencia)

---

## Arquitectura de roles

| Rol | Responsable | Tareas |
|---|---|---|
| **Tech Lead** | Vos | Recibir brief → preparar specs para Ejecutor 1 → supervisar → preparar specs para Ejecutor 2 → ejecutar Tarea 2 (RPi4B) → integrar → reportar |
| **Ejecutor 1** | Agente | Tarea 1: métricas retroactivas + Runtime fix + captura automática |
| **Ejecutor 2** | Agente | Tarea 3: empaquetado pip install |

**No se usa Diseñador.** Las tareas son mecánicas, con specs explícitas en este mismo brief.

**No se usa Revisor.** El Tech Lead valida directamente contra los criterios de éxito de cada tarea.

---

## Lo que ya existe (no reconstruir)

| Artefacto | Estado |
|---|---|
| `adapters/systemd/` — adapter con métricas completas | ✅ Ya tiene `chars_processed` + `chars_returned` |
| `adapters/engine.py` — 12 funciones, 225 líneas | ✅ No se modifica |
| `adapters/bildung/adapter.py` — adapter compliant | ✅ Necesita ~8 líneas de métricas |
| `adapters/git/adapter.py` — adapter compliant | ✅ Necesita ~10 líneas de métricas |
| `adapters/bildung-context/eal_adapter.py` — adapter compliant (clase) | ✅ Necesita ~10 líneas de métricas |
| `runtime.py` — 142 líneas, discover + dispatch | ✅ Necesita fix (2 líneas) + captura (~8 líneas) |
| `eal-config.yaml` — 4 adapters registrados | ✅ No se modifica |
| `specs/adapter_contract.md` — contrato canónico | ✅ Solo lectura |
| `specs/roadmap_eal.md` — referencia | ✅ Solo lectura |
| Acceso SSH al RPi4B | ✅ Configurado, carpeta sincronizada |

---

## Restricciones

- **No escribas código.** Descomponé, prepará specs, delegá, supervisá.
- **No modifiques engine, bildung-context/db.py, bildung-context/models.py.**
- **No modifiques los adapters systemd ni git más allá de las métricas.**
- **No toques Gobernanza ni Codex.**
- **Máximo 2 iteraciones por Ejecutor.** Si después de 2 iteraciones no funciona, archiva y escala.
- **El orden importa.** Tarea 1 → Tarea 3. La Tarea 2 puede ejecutarse en paralelo, pero la evidencia es más valiosa con todas las métricas ya implementadas.

---

## Documentos de referencia

- `specs/adapter_contract.md` — contrato canónico + métricas §3
- `specs/roadmap_eal.md` — Ciclo 6 definición original
- `docs/agent_hierarchy.md` — tu rol (Capa 4)
- `docs/agent_constitution.md` — 10 artículos vinculantes
- `docs/reporting_protocol.md` — formato de REPORT.md
- `docs/descubrimientos.md` — evidencia de ciclos anteriores
- `adapters/systemd/adapter.py` — referencia de métricas completas
- `adapters/bildung-context/eal_adapter.py` — referencia del patrón clase + closures


El adapter systemd se probó solo con graceful degradation (PID 1 = tini). Esta tarea es la **primera ejecución con systemd real**.

### Cómo

El Tech Lead accede al RPi4B vía SSH y ejecuta:

```bash
# 1. Verificar que systemd está operativo
systemctl --version && echo "PID 1: $(cat /proc/1/comm)"

# 2. Listar servicios activos para elegir targets de prueba
systemctl list-units --type=service --state=running | head -20

# 3. Ejecutar los 3 comandos del adapter systemd contra servicios reales
eal systemd status --service n8n
eal systemd status --service docker    # si Docker está instalado
eal systemd status --service ssh

# 4. Errores reales (journalctl con datos)
eal systemd errors --service n8n --since "24h"
eal systemd errors --service docker --since "24h"

# 5. Logs reales
eal systemd logs --service ssh --since "1h" --limit 10
```

### Qué medir

Por cada comando, registrar:

| Métrica | Ejemplo |
|---|---|
| `chars_processed` | 48231 |
| `chars_returned` | 2147 |
| **Ratio de compresión** | 22.5:1 |
| `time_ms` | 47 |

### Criterios de éxito

- [ ] Al menos 3 servicios devuelven `ok: true` con `active`, `enabled`, `pid`, `memory` poblados
- [ ] `errors` devuelve entradas reales si hay errores en el journal (o `ok: true` con lista vacía si no hay)
- [ ] `logs` devuelve entradas con timestamps, level, unit, message correctos
- [ ] Ratio de compresión documentado para al menos 3 comandos
- [ ] Ningún `handle()` lanza excepción

---

## Tarea 3 — Empaquetado (Ejecutor)

### Qué

La EAL debe ser instalable con `pip install eal` desde cualquier entorno.

### Estructura objetivo

```
02-eal/
├── pyproject.toml          ← nuevo
├── setup.cfg               ← nuevo (o solo pyproject.toml)
├── eal/                     ← nuevo: paquete Python
│   ├── __init__.py
│   ├── runtime.py           ← renombrado/movido del actual
│   ├── engine.py            ← movido de adapters/engine.py
│   └── adapters/            ← movidos aquí
│       ├── bildung/
│       ├── bildung-context/
│       ├── git/
│       └── systemd/
├── adapters/                ← se preserva para desarrollo local
├── eal-config.yaml          ← se preserva para desarrollo local
└── specs/                   ← no se empaqueta
```

### Entry point

```bash
# Después de pip install:
$ eal git status
$ eal systemd status --service nginx
```

El entry point se define en `pyproject.toml`:
```toml
[project.scripts]
eal = "eal.runtime:main"
```

### Reglas

- **NO** modificar `runtime.py` (ya se modificó en Tarea 1). Solo moverlo.
- **NO** modificar los adapters. Solo reubicarlos.
- **NO** modificar `engine.py`. Solo moverlo de `adapters/engine.py` a `eal/engine.py`.
- El `eal-config.yaml` del sistema instalado debe buscar adapters en `~/.bildung/eal/adapters/` por defecto.
- La instalación debe funcionar en modo desarrollo también: `pip install -e .`

### Instalación en RPi4B (post-empaquetado)

```bash
# En el RPi4B:
cd ~/bildung/Lab/3-dev/self-driving-lab/workdir/02-eal
pip install -e .
# Luego:
cd /tmp && eal systemd status --service n8n
```

### Criterios de éxito

- [ ] `pip install -e .` desde el directorio de la EAL completa sin errores
- [ ] `eal` está disponible en el PATH (p. ej. `which eal` resuelve)
- [ ] `eal systemd status --service nginx` funciona desde un directorio cualquiera
- [ ] `eal bildung proyecto self-driving-lab` funciona
- [ ] `eal bildung-context stats --proyecto self-driving-lab` funciona
- [ ] `eal git status` funciona (dentro de un repo git)
- [ ] `eal-config.yaml` se lee desde el directorio de instalación o `~/.bildung/eal/`
- [ ] Cero dependencias externas nuevas (solo stdlib + PyYAML que ya es dependencia)

---

## Arquitectura de roles

| Rol | Responsable | Tareas |
|---|---|---|
| **Tech Lead** | Vos | Recibir brief → preparar specs para Ejecutor 1 → supervisar → preparar specs para Ejecutor 2 → ejecutar Tarea 2 (RPi4B) → integrar → reportar |
| **Ejecutor 1** | Agente | Tarea 1: métricas retroactivas + Runtime fix + captura automática |
| **Ejecutor 2** | Agente | Tarea 3: empaquetado pip install |

**No se usa Diseñador.** Las tareas son mecánicas, con specs explícitas en este mismo brief.

**No se usa Revisor.** El Tech Lead valida directamente contra los criterios de éxito de cada tarea.

---

## Lo que ya existe (no reconstruir)

| Artefacto                                                             | Estado                                          |
| --------------------------------------------------------------------- | ----------------------------------------------- |
| `adapters/systemd/` — adapter con métricas completas                  | ✅ Ya tiene `chars_processed` + `chars_returned` |
| `adapters/engine.py` — 12 funciones, 225 líneas                       | ✅ No se modifica                                |
| `adapters/bildung/adapter.py` — adapter compliant                     | ✅ Necesita ~8 líneas de métricas                |
| `adapters/git/adapter.py` — adapter compliant                         | ✅ Necesita ~10 líneas de métricas               |
| `adapters/bildung-context/eal_adapter.py` — adapter compliant (clase) | ✅ Necesita ~10 líneas de métricas               |
| `runtime.py` — 142 líneas, discover + dispatch                        | ✅ Necesita fix (2 líneas) + captura (~8 líneas) |
| `eal-config.yaml` — 4 adapters registrados                            | ✅ No se modifica                                |
| `specs/adapter_contract.md` — contrato canónico                       | ✅ Solo lectura                                  |
| `specs/roadmap_eal.md` — referencia                                   | ✅ Solo lectura                                  |
| Acceso SSH al RPi4B                                                   | ✅ Configurado, carpeta sincronizada             |

---

## Restricciones

- **No escribas código.** Descomponé, prepará specs, delegá, supervisá.
- **No modifiques engine, bildung-context/db.py, bildung-context/models.py.**
- **No modifiques los adapters systemd ni git más allá de las métricas.**
- **No toques Gobernanza ni Codex.**
- **Máximo 2 iteraciones por Ejecutor.** Si después de 2 iteraciones no funciona, archiva y escala.
- **El orden importa.** Tarea 1 → Tarea 3. La Tarea 2 puede ejecutarse en paralelo, pero la evidencia es más valiosa con todas las métricas ya implementadas.

---

## Documentos de referencia

- `specs/adapter_contract.md` — contrato canónico + métricas §3
- `specs/roadmap_eal.md` — Ciclo 6 definición original
- `docs/agent_hierarchy.md` — tu rol (Capa 4)
- `docs/agent_constitution.md` — 10 artículos vinculantes
- `docs/reporting_protocol.md` — formato de REPORT.md
- `docs/descubrimientos.md` — evidencia de ciclos anteriores
- `adapters/systemd/adapter.py` — referencia de métricas completas
- `adapters/bildung-context/eal_adapter.py` — referencia del patrón clase + closures
