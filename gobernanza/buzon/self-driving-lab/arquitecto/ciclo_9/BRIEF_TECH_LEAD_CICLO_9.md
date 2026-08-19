# BRIEF — Tech Lead / self-driving-lab (Ciclo 9)

> **De:** Arquitecto (Capa 3)
> **Para:** Tech Lead (Capa 4)
> **Fecha:** Agosto 2026
> **Ciclo:** 9 — Docker + Telemetry + Auto-testing

---

## Objetivo

Cerrar la Fase II del roadmap. Dos tareas en paralelo. Docker es el último
adapter de infraestructura que falta. Con él, la EAL cubre el stack completo
de Bildung: documentación, base de datos, control de versiones, sistema
operativo, y contenedores. Telemetry build-up y auto-testing convierten al
Runtime en un orquestador auto-consciente.

```
Task A (Ejecutor A)              Task B (Ejecutor B)
Docker adapter                   Telemetry + Test
adapters/docker/                 runtime.py
ps, stats, logs                  --since/--until
       │                         eal test smoke
       │                                │
       └───────────────┬────────────────┘
                       │
                REPORT_TECH_LEAD
```

**Paralelo.** No comparten archivos. Docker crea `adapters/docker/`. Telemetry
y test modifican `runtime.py`. Cero riesgo de conflicto de merge.

---

## Task A — Docker adapter (Ejecutor A)

### Qué

Construir `adapters/docker/` — sexto adapter EAL. Docker entrega JSON nativo
con `--format json`. El adapter es un mapeo de campos, no parseo heurístico.
Es el adapter más limpio del ecosistema después de filesystem.

### Por qué cierra la Fase II

Con docker, la EAL cubre el stack completo de Bildung:

```
bildung (docs)  bildung-context (BD)  git (código)  systemd (OS)  docker (infra)
     ✅              ✅                 ✅            ✅            🔲→✅
```

Un agente puede preguntar:

```bash
eal docker ps                         # ¿qué contenedores corren?
eal docker stats --container n8n      # ¿cuánta RAM/CPU usa n8n?
eal docker logs --container traefik   # ¿qué pasó en las últimas 100 líneas?
eal systemd status --service docker   # ¿docker está corriendo?
eal filesystem tree --path .          # ¿qué archivos hay?
eal bildung search --query "EAL"      # ¿dónde se habla de EAL?
```

**Stack completo. Una interfaz. Cero texto crudo.**

### Modelo canónico

```yaml
# eal docker ps
containers:
  - name: n8n
    image: n8nio/n8n:latest
    status: running
    uptime: "5 days"
    ports: ["5678:5678"]
    health: healthy
  - name: traefik
    image: traefik:v3.0
    status: running
    uptime: "12 days"
    ports: ["80:80", "443:443"]
    health: healthy

# eal docker stats --container n8n
container: n8n
cpu_percent: "2.15%"
memory_usage: "256.4 MB"
memory_limit: "3.8 GB"
memory_percent: "6.59%"
network_rx: "1.2 GB"
network_tx: "450 MB"
pids: 32

# eal docker logs --container traefik --tail 20 --since "1h"
container: traefik
entries:
  - timestamp: "2026-08-11T10:15:00Z"
    message: "192.168.1.1 - - [11/Aug/2026:10:15:00] \"GET /dashboard/ HTTP/2.0\" 200"
  - timestamp: "2026-08-11T10:14:55Z"
    message: "Configuration reloaded successfully"
```

### Arquitectura

```
adapters/docker/
├── __init__.py       ← ADAPTER_NAME = "docker" + handle()
└── adapter.py        ← ps, stats, logs (~200 líneas)
```

### Comandos

| Comando | Args | Implementación | Output |
|---|---|---|---|
| `ps` | `--all` (bool, default false) | `docker ps --format json` o `docker ps -a --format json` | Lista de contenedores con name, image, status, ports, health |
| `stats` | `--container` (requerido) | `docker stats --no-stream --format json` | CPU, memoria, red, PIDs para un contenedor |
| `logs` | `--container` (requerido), `--tail` (default 50), `--since` (opcional) | `docker logs --tail N --since T` | Líneas de log con timestamp inferido |

### Reglas

- **Usar `engine.run_subprocess()`.** Docker no tiene API Python en stdlib. CLI con `--format json` es el camino más limpio.
- **`--format json` siempre.** Nunca parsear output tabular de docker. Si `--format json` no está disponible en la versión de docker, `ok: false` con mensaje.
- **`docker logs` requiere `--tail`.** Sin límite, los logs pueden ser infinitos. Si no se especifica, default 50. Máximo 500.
- **Docker no disponible → `ok: false`** con mensaje descriptivo. Mismo patrón que systemd.
- **Métricas vinculantes.** `time_ms`, `chars_processed`, `chars_returned`.
- **`chars_processed`:** `len(stdout) + len(stderr)` de cada invocación de subprocess.
- **`handle()` nunca lanza excepciones.**

### Criterios de éxito

- [ ] `adapters/docker/__init__.py` exporta `ADAPTER_NAME = "docker"` y `handle`
- [ ] `eal docker ps` → lista de contenedores con name, image, status (datos reales si Docker disponible)
- [ ] `eal docker stats --container n8n` → cpu_percent, memory_usage, memory_percent
- [ ] `eal docker logs --container traefik --tail 10` → 10 entradas de log
- [ ] Docker no disponible → `ok: false`, no lanza excepción
- [ ] Contenedor inexistente → `ok: false`
- [ ] Métricas `chars_processed`, `chars_returned`, `time_ms` en toda respuesta
- [ ] `handle()` nunca lanza excepciones
- [ ] Cero dependencias externas (solo engine + stdlib)

---

## Task B — Telemetry build-up + Auto-testing (Ejecutor B)

### Qué

Dos extensiones del Runtime en `runtime.py`. Mismo archivo, secuenciales
internamente. No modificar `_discover_adapters()` ni `main()`.

| Sub-tarea | Archivo | Acción | Líneas |
|---|---|---|---|
| **B1** — Telemetry `--since`/`--until` | `runtime.py` | Modificar `_telemetry_ratios()` y `_handle_telemetry()` para aceptar filtros temporales | ~30 |
| **B2** — `eal test` smoke | `runtime.py` | Nuevo dominio built-in `test`. `eal test <adapter>` ejecuta smoke test. | ~50 |

### B1 — Telemetry con rango temporal

**Problema actual:** `telemetry ratios` y `telemetry summary` procesan TODAS
las entradas del JSONL. Con 100+ entradas diarias, esto se vuelve ruidoso.
Un agente quiere preguntar "¿qué ratio tuvo systemd en las últimas 24 horas?"

**Solución:** Filtrar entradas por timestamp antes de agregar.

```python
# Nueva función auxiliar
def _filter_by_time(entries, since=None, until=None):
    """Filtra entradas por rango de timestamps ISO 8601."""
    if not since and not until:
        return entries
    result = []
    for e in entries:
        ts = e.get("timestamp", "")
        if since and ts < since:
            continue
        if until and ts > until:
            continue
        result.append(e)
    return result
```

Los comandos `summary`, `ratios`, y `recent` aceptan `--since` y `--until`
como strings ISO 8601 (ej. `"2026-08-10T00:00:00+00:00"`).

```bash
eal telemetry ratios --domain systemd --since "2026-08-10"
eal telemetry summary --since "2026-08-11"
```

**Validación:** `eal telemetry ratios --domain systemd --since "2026-08-10"` →
solo entradas desde esa fecha. Si no hay entradas en el rango, `ok: true` con
ratios vacíos.

### B2 — `eal test` smoke test

**Problema actual:** Con 5 adapters y 25 comandos, validar manualmente que
cada uno cumple el contrato es frágil. El Tech Lead ejecuta comandos uno por
uno y verifica outputs. Eso no escala a 9 adapters.

**Solución:** Dominio built-in `test` en el Runtime. Ejecuta un smoke test
mínimo sobre cualquier adapter registrado.

```bash
eal test bildung       # smoke test del adapter bildung
eal test filesystem    # smoke test del adapter filesystem
eal test all           # smoke test de todos los adapters
```

**Qué verifica `eal test <adapter>`:**

1. `ADAPTER_NAME` es string no vacío
2. `handle` es callable
3. `handle("status", {})` no lanza excepción
4. Respuesta tiene `ok`, `data`, `error`, `metrics`
5. `metrics` tiene `time_ms` (int)
6. Si `ok: true`, `metrics` idealmente tiene `chars_processed` y `chars_returned`

```yaml
# eal test bildung
adapter: bildung
checks:
  - check: ADAPTER_NAME is str
    passed: true
  - check: handle is callable
    passed: true
  - check: handle() does not throw
    passed: true
    time_ms: 42
  - check: response has ok/data/error/metrics
    passed: true
  - check: metrics has time_ms
    passed: true
  - check: metrics has chars_processed (advisory)
    passed: true
all_required: 5/5
all_advisory: 1/1
verdict: PASS
```

```yaml
# eal test all
results:
  bildung: PASS (5/5)
  bildung-context: PASS (5/5)
  git: PASS (5/5)
  systemd: PASS (5/5)
  filesystem: PASS (5/5)
total: 5/5 adapters passing
```

**Implementación:**

```python
# En main(), antes del bloque de adapters:
if domain == "test":
    result = _handle_test(command, args, adapters)
    print(json.dumps(result, indent=2, ensure_ascii=False))
    sys.exit(0 if result.get("ok") else 1)

def _handle_test(command, args, adapters):
    """Dominio test: smoke test de adapters."""
    adapter_name = command  # eal test <adapter>
    
    def _test_one(name, handle):
        checks = []
        # 1. ADAPTER_NAME
        # ... pero no tenemos acceso al módulo, solo al handle
        # Podemos verificar handle es callable
        checks.append({"check": "handle is callable", "passed": callable(handle)})
        
        # 2. handle() no lanza
        try:
            result = handle("status", {})
            checks.append({"check": "handle() does not throw", "passed": True})
        except Exception as e:
            checks.append({"check": "handle() does not throw", "passed": False, "error": str(e)})
            return checks
        
        # 3-5. Formato de respuesta
        checks.append({"check": "response has ok", "passed": "ok" in result})
        checks.append({"check": "response has metrics", "passed": "metrics" in result})
        checks.append({"check": "metrics has time_ms", "passed": isinstance(result.get("metrics", {}).get("time_ms"), int)})
        checks.append({"check": "metrics has chars (advisory)", "passed": "chars_processed" in result.get("metrics", {})})
        
        return checks
    
    if adapter_name == "all":
        results = {}
        for name, handle in adapters.items():
            checks = _test_one(name, handle)
            required = sum(1 for c in checks if not c["check"].endswith("(advisory)"))
            passed = sum(1 for c in checks if c["passed"] and not c["check"].endswith("(advisory)"))
            results[name] = f"PASS ({passed}/{required})" if passed == required else f"FAIL ({passed}/{required})"
        return _ok({"results": results, "total": f"{sum(1 for v in results.values() if v.startswith('PASS'))}/{len(results)} adapters passing"})
    
    if adapter_name not in adapters:
        return _err(f"Adapter not found: {adapter_name}")
    
    checks = _test_one(adapter_name, adapters[adapter_name])
    required = sum(1 for c in checks if not c["check"].endswith("(advisory)"))
    passed = sum(1 for c in checks if c["passed"] and not c["check"].endswith("(advisory)"))
    advisory = sum(1 for c in checks if c["check"].endswith("(advisory)"))
    advisory_passed = sum(1 for c in checks if c["passed"] and c["check"].endswith("(advisory)"))
    
    return _ok({
        "adapter": adapter_name,
        "checks": checks,
        "all_required": f"{passed}/{required}",
        "all_advisory": f"{advisory_passed}/{advisory}",
        "verdict": "PASS" if passed == required else "FAIL",
    })
```

### Reglas compartidas

- **NO** modificar `_discover_adapters()`.
- **NO** modificar `_capture_metric()` ni `_read_metrics()`.
- **NO** modificar `_parse_cli_args()` ni `_infer_type()`.
- **NO** crear archivos nuevos. Todo en `runtime.py`.
- **`eal test` es built-in.** Como `telemetry`, no aparece en `eal-config.yaml`.
- **El comando de prueba usa `"status"` como comando genérico.** Si un adapter no tiene `status`, puede devolver `ok: false` con "Unknown command" — eso no es un fallo del smoke test.

### Criterios de éxito

- [ ] `eal telemetry ratios --domain systemd --since "2026-08-10"` → solo entradas desde esa fecha
- [ ] `eal telemetry summary --since "2026-08-11"` → solo entradas desde esa fecha
- [ ] Rango sin entradas → `ok: true`, datos vacíos (no error)
- [ ] `eal test bildung` → `verdict: PASS` con 5/5 required
- [ ] `eal test filesystem` → `verdict: PASS`
- [ ] `eal test all` → resultados para los 5 adapters registrados
- [ ] `eal test inexistente` → `ok: false`
- [ ] `telemetry` y `test` no aparecen en `eal-config.yaml`
- [ ] Métricas `chars_processed`, `chars_returned`, `time_ms` en `telemetry` (ya existente) y `test` (nuevo)

---

## Arquitectura de roles

| Rol | Responsable | Tareas |
|---|---|---|
| **Tech Lead** | Vos | Preparar specs para Ejecutores A y B → supervisar ambos en paralelo → integrar → reportar |
| **Ejecutor A** | Agente | Task A: docker adapter |
| **Ejecutor B** | Agente | Task B: telemetry `--since`/`--until` + `eal test` smoke |

**No se usa Diseñador.** El brief contiene specs.

**No se usa Revisor.** Criterios ejecutables.

---

## Lo que ya existe (no reconstruir)

| Artefacto | Estado |
|---|---|
| `runtime.py` — 348 líneas, `_telemetry_*` funciones | ✅ Necesita filtro temporal + `_handle_test` (~80 líneas) |
| `adapters/engine.py` — 12 funciones | ✅ Solo lectura |
| `adapters/bildung/`, `git/`, `systemd/`, `filesystem/` | ✅ No se modifican |
| `adapters/bildung-context/` | ✅ No se modifica |
| `eal/` — paquete pip | ✅ No se modifica |
| `eal-config.yaml` — 5 adapters | ✅ Tech Lead agrega docker al integrar |
| `~/.bildung/eal/metrics.jsonl` | ✅ Solo lectura para telemetry |
| `specs/adapter_contract.md` | ✅ Solo lectura |
| Docker en RPi4B (20+ contenedores) | ✅ Datos reales para validación |

---

## Restricciones

- **No escribas código.** Prepará specs, delegá, supervisá.
- **No modifiques bildung-context, engine, git, systemd, filesystem.**
- **No modifiques el paquete pip `eal/`.**
- **No crees un directorio `adapters/test/`.** `test` es built-in del Runtime.
- **Docker adapter usa `engine.run_subprocess()`.** Ya validado en git y systemd.
- **No toques Gobernanza ni Codex.**
- **Máximo 2 iteraciones por Ejecutor.**
- **`eal test` no escribe métricas.** No queremos tests contaminando `metrics.jsonl`.

---

## Documentos de referencia

- `specs/adapter_contract.md` — contrato canónico
- `specs/roadmap_eal.md` — Ciclo 9 definición
- `specs/proximos_adapters.md` §4 — spec original de docker
- `docs/agent_hierarchy.md` — tu rol (Capa 4)
- `docs/agent_constitution.md` — 10 artículos vinculantes
- `docs/reporting_protocol.md` — formato de REPORT.md
- `adapters/systemd/adapter.py` — referencia de adapter con `engine.run_subprocess()`
- `adapters/filesystem/adapter.py` — referencia de adapter post-subprocess
- `runtime.py` líneas 200-348 — `_telemetry_*` actual y `main()`
