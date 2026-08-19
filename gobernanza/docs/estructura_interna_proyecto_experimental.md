# Estructura Interna de Proyecto — Experimental v0

> **El pipeline del Lab es fractal.** Así como los proyectos fluyen entre niveles (workspace → modelado → dev → activos → infraestructura → archivo), un proyecto con suficiente complejidad puede contener **múltiples experimentos internos** con ciclos de vida independientes.
>
> Esta estructura es experimental. Se aplica sobre `self-driving-lab` como primer caso de prueba. No reemplaza `estructura_de_proyecto.md` (que define requisitos por nivel del Lab). Lo complementa para proyectos que contienen múltiples hipótesis activas simultáneamente.

---

## Principio rector

> **Tratar un proyecto como monolito cuando contiene múltiples hipótesis es perder resolución.**

Cuando un proyecto genera varios experimentos con ciclos de vida distintos —cada uno con su propia hipótesis, iteraciones y criterios de validación— gestionarlos como "el proyecto" es como tratar el Lab entero como "la carpeta de ideas". La estructura debe reflejar la granularidad real del trabajo.

---

## Topología

```
proyecto/
│
├── README.md                 ← qué es este proyecto, hacia dónde va
├── evaluacion.yaml           ← evaluación del contenedor (solo en transiciones)
│
├── .gob/                     ← gobernanza interna mínima
│   ├── README.md             ← criterios de priorización entre experimentos
│   └── bitacora.md           ← decisiones del contenedor (una línea por decisión)
│
├── workdir/                  ← experimentos activos (workspace + modelado + dev)
│   ├── 01-nombre-experimento/
│   │   ├── hipotesis.md      ← ~30 líneas: qué testeo, criterios, cómo sé si funcionó
│   │   └── bitacora.md       ← una línea por iteración: fecha + qué aprendí
│   ├── 02-nombre-experimento/
│   │   ├── hipotesis.md
│   │   └── bitacora.md
│   └── 03-nombre-experimento/
│       ├── hipotesis.md
│       └── bitacora.md
│
├── docs/                     ← conocimiento normalizado (Codex interno)
│   ├── (hallazgos, modelos, patrones detectados)
│   └── archive/              ← ruido congelado, referencias externas, clones
│
├── archivo/                  ← experimentos internos cerrados (con CIERRE.md)
│
└── (código, assets, scripts — estructura libre)
```

---

## Las tres zonas

### `.gob/` — Gobernanza interna mínima

No es una réplica de `0-gobernanza/`. Es una capa ligera para decisiones que afectan al proyecto como contenedor:

| Archivo | Contenido |
|---|---|
| `README.md` | ¿Cómo decido qué experimento priorizar? ¿Qué criterios uso para promover, pausar o cerrar un experimento hijo? ¿Cada cuánto reviso? |
| `bitacora.md` | Una línea por decisión del contenedor: `2026-08-07: EAL pasa a ser prioridad sobre Branch Lab. Razón: X.` |

No necesita plantilla de evaluación propia — usa `evaluacion.yaml` del proyecto y la plantilla del Lab.

### `workdir/` — Experimentos hijos

Cada subdirectorio en `workdir/` es un experimento autónomo. Solo requiere dos archivos:

**`hipotesis.md`** (~30 líneas):

```markdown
# [Nombre del experimento]

## Hipótesis
> [Una frase. Clara. Falsable.]

## Criterios de validación
- [¿Cómo sabré si funcionó?]
- [¿Cómo sabré si falló?]

## Vector de potencial
- [ ] Activo
- [ ] Infraestructura
- [ ] Mariposa

## ¿Qué desbloquea si funciona?
[Árbol de posibilidades]
```

**`bitacora.md`** (una línea por ciclo):

```markdown
# Bitácora

- 2026-08-07: Implementé X. Resultado: Y. Aprendizaje: Z. Próximo: W.
- 2026-08-09: Probé enfoque alternativo. No funcionó por A. Próximo: B.
- 2026-08-12: Hipótesis validada en 2 de 3 contextos. Falta testear C.
```

Cinco líneas que capturen el aprendizaje. No más. Si un experimento necesita un diario exhaustivo, está haciendo demasiado — probablemente debería graduarse a proyecto independiente.

### `docs/` — Codex interno

Conocimiento que emergió de los experimentos y fue normalizado:

- Patrones detectados que aplican a múltiples experimentos.
- Modelos que explican resultados.
- Referencias externas procesadas (no crudas — eso va en `docs/archive/`).

`docs/archive/` es para ruido congelado: clones de repositorios, datasets sin procesar, referencias que aún no fueron digeridas. Es el equivalente interno de `1-workspace` para el proyecto.

---

## Reglas de operación

### 1. Nada obligatorio en workdir salvo dos archivos
`hipotesis.md` y `bitacora.md`. Si un experimento no tiene al menos eso, no es un experimento — es una idea suelta. Las ideas sueltas van en `docs/archive/` o en notas informales.

### 2. La evaluación formal solo en transiciones
`evaluacion.yaml` del proyecto contenedor se completa únicamente cuando un experimento hijo:
- Se gradúa a proyecto independiente en el Lab.
- Se cierra (compostaje interno → `archivo/`).
- Cambia significativamente de dirección.

No se evalúa cada iteración. Para eso está la bitácora.

### 3. Graduación: cuando un experimento hijo supera al contenedor
Si un experimento en `workdir/` crece hasta tener sus propios sub-experimentos, o requiere evaluación independiente, o su complejidad ya no cabe en `hipotesis.md` + `bitacora.md`, **se gradúa**: se mueve a su propio directorio en el nivel correspondiente del Lab (`2-modelado/` o `3-dev/`).

### 4. Cierre interno
Un experimento hijo que terminó (validado, falseado o abandonado) se mueve a `archivo/` con un mini `CIERRE.md`:
- ¿Qué hipótesis se testeó?
- ¿Qué se aprendió?
- ¿Por qué terminó?

Esto alimenta el Codex del proyecto (`docs/`) y eventualmente el Codex de Bildung.

---

## Cuándo usar esta estructura

**Usar cuando:**
- Un proyecto genera ≥2 experimentos con hipótesis diferenciadas.
- Los experimentos tienen ciclos de iteración independientes.
- Tratar el proyecto como monolito está generando fricción (no sabés en qué estado está cada cosa).

**NO usar cuando:**
- El proyecto tiene una sola hipótesis clara.
- El proyecto está en `1-workspace` (el caos no se estructura).
- El proyecto es simple y `README.md` + `evaluacion.yaml` bastan.

---

## Relación con el pipeline del Lab

La topología fractal no reemplaza el pipeline. Lo refleja:

| Pipeline del Lab | Reflejo interno |
|---|---|
| `0-gobernanza/` | `.gob/` |
| `1-workspace/` | `docs/archive/` (ruido congelado) |
| `2-modelado/` + `3-dev/` | `workdir/` (experimentos activos) |
| `codex/` | `docs/` (conocimiento normalizado) |
| `6-archivo/` | `archivo/` (experimentos cerrados) |

Los niveles `4-activos` y `5-infraestructura` no tienen reflejo interno — un experimento hijo que cristaliza se gradúa al Lab principal.

---

## Métricas internas (mínimas)

| Métrica | Fuente | Frecuencia |
|---|---|---|
| **Iteraciones por experimento** | `bitacora.md` → contar líneas | Bajo demanda |
| **Experimentos activos vs cerrados** | `ls workdir/` vs `ls archivo/` | Revisión de `.gob/` |
| **Tasa de graduación** | ¿cuántos experimentos hijos se volvieron proyectos independientes? | Trimestral |
| **Energía del contenedor** | Juicio humano: ¿este proyecto drena o energiza? | Bitácora mensual |

---

## Anti-patrones

| Anti-patrón                              | Síntoma                                                           | Corrección                                                                           |
| ---------------------------------------- | ----------------------------------------------------------------- | ------------------------------------------------------------------------------------ |
| **workdir como vertedero**               | 15 carpetas en `workdir/`, solo 3 con `hipotesis.md`              | Los que no tienen hipótesis no son experimentos. Mover a `docs/archive/` o eliminar. |
| **Micro-gestión de experimentos**        | `hipotesis.md` de 200 líneas, `bitacora.md` con entradas horarias | El experimento probablemente debería graduarse a proyecto independiente.             |
| **Gobernanza interna fantasma**          | `.gob/` existe pero la bitácora tiene 6 meses sin entradas        | O no se está gobernando, o el proyecto no necesita estructura fractal.               |
| **Confundir docs/archive/ con archivo/** | Clon de un repo en `archivo/` en lugar de `docs/archive/`         | `docs/archive/` = ruido externo. `archivo/` = experimentos propios cerrados.         |

---

## Próxima iteración

Esta estructura se está testeando en `self-driving-lab`. Lo que se aprenda allí determinará si:
- Se promueve a estándar oficial (reemplazando o complementando `estructura_de_proyecto.md`).
- Se ajusta (¿hacen falta más zonas? ¿sobran?).
- Se composta (la estructura plana era suficiente).
