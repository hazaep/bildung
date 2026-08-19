# BRIEF — Tech Lead / self-driving-lab (Ciclo 7)

> **De:** Arquitecto (Capa 3)
> **Para:** Tech Lead (Capa 4)
> **Fecha:** Agosto 2026
> **Ciclo:** 7 — Runtime Fix + Telemetry (v3: Runtime built-in + JSONL)

---

## Objetivo

Primer ciclo post-roadmap. Una delegación con dos sub-tareas para un solo Ejecutor. Consolidar deuda técnica estructural del Runtime y cerrar el loop de observabilidad — con telemetry como capacidad interna del orquestador, no como adapter ni como comando delegado.

```
Ejecutor
  ├── Sub-tarea A: Runtime type coercion + --limit fix (~25 líneas)
  └── Sub-tarea B: Telemetry como dominio built-in + JSONL (~35 líneas)
              │
       REPORT_TECH_LEAD
```

**Una sola delegación.** Ambas sub-tareas modifican `runtime.py`. Ejecutarlas juntas evita conflictos de merge.

---

## Nota arquitectónica — diseño de tres iteraciones

Este brief es la v3 de un diseño que pasó por tres iteraciones en una sola
conversación con Gobernanza. Documentado para trazabilidad:

| Versión | Diseño | Problema | Veredicto |
|---|---|---|---|
| **v1** | Adapter `adapters/telemetry/` que importa bildung-context | Viola `adapter_contract.md` §2.2 regla #4: "autocontenido" | ❌ |
| **v2** | Comando `telemetry` en bildung-context | bildung-context se vuelve "especial". Acoplamiento Runtime→adapter frágil. bildung-context no es backend de telemetría. | ❌ |
| **v3** | Runtime built-in + JSONL | — | ✅ |

**Principio:** Telemetry no es un dominio externo como git o systemd. Es la
capacidad del orquestador de observarse a sí mismo. Así como el Runtime conoce
`eal-config.yaml` sin un "adapter de configuración", debe conocer `telemetry`
sin un "adapter de telemetría".

---

## Sub-tarea A — Runtime type coercion + `--limit` fix

### Qué

| Archivo | Acción | Riesgo |
|---|---|---|
| `runtime.py` — `_parse_cli_args()` | Agregar `_infer_type()` para inferir `int`, `float`, `bool` de valores CLI | Medio. Cambia el contrato implícito "args son strings". |
| `adapters/systemd/adapter.py` | `_handle_logs()`: convertir `limit` a `int` explícitamente | Bajo. ~2 líneas. |

### Por qué es estructural

Este bug afecta a **todos los adapters actuales y futuros.** Cada adapter que
recibe un argumento numérico (`--limit`, `--limite`, `--desde`, `--max`) tiene
que hacer conversión manual de tipos. Si se agregan 5 adapters más, cada uno
hereda el bug. El fix en el Runtime lo resuelve para siempre.

### El problema

```python
# _parse_cli_args() actual — todo es string:
args["limit"] = "20"        # string
args["limite"] = "10"       # string

# systemd compara sin fallar (pero incorrecto):
if len(entries) >= limit:   # int >= str: Python no lanza error, lógica rota

# bildung-context compara y falla:
if limite < 1:              # str < int → TypeError
```

### La solución — Runtime

Agregar `_infer_type()` y usarlo en `_parse_cli_args()`:

```python
def _infer_type(value: str):
    """Inferir tipo de un valor CLI. int, float, bool. Default: str."""
    if value.isdigit():
        return int(value)
    # Float: dígitos con un punto decimal
    cleaned = value.replace('.', '', 1).lstrip('-')
    if cleaned.isdigit() and value.count('.') <= 1 and value.count('-') <= 1:
        try:
            return float(value)
        except ValueError:
            pass
    if value.lower() in ("true", "false"):
        return value.lower() == "true"
    return value
```

### La solución — systemd adapter

```python
# _handle_logs, línea ~232:
limit = int(args.get("limit", 20))
```

Con el fix del Runtime, `args.get("limit", 20)` ya devuelve `int(20)`. Pero
el `int()` explícito es defensivo — si alguien invoca el adapter sin pasar
por el Runtime, sigue funcionando.

### Reglas

- **NO** cambiar la firma de `_parse_cli_args`. Sigue devolviendo `(str, str, dict)`.
- **NO** modificar otros adapters. Solo systemd.
- **NO** tocar `_discover_adapters()` ni la sección de captura de métricas (se modifica en Tarea B).

### Criterios de éxito

- [ ] `python runtime.py bildung-context read --proyecto self-driving-lab --limite 3` → no lanza TypeError
- [ ] `python runtime.py systemd logs --service ssh --limit 5` → `limit` como `int(5)`
- [ ] `python runtime.py systemd status --service nginx` → `service` como `"nginx"` (string)
- [ ] `python runtime.py bildung-context read --proyecto self-driving-lab --tipo metrica --limite 10` → `limite` es `int(10)`
- [ ] `python runtime.py some-adapter --verbose true` → `True` (bool)
- [ ] `_infer_type()` no modifica strings no numéricos (`"24 hours ago"` → `"24 hours ago"`)
- [ ] Ningún adapter existente rompe por el cambio de tipado

---

## Sub-tarea B — Telemetry como dominio built-in del Runtime + JSONL

### Qué

Tres cambios en `runtime.py`. Ningún archivo nuevo. Ningún adapter modificado.

| Cambio | Descripción | Líneas |
|---|---|---|
| Captura a JSONL | Reemplazar captura vía bildung-context por append a `~/.bildung/eal/metrics.jsonl` | ~15 líneas |
| Dominio `telemetry` | Manejar `domain == "telemetry"` internamente sin delegar a adapters | ~25 líneas |
| Eliminar captura vieja | Quitar bloque `try: if "bildung-context" in adapters...` (líneas 133-147 actuales) | -14 líneas |

### Captura pasiva a JSONL

```python
import os
from pathlib import Path
from datetime import datetime, timezone

METRICS_DIR = Path.home() / ".bildung" / "eal"
METRICS_FILE = METRICS_DIR / "metrics.jsonl"

def _capture_metric(domain: str, command: str, result: dict) -> None:
    """Append una línea JSON al archivo de métricas. Nunca lanza excepción."""
    try:
        METRICS_DIR.mkdir(parents=True, exist_ok=True)
        entry = {
            "timestamp": datetime.now(timezone.utc).isoformat(),
            "domain": domain,
            "command": command,
            "ok": result.get("ok", False),
            "metrics": result.get("metrics", {}),
        }
        with open(METRICS_FILE, "a") as f:
            f.write(json.dumps(entry) + "\n")
    except Exception:
        pass  # Silencioso: si el disco falla, no interrumpir el comando
```

Se llama en `main()` justo después de `result = handle(command, args)`:

```python
result = handle(command, args)
_capture_metric(domain, command, result)  # ← pasivo, reemplaza bloque bildung-context
```

### Dominio `telemetry` built-in

En `main()`, antes de buscar en `adapters`:

```python
domain, command, args = _parse_cli_args(sys.argv[1:])

# Dominio interno: telemetry — no delegar a adapters
if domain == "telemetry":
    result = _handle_telemetry(command, args)
    print(json.dumps(result, indent=2, ensure_ascii=False))
    sys.exit(0 if result.get("ok") else 1)
```

Las funciones de telemetry:

```python
def _handle_telemetry(command: str, args: dict) -> dict:
    """Dominio telemetry: agrega métricas del archivo JSONL."""
    start = time.time()
    
    def _ok(data, cp=0, cr=0):
        return {"ok": True, "data": data, "error": None,
                "metrics": {"time_ms": int((time.time() - start) * 1000),
                            "chars_processed": cp, "chars_returned": cr}}
    def _err(msg):
        return {"ok": False, "data": None, "error": msg,
                "metrics": {"time_ms": int((time.time() - start) * 1000),
                            "chars_processed": 0, "chars_returned": 0}}
    
    try:
        entries = _read_metrics()
        cp = sum(len(json.dumps(e)) for e in entries)  # chars leídos
        
        if command == "summary":
            return _telemetry_summary(entries, cp)
        elif command == "ratios":
            return _telemetry_ratios(entries, args, cp)
        elif command == "recent":
            return _telemetry_recent(entries, args, cp)
        else:
            return _err(f"Unknown telemetry command: {command}")
    except Exception as e:
        return _err(str(e))

def _read_metrics() -> list[dict]:
    """Lee todas las líneas del archivo JSONL. Si no existe, lista vacía."""
    if not METRICS_FILE.exists():
        return []
    entries = []
    with open(METRICS_FILE) as f:
        for line in f:
            line = line.strip()
            if line:
                try:
                    entries.append(json.loads(line))
                except json.JSONDecodeError:
                    pass
    return entries
```

### Modelo canónico

```yaml
# eal telemetry summary
total_commands: 148
by_domain:
  systemd: 89
  git: 32
  bildung: 15
  bildung-context: 12
by_status:
  ok: 120
  fail: 28
avg_time_ms: 52

# eal telemetry ratios --domain systemd
domain: systemd
ratios:
  - command: logs
    count: 34
    avg_chars_in: 40556
    avg_chars_out: 3757
    ratio: 10.8
  - command: status
    count: 42
    avg_chars_in: 142
    avg_chars_out: 136
    ratio: 1.04
  - command: errors
    count: 13
    avg_chars_in: 15
    avg_chars_out: 14
    ratio: 1.07

# eal telemetry recent --limit 3
recent:
  - timestamp: "2026-08-10T22:15:00+00:00"
    domain: systemd
    command: status
    ok: true
    time_ms: 47
  - timestamp: "2026-08-10T22:14:55+00:00"
    domain: git
    command: status
    ok: true
    time_ms: 32
  - timestamp: "2026-08-10T22:14:30+00:00"
    domain: bildung-context
    command: read
    ok: true
    time_ms: 2
```

### Dónde vive cada cosa

```
~/.bildung/eal/
├── metrics.jsonl          ← archivo de métricas (nuevo)
├── config.yaml            ← configuración de adapters (existente)
└── adapters/              ← adapters instalados (existente)
```

### Reglas

- **NO** crear directorio `adapters/telemetry/`. No es un adapter.
- **NO** modificar bildung-context. Vuelve a ser solo documentación.
- **NO** modificar engine.py, bildung adapter, git adapter, systemd adapter.
- **NO** modificar `eal/` (paquete pip).
- **Captura es pasiva.** Si el archivo JSONL no se puede escribir, el comando principal no se interrumpe.
- **El dominio `telemetry` no aparece en `eal-config.yaml`.** Es built-in.

### Migración desde bildung-context

Las ~20 entradas de métrica en bildung-context (SQLite, tipo `metrica`) son de
prueba. **No se migran.** Se empieza JSONL desde cero. Las entradas viejas en
SQLite quedan como históricas — no se rompe nada, solo no las lee `telemetry`.

Opcional: si el Tech Lead quiere migrarlas, puede escribir un script único que
lea `query_entries(proyecto="self-driving-lab", tipo="metrica")` y las
convierta a JSONL. Pero no es parte del scope del Ciclo 7.

### Criterios de éxito

- [ ] `eal telemetry summary` → `total_commands`, `by_domain`, `by_status`, `avg_time_ms` (números ≥ 0)
- [ ] `eal telemetry ratios --domain systemd` → comandos con `count`, `avg_chars_in`, `avg_chars_out`, `ratio`
- [ ] `eal telemetry recent --limit 3` → 3 entradas más recientes con timestamp ISO 8601
- [ ] `_capture_metric()` escribe una línea JSON por cada invocación de `eal`
- [ ] `metrics.jsonl` se crea automáticamente en `~/.bildung/eal/`
- [ ] Métricas `time_ms`, `chars_processed`, `chars_returned` en toda respuesta de telemetry
- [ ] `telemetry` no aparece en `eal-config.yaml` — es built-in
- [ ] bildung-context no recibe métricas nuevas (las viejas en SQLite se preservan)
- [ ] Cero dependencias externas. Solo stdlib (`json`, `pathlib`, `datetime`).

---

## Arquitectura de roles

| Rol | Responsable | Tareas |
|---|---|---|
| **Tech Lead** | Vos | Recibir brief → preparar specs para Ejecutor → supervisar → integrar → reportar |
| **Ejecutor** | Agente | Sub-tarea A (Runtime + systemd) + Sub-tarea B (telemetry JSONL) |

**No se usa Diseñador.** Las tareas son acotadas y el brief contiene specs.

**No se usa Revisor.** Criterios de éxito ejecutables.

---

## Lo que ya existe (no reconstruir)

| Artefacto | Estado |
|---|---|
| `runtime.py` — 157 líneas, discover + dispatch | ✅ Necesita `_infer_type()` + `_capture_metric()` + `_handle_telemetry()` (~60 líneas total) |
| `adapters/systemd/adapter.py` — 266 líneas, 3 comandos | ✅ Necesita `int(limit)` (~2 líneas) |
| `adapters/engine.py` — 12 funciones, 225 líneas | ✅ No se modifica |
| `adapters/bildung/`, `adapters/git/` | ✅ No se modifican |
| `adapters/bildung-context/` — 6 archivos | ✅ No se modifica. Vuelve a ser solo documentación. |
| `eal/` — paquete pip | ✅ No se modifica |
| `specs/adapter_contract.md` — contrato canónico | ✅ Solo lectura |
| `~/.bildung/eal/` — directorio de configuración | ✅ Ya existe, se agrega `metrics.jsonl` |

---

## Restricciones

- **No escribas código.** Prepará specs, delegá, supervisá.
- **No modifiques engine, bildung adapter, git adapter, bildung-context.**
- **No modifiques el paquete pip `eal/`.**
- **No crees un adapter nuevo para telemetry.** Es built-in del Runtime.
- **No crees un directorio `adapters/telemetry/`.**
- **No toques Gobernanza ni Codex.**
- **Máximo 2 iteraciones.** Si después de 2 no funciona, archiva y escala.

---

## Documentos de referencia

- `specs/adapter_contract.md` — contrato canónico §2.2
- `specs/roadmap_eal.md` — telemetry pospuesto a Ciclo 7
- `docs/agent_hierarchy.md` — tu rol (Capa 4)
- `docs/agent_constitution.md` — 10 artículos vinculantes
- `docs/reporting_protocol.md` — formato de REPORT.md
- `adapters/systemd/adapter.py` — referencia de `_handle_logs()` con `--limit`
- `runtime.py` líneas 80-147 — `_parse_cli_args()` + bloque de captura actual
