# Estructura de Proyecto

> **El estándar mínimo crece con el nivel de resolución del proyecto.**
>
> No se exige lo mismo en `1-workspace` (donde el caos es legítimo) que en `4-activos` (donde la autonomía debe estar documentada).

---

## Principio

La estructura de un proyecto **no se hereda del tema** (software, hardware, conocimiento). Se hereda del **nivel del pipeline** donde habita.

Dos proyectos de software pueden tener estructuras completamente distintas si uno está en `1-workspace` y el otro en `4-activos`. Y está bien.

---

## Requisitos por nivel

### Nivel 1 — Workspace

**Sin requisitos.** El caos es el estado natural de este nivel. No se exige README, documentación ni organización interna.

Lo único que se pide es **no interferir** con otros proyectos del workspace.

---

### Nivel 2 — Modelado

Requisitos mínimos:

```
nombre-del-proyecto/
├── README.md            ← obligatorio
│   ├── ¿Qué observo?
│   ├── ¿Qué patrón/flujo/stocks detecto?
│   ├── ¿Qué hipótesis estoy formando?
│   └── Posible vector: ¿activo, infraestructura o mariposa?
│
└── (contenido libre)
```

El README de modelado no necesita ser extenso. Puede ser un archivo de 20 líneas. Pero debe existir — pasar de workspace a modelado implica que ya hay algo que merece ser escrito.

---

### Nivel 3 — Dev

```
nombre-del-proyecto/
├── README.md            ← obligatorio
│   ├── Hipótesis explícita
│   ├── Criterios de validación (¿cómo sabré si funciona?)
│   ├── Criterio de salida (¿a qué nivel apunta? ¿cuándo se archiva?)
│   └── Bitácora de iteraciones (una línea por ciclo: fecha + qué se aprendió)
│
├── evaluacion.yaml      ← obligatorio (copia del template, rellenado)
│
├── archivo/             ← opcional: versiones deprecadas, intentos fallidos
│
└── (código, docs, assets — estructura libre)
```

La bitácora de iteraciones no es un diario exhaustivo. Es un registro mínimo de ciclos de aprendizaje:

```markdown
## Bitácora

- 2026-07-20: Primera versión del prototipo. La latencia en X es inaceptable.
- 2026-07-22: Cambié el enfoque de A a B. Latencia se redujo 80%.
- 2026-07-25: Hipótesis validada en 2 de 3 contextos. Falta testear el tercero.
```

Cinco líneas que capturen el aprendizaje. No más.

---

### Nivel 4 — Activos

```
nombre-del-proyecto/
├── README.md            ← obligatorio
│   ├── ¿Qué hace este activo?
│   ├── ¿Qué valor genera sin mi presencia?
│   ├── Instrucciones de operación (para delegar)
│   ├── Requisitos de mantenimiento (¿qué necesita periódicamente?)
│   └── Monitoreo: ¿cómo sé que sigue funcionando?
│
├── evaluacion.yaml      ← obligatorio (evaluación que justificó la promoción)
│
├── archivo/             ← versiones deprecadas
│
└── (contenido libre)
```

Un activo debe poder ser **operado por otra persona o agente**. Si el README no permite eso, el proyecto no es realmente un activo — todavía pertenece a `3-dev`.

---

### Nivel 5 — Infraestructura

```
nombre-del-proyecto/
├── README.md            ← obligatorio
│   ├── ¿Qué modifica este componente en el ecosistema Bildung?
│   ├── ¿Qué flujos altera? ¿Qué nuevas conexiones crea?
│   ├── ¿Qué experimentos o capacidades desbloquea?
│   ├── Arquitectura: diagrama de cómo se conecta con el resto del Lab
│   ├── Instrucciones de operación
│   └── Requisitos de mantenimiento
│
├── evaluacion.yaml      ← obligatorio
│
├── archivo/             ← versiones deprecadas
│
└── (contenido libre)
```

La diferencia clave con un activo: el README de infraestructura debe documentar **cómo modifica al sistema**, no solo qué valor genera.

---

### Nivel 6 — Archivo

```
nombre-del-proyecto/
├── CIERRE.md            ← obligatorio
│   ├── ¿Qué era este proyecto?
│   ├── ¿Por qué terminó?
│   ├── ¿Qué se aprendió? (anti-patrones, límites, restricciones)
│   └── ¿Hay algo rescatable para el Codex?
│
└── (contenido original del proyecto, congelado)
```

`CIERRE.md` es el mecanismo de compostaje. Sin este archivo, archivar es solo abandonar. Con él, el fracaso se convierte en nutriente para el Codex.

---

## Archivo anidado (versiones deprecadas)

Cuando un proyecto **sigue vivo** pero una versión específica queda obsoleta:

```
proyecto-activo/
├── ...
└── archivo/
    └── v1-deprecada/
        ├── (contenido de la versión anterior)
        └── CIERRE.md   ← ¿por qué se deprecó esta versión? ¿qué se aprendió?
```

Esto es distinto de `6-archivo/`, que es para proyectos completos que terminaron su ciclo de vida.

---

## La plantilla portátil: `evaluacion.yaml`

Todo proyecto en nivel `2-modelado` o superior debería tener una copia de `evaluacion.yaml` en su raíz. No se requiere en `1-workspace`.

La copia se toma de `0-gobernanza/evaluacion.yaml` y se rellena con los datos del proyecto. Esto permite:

- Evaluar el proyecto sin depender de un archivo externo.
- Que las evaluaciones viajen con el proyecto cuando cambia de nivel.
- Tener un historial: la evaluación que justificó la promoción de `2-modelado` a `3-dev` se preserva dentro del proyecto, aunque luego se genere una nueva evaluación para la siguiente transición.

---

## Lo que NO se exige

- **No se exige estructura de carpetas interna** (src/, docs/, tests/). Cada proyecto decide la suya.
- **No se exige lenguaje o stack específico**.
- **No se exige cobertura de tests ni CI/CD**.
- **No se exige que el README sea extenso** — se exige que exista y responda las preguntas del nivel.

---

## Resumen

| Nivel | README | `evaluacion.yaml` | CIERRE.md |
|---|---|---|---|
| 1-workspace | — | — | — |
| 2-modelado | Obligatorio (mínimo) | Opcional | — |
| 3-dev | Obligatorio + bitácora | Obligatorio | — |
| 4-activos | Obligatorio + ops | Obligatorio | — |
| 5-infraestructura | Obligatorio + arquitectura | Obligatorio | — |
| 6-archivo | — | — | Obligatorio |
