# Bitácora del Arquitecto — self-driving-lab

> Decisiones, riesgos y recomendaciones. Resumen para Gobernanza.

---

## Ciclo 6 — Métricas, Validación, Empaquetado (Agosto 2026)

### Decisión

Tres tareas secuenciales, dos delegaciones a Ejecutor, una validación manual del Tech Lead en RPi4B:

1. **Métricas retroactivas + Runtime fix** (Ejecutor): agregar `chars_processed` + `chars_returned` a bildung, bildung-context, git. Corregir bug de `ERROR:` duplicado en Runtime. Agregar captura automática de métricas a bildung-context.
2. **Validación systemd real** (Tech Lead): ejecutar `eal systemd status/errors/logs` en RPi4B con acceso SSH. Primera medición cuantitativa de la hipótesis EAL (ratio chars_processed / chars_returned con datos reales).
3. **Empaquetado** (Ejecutor): estructura `pyproject.toml` + `pip install eal`.

### Riesgos

- **bildungs-context tiene estructura de clases** (no sigue el patrón plano de git/systemd). El Ejecutor podría querer refactorizar en lugar de agregar 5 líneas de métricas. Mitigación: prohibición explícita de cambiar estructura de clases.
- **El Runtime actualmente imprime `ERROR:` a stderr cuando `ok: false`**. Esto rompe la promesa de output estructurado y afecta a los 4 adapters. El fix es ~3 líneas pero es la primera modificación al Runtime desde el Ciclo 3.
- **RPi4B corre producción** (n8n, PostgreSQL, Traefik, 20+ servicios). La validación con systemd es solo-lectura (`status`, `errors`, `logs`), pero si alguien ejecuta por error un adaptador futuro con `start`/`stop`, podría afectar servicios vivos. Mitigación: el adapter systemd actual no tiene comandos mutables.

### Resultado — ✅ Integrado

**3/3 tareas completadas. Roadmap EAL original cerrado.**

- **T1 (Ejecutor):** métricas retroactivas en git, bildung, bildung-context (~43 líneas, 0 iteraciones). Runtime sin `ERROR:` duplicado. Captura automática de métricas vía bildung-context funcional.
- **T2 (Tech Lead + Gobernanza):** validación en RPi4B (systemd 257, PID 1). SSH logs: **chars_processed=40,556 → chars_returned=3,757 (ratio 10.8:1)**. Primera evidencia cuantitativa de la hipótesis EAL con datos reales.
- **T3 (Ejecutor):** empaquetado `pip install -e .`, entry point `eal`. 0 iteraciones.
- **Forense:** 9 archivos residuales eliminados (~41 KB). Proyecto: 92 → 83 archivos.

**Riesgo materializado:** bildung-context mantuvo su estructura de clases sin refactor — la prohibición explícita del brief fue efectiva. El bug `--limit` string→int es preexistente del Ciclo 5 y no se manifestó porque el Runtime pasa todos los args como strings. **Esto es deuda del Runtime, no del adapter.**

**Arquitecto no intervino** — 4° ciclo consecutivo. La cadena Gobernanza → Tech Lead → Ejecutor es autosuficiente.

---

## Ciclo 7 — Árbol de decisiones: Telemetry (v1 → v2 → v3)

> **Contexto:** Gobernanza preguntó si telemetry debía ser un adapter, un engine,
> parte de bildung-context, o del Runtime. Esto forzó tres iteraciones de diseño
> en una sola conversación.

### v1 — Adapter independiente `adapters/telemetry/` ❌ DESCARTADA

**Diseño:** `adapters/telemetry/__init__.py` exporta `ADAPTER_NAME + handle()`. Importa bildung-context directamente para consultar métricas vía `query_entries()`.

**Problema detectado por Gobernanza:** Viola `adapter_contract.md` §2.2, regla #4: *"Cada adapter es autocontenido. No asume que existe otro adapter."* Si bildung-context migra de SQLite a PostgreSQL, telemetry se rompe sin que el Runtime lo detecte. Acoplamiento invisible adapter→adapter.

### v2 — Comando `telemetry` en bildung-context ❌ DESCARTADA

**Diseño:** Séptimo comando en `bildung-context/eal_adapter.py`. bildung-context consulta sus propios datos SQLite. `eal bildung-context telemetry summary`. Cero acoplamiento nuevo entre adapters.

**Problema detectado por Gobernanza (segunda iteración):** ¿Debería telemetry ser un engine? ¿O parte del Runtime? La pregunta reveló un problema más profundo:

- bildung-context fue diseñado para almacenar documentación de Bildung (iteraciones, decisiones, métricas de ciclos de delegación). No fue diseñado para ser el backend de telemetría operacional de la EAL.
- Al ponerle el comando `telemetry`, bildung-context se vuelve "especial" — ya no es un adapter más. Si bildung-context no está registrado en `eal-config.yaml`, el Runtime pierde métricas silenciosamente (el `try/except` traga el error).
- La captura de métricas Runtime→bildung-context introduce acoplamiento frágil: si bildung-context cambia su API (`write` → `insert`), el Runtime se rompe.

### v3 — Runtime built-in + JSONL ✅ ELEGIDA

**Diseño:**

1. **Captura de métricas:** el Runtime escribe a `~/.bildung/eal/metrics.jsonl` (append-only, sin bildung-context). Cada línea es un JSON con `{timestamp, domain, command, ok, metrics}`. Si el archivo no existe, se crea. Si el disco está lleno, la métrica se pierde — igual que con SQLite.
2. **Consulta de métricas:** el Runtime maneja el dominio `telemetry` internamente (sin delegar a un adapter). `eal telemetry summary` → el Runtime lee JSONL, agrega en memoria, devuelve.
3. **Limpieza:** se elimina la captura vía bildung-context del Runtime (líneas 133-147 actuales). bildung-context vuelve a ser un adapter más.

**Por qué es superior:**

| Propiedad | v2 (bildung-context) | v3 (Runtime + JSONL) |
|---|---|---|
| **Acoplamiento** | Runtime → bildung-context → SQLite | Runtime → archivo local |
| **bildung-context es especial** | Sí. Backend de métricas. | No. Vuelve a ser documentación. |
| **Pasividad** | Adapters no saben | Adapters no saben |
| **Escalabilidad escritura** | SQLite write por comando | Append JSONL, O(1) |
| **Portabilidad** | Necesita bildung-context registrado | Solo necesita filesystem |
| **Supervivencia** | Si bildung-context falta → pérdida | Si archivo falta → se crea |
| **Dependencias** | SQLite (transitiva) | stdlib (`json`, `pathlib`) |
| **Código nuevo** | ~30 líneas en eal_adapter.py | ~35 líneas en runtime.py |

**Expansión del contrato:** *"El Runtime maneja el dominio `telemetry` internamente, sin delegar a un adapter. El resto de los dominios se descubren por filesystem."* Es una línea. Telemetry no es un dominio externo como git o systemd — es la capacidad del orquestador de observarse a sí mismo.

### Árbol visual

```
telemetry: ¿dónde vive?
│
├─ ¿Engine? → NO.
│   Engine = 12 fn puras sin conocimiento de dominio Bildung/EAL.
│   Telemetry SÍ es conocimiento de dominio (operación interna EAL).
│
├─ ¿Adapter nuevo? → NO (v1).
│   Viola adapter_contract.md §2.2 regla #4: "autocontenido".
│   Adapter→adapter = acoplamiento invisible al Runtime.
│
├─ ¿Comando en bildung-context? → NO (v2).
│   Lo convierte en "especial". Acoplamiento Runtime→adapter.
│   Si bildung-context falta, métricas se pierden.
│   bildung-context no fue diseñado para ser backend de telemetría.
│
└─ ¿Runtime built-in + JSONL? → SÍ (v3). ✅
    Captura pasiva. Escalable. bildung-context es un adapter más.
    Contrato se expande 1 línea.
    Métricas sobreviven independientemente de qué adapters existan.
```

### Lo que mantenemos de v1/v2

El modelo canónico es idéntico: `summary`, `ratios`, `recent`. Solo cambia dónde vive el código y dónde se almacenan los datos.

---

## Ciclo 7 — Runtime Fix + Telemetry (v3 final, Agosto 2026)

### Decisión

Una delegación a Ejecutor con dos sub-tareas:

1. **Runtime type coercion + adapter fix**: `_parse_cli_args()` infiere `int` y `float`. Fix del `--limit` bug en systemd. **Este fix es estructural:** afecta a todos los adapters actuales y futuros. Sin él, cada adapter nuevo hereda el bug de strings-como-ints. El fix en el Runtime evita que cada adapter tenga que hacer `int(args.get("limit", 20))` manualmente.

2. **Telemetry como dominio built-in del Runtime + migración a JSONL**: el Runtime captura métricas a `~/.bildung/eal/metrics.jsonl` (append-only). Maneja el dominio `telemetry` internamente (`summary`, `ratios`, `recent`). Se elimina la captura vía bildung-context. bildung-context vuelve a ser solo documentación.

### Riesgos

- **Type coercion** cambia el contrato implícito "args son strings". Si un adapter existente depende de recibir strings y ahora recibe ints, puede fallar. Mitigación: cambio mínimo — solo inferir int/float cuando `isdigit()` o `replace('.','',1).isdigit()`. Strings no numéricos intactos. Booleans (`true`/`false`) también.
- **Migración de métricas:** las 20+ entradas existentes en bildung-context (SQLite) son de prueba. No se migran — se empieza JSONL desde cero. Las entradas viejas en SQLite quedan como históricas.
- **Métricas en dos lugares:** durante la transición, el Runtime solo escribe JSONL. bildung-context ya no recibe métricas nuevas. Las viejas en SQLite se preservan pero `telemetry` no las lee. No se rompe nada — solo hay un corte limpio.

### Recomendación

Ejecutar T1 → T2 secuencial. Misma delegación, mismo Ejecutor. La T2 no depende estrictamente de T1, pero ambas modifican `runtime.py`. Hacerlas juntas evita conflictos de merge.
