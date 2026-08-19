# Bitácora del Arquitecto — self-driving-lab

> Decisiones, riesgos y recomendaciones. Resumen para Gobernanza.

---

## Ciclos 6-7 — Cierre de Fase I

### Ciclo 6 — Métricas + Empaquetado + Validación RPi4B ✅
T1: métricas retroactivas (~43 líneas). T2: validación RPi4B (ratio 10.8:1). T3: `pip install -e .`. Forense: 9 archivos eliminados. Roadmap original cerrado.

### Ciclo 7 — Runtime Type Coercion + Telemetry Built-in ✅
runtime.py 157→344 líneas. `_infer_type()`. Telemetry como dominio interno del Runtime con captura pasiva a JSONL. Árbol de decisiones v1→v2→v3 documentado.

**Arquitecto no intervino en 5 ciclos consecutivos (3-7).**

---

## Ciclo 8 — Saneamiento + Filesystem + Bildung Extendido ✅

Task 8 (Tech Lead): `_infer_type` fix, `.gitignore` +egg-info, `CHANGELOG.md`. Task 10a (Ejecutor A): `filesystem` — 265 líneas, primer adapter post-subprocess. Task 10b (Ejecutor B): bildung → 473 líneas, `search` (ratio 635:1) + `dependencias`. Primera delegación paralela.

---

## Ciclo 9 — Docker + Telemetry + Auto-testing ✅

### Decisión
Dos tareas en paralelo. Docker adapter (Ejecutor A, 258 líneas). Telemetry `--since`/`--until` + `eal test` smoke (Ejecutor B, runtime.py +121 líneas).

### Resultado — ✅ Integrado. Fase II cerrada.

- **Task A:** `adapters/docker/` — 258 líneas, `ps`/`stats`/`logs`. JSON nativo (`--format json`). Graceful degradation cuando Docker no disponible.
- **Task B:** `_filter_by_time()` + `_handle_test()`. `eal test all` → **6/6 PASS**. `telemetry --since` funcional.
- **Validación RPi4B:** Docker adapter validado contra **16 contenedores reales** en producción. 10/10 tests. `ps`/`stats`/`logs` con datos reales. Métricas vinculantes en toda respuesta. Cero modificaciones requeridas.
- **Stack completo:** bildung → bildung-context → git → systemd → filesystem → docker. 6 adapters, 28 comandos, 3 dominios built-in (telemetry, test).
- **Delegación paralela consolidada:** segundo ciclo con 2 Ejecutores simultáneos. Cero conflictos.

**Arquitecto no intervino — 7° ciclo consecutivo.**

---

## Ciclo 10 — Adapters de código I (Agosto 2026)

### Decisión
Dos tareas en paralelo. Fase III: adapters de código vía `ast.parse`.

1. **`code` adapter (Ejecutor A):** `adapters/code/`. `structure`, `imports`, `summary`. `ast.parse()` — sin subprocess, sin CLI. Segundo adapter post-subprocess.
2. **`deps` adapter (Ejecutor B):** `adapters/deps/`. `graph`, `impact`. Grafo de dependencias entre archivos. Primer adapter que modela nodos y aristas.

### Riesgos
- **`ast` es frágil con imports dinámicos.** Los adapters usan `sys.path.insert(0, ...)` + `importlib`. `ast.parse()` ve el `import engine` pero no sabe que `engine` se resuelve en runtime vía `_discover_adapters()`.
- **`deps` requiere resolver paths relativos.** `from .adapter import ADAPTER_NAME` → hay que resolver `.` al directorio del archivo. Edge case: imports que usan `_ENGINE_DIR.parent` como en docker/systemd.
- **Dos adapters nuevos en un ciclo.** 6→8 adapters. `eal test all` debe seguir pasando.

### Recomendación
Paralelo. `code` y `deps` comparten `ast` pero son directorios distintos. Si el Ejecutor B necesita parsear imports que el Ejecutor A ya resolvió... no importa, porque no comparten código. Cada adapter es autocontenido.
