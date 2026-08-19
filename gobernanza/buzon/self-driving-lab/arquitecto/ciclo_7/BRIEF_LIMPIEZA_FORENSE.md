# BRIEF — Tech Lead / self-driving-lab (Ciclo 7 — Limpieza Forense)

> **De:** Arquitecto (Capa 3)
> **Para:** Tech Lead (Capa 4)
> **Fecha:** Agosto 2026
> **Ciclo:** 7 — Verificación de limpieza forense + prevención

---

## Objetivo

Dos acciones mínimas. Sin delegación a Ejecutor — el Tech Lead las ejecuta directamente. Cierran el hallazgo forense del Ciclo 6.

---

## Verificación — 9 archivos + 1 directorio

### Estado real

El forense del Ciclo 6 (`forense_ciclo_6/REPORT_HUERFANOS.md`) identificó 9 archivos
+ 1 directorio para eliminar. Verifiqué su estado actual: **los 9 ya están eliminados.**
La limpieza se ejecutó durante el cierre del Ciclo 6.

| # | Archivo/Directorio | Estado verificado |
|---|---|---|
| 1 | `parser.py` | ✅ Eliminado |
| 2 | `adapters/eal-engine/engine.py` | ✅ Eliminado |
| 3 | `adapters/eal-engine/` (directorio) | ✅ Eliminado |
| 4 | `adapters/bildung-context/TASK.md` | ✅ Eliminado |
| 5 | `adapters/bildung-context/REPORT.md` | ✅ Eliminado |
| 6 | `adapters/git/REPORT.md` | ✅ Eliminado |
| 7 | `eal/adapters/bildung-context/TASK.md` | ✅ Eliminado |
| 8 | `eal/adapters/bildung-context/REPORT.md` | ✅ Eliminado |
| 9 | `eal/adapters/git/REPORT.md` | ✅ Eliminado |

**No hay acción de eliminación pendiente.**

---

## Prevención — `.gitignore`

### El problema

El forense detectó que `TASK.md` y `REPORT.md` se filtraron a los directorios
de adapters y al paquete pip. Estos archivos son specs de ejecución, no código
de producción. Si no se previene, cada ciclo futuro corre el riesgo de
contaminar los mismos directorios.

### La solución

Agregar dos líneas al `.gitignore` de `02-eal/`:

```bash
# Prevenir que specs de ejecución se filtren a código de producción
**/adapters/**/TASK.md
**/adapters/**/REPORT.md
```

O más específico, si se quiere cubrir solo los directorios de adapters
existentes y el paquete pip:

```bash
adapters/*/TASK.md
adapters/*/REPORT.md
eal/adapters/*/TASK.md
eal/adapters/*/REPORT.md
```

### Criterios de éxito

- [ ] `.gitignore` actualizado con patrones para `TASK.md` y `REPORT.md` en adapters
- [ ] `git status` no muestra `TASK.md` ni `REPORT.md` como untracked en directorios de adapters

---

## Nota: ¿por qué sin Ejecutor?

Dos líneas en `.gitignore`. El forense ya hizo el trabajo pesado (auditar 92
archivos, verificar imports cruzados, diff byte a byte). Lo que queda es cerrar
el loop con una regla preventiva. No justifica preparar specs, contexto, y
delegación para 2 líneas de configuración.

---

## Documentos de referencia

- `specs/ciclo_6_empaquetado+metricas/forense_ciclo_6/REPORT_HUERFANOS.md` — hallazgo original
- `.gitignore` actual — 1 línea (`__pycache__/`)
