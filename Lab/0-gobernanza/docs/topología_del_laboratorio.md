# Topología del Laboratorio

> **No es un árbol de carpetas. Es un mapa de flujos, restricciones y propiedades emergentes.**
>
> Este documento describe la estructura del Lab, las relaciones entre sus componentes y las propiedades que emergen de su operación. No es el "qué" (para eso está el pipeline) sino el "cómo se conecta todo".

---

## Estructura general

```
Lab/
├── 0-gobernanza/          ← Meta-nivel: criterios, reglas, bitácora
│   ├── README.md
│   ├── Plantilla - Evaluación.md
│   ├── evaluacion.yaml
│   ├── Cartera de Bildung.md
│   ├── docs/
│   ├── bitacora/
│   └── evaluaciones/
│
├── 1-workspace/           ← Gas: caos, ideas crudas, exploración
├── 2-modelado/            ← Líquido: patrones detectados, flujos visibles
├── 3-dev/                 ← Sólido blando: hipótesis en prueba
├── 4-activos/             ← Cristal: opera sin intervención
├── 5-infraestructura/     ← Cristal funcional: modifica el sistema
├── 6-archivo/             ← Fósil: lecciones extraídas, proyecto cerrado
│
└── codex/                 ← Observador: ontología compilada, peer del Lab
    ├── Ontología/
    ├── E1-E7/
    └── ...
```

---

## Relaciones fundamentales

### 1. Gobernanza ↔ Codex (co-evolución)

```
Gobernanza decide qué se compila → Codex
Codex redefine conceptos → Gobernanza afina criterios
```

No es una relación jerárquica. Es un bucle de resolución creciente. La gobernanza establece los filtros; el Codex, al compilar patrones, revela nuevos conceptos que obligan a recalibrar los filtros.

### 2. Gobernanza ↔ Pipeline (control de flujo)

```
Gobernanza → Pipeline: decide transiciones entre niveles
Pipeline → Gobernanza: genera datos de telemetría (ΔF, ΔA, mariposas)
```

La gobernanza no ejecuta — observa, decide y ajusta. El pipeline ejecuta. La bitácora mensual es el punto de sincronización.

### 3. Pipeline → Codex (alimentación)

Cada nivel del pipeline puede alimentar el Codex con distintos tipos de conocimiento:

| Origen | Tipo de conocimiento |
|---|---|
| `3-dev` | Patrones validados que sobrevivieron al testeo |
| `4-activos` | Conceptos que demostraron generar valor real |
| `5-infraestructura` | Reglas sobre cómo opera el ecosistema |
| `6-archivo` | Anti-patrones, límites, telemetría de fricción |

### 4. Codex → Pipeline (resolución)

El Codex no inyecta proyectos en el pipeline. Lo que inyecta es **resolución**: conceptos normalizados que permiten ver patrones donde antes había ruido. Esto modifica qué se detecta en `1-workspace` y cómo se modela en `2-modelado`.

### 5. Fundación y Gobernanza (capas pre-Lab)

```
Bildung Fundacional (Docs/)
        ↓ establece propósito y principios
    Gobernanza (0-gobernanza/)
        ↓ traduce propósito en criterios operativos
    Pipeline (1- a 6-)
        ↓ ejecuta bajo esos criterios
```

Ni la fundación ni la gobernanza son parte del pipeline. Son **pre-condiciones** para que el pipeline opere con dirección. Sin ellas, el Lab sería un sandbox sin criterio — entretenido, pero no convergente.

---

## Flujos

### Flujo principal: entropía → resolución

```
Mundo exterior (caos, información, problemas)
        ↓
    1-workspace (ingesta sin filtro)
        ↓
    2-modelado (detección de patrones)
        ↓
    3-dev (validación de hipótesis)
        ↓
    ┌───────────────┬───────────────────┐
    ▼               ▼                   ▼
4-activos    5-infraestructura    6-archivo
(cristaliza)  (modifica sistema)  (composta)
    │               │                   │
    └───────────────┴───────────────────┘
                    │
                    ▼
               codex/ (compilación ontológica)
```

### Flujo de gobernanza (ciclo mensual)

```
Bitácora del mes anterior
        ↓
Revisión de cada proyecto activo (Cartera)
        ↓
Decisión por proyecto: ¿promover, degradar, archivar, mantener?
        ↓
Ajuste de criterios si el Codex reveló nuevos conceptos
        ↓
Registro en bitácora del mes actual
```

### Flujo de Codex (ciclo de compilación)

```
Señal del pipeline (patrón detectado en dev/activos/archivo)
        ↓
Test isomórfico: ¿sobrevive en ≥3 dominios?
        ↓
Normalización: traducción al lenguaje del Codex
        ↓
Compilación: inyección + recálculo de bordes en la ontología
        ↓
Codex actualizado → posible recalibración de gobernanza
```

---

## Propiedades topológicas

### 1. El Codex como atractor extraño

El Codex no fuerza al Lab hacia un punto fijo. Pero su presencia modifica el espacio de posibilidades: ciertos patrones se vuelven visibles, ciertas preguntas se disuelven, ciertos experimentos dejan de tener sentido.

Es un **atractor**: el sistema tiende a orbitar cerca de los conceptos compilados. Pero es **extraño**: la órbita nunca se estabiliza del todo porque el Codex mismo se reescribe.

### 2. El pipeline como gradiente, no como tubería

Una tubería tiene diámetro fijo, presión constante y output predecible. Un gradiente es distinto:

- La velocidad de tránsito entre niveles varía según el proyecto.
- Algunos proyectos se estancan en un nivel indefinidamente (y está bien).
- La dirección puede invertirse (volver de dev a modelado).
- No hay cuota de "throughput". No se mide éxito por volumen.

### 3. Gobernanza como membrana semipermeable

La gobernanza no bloquea el paso — lo **regula**. Es una membrana que deja pasar proyectos en una dirección (hacia mayor resolución) bajo condiciones específicas, pero también permite el flujo inverso cuando es necesario (reversión, compostaje).

### 4. Independencia de niveles

Cada nivel opera con reglas distintas. `1-workspace` no tiene reglas. `3-dev` exige hipótesis explícitas. `4-activos` exige autonomía demostrada. Esto permite que coexistan proyectos en estados muy distintos sin que las restricciones de un nivel contaminen a otro.

### 5. La no-linealidad como feature

A diferencia de un pipeline industrial (donde la no-linealidad es un bug), aquí es intencional:

- Un proyecto puede saltar niveles si cumple criterios de salida anticipadamente.
- Un proyecto puede retroceder si nueva información invalida suposiciones previas.
- Un proyecto en `3-dev` puede decidir que su destino no es `4-activos` sino `5-infraestructura` — y eso cambia sus criterios de validación.

---

## Invariantes

Estas propiedades deben mantenerse incluso si la estructura de directorios cambia:

1. **Separación gobernanza/ejecución**: quien decide no es quien ejecuta (aunque sean la misma persona en distintos modos cognitivos).
2. **Codex fuera del pipeline**: el observador no es un paso más en la cadena.
3. **El nombre del directorio es el estado**: sin metadata externa que pueda desincronizarse.
4. **Criterios de salida, no de entrada**: la exigencia está en promocionar, no en entrar.
5. **Ciclo de revisión periódico**: sin revisión, la gobernanza se vuelve decorativa.

---

## Lo que esta topología permite

- **Coexistencia de caos y orden**: `1-workspace` puede ser puro ruido mientras `4-activos` opera con precisión. No se contaminan.
- **Evolución sin plan maestro**: el sistema se reescribe a sí mismo cuando un experimento en `3-dev` cristaliza en `4-activos` y eventualmente modifica `0-gobernanza`.
- **Fracaso productivo**: `6-archivo` + compostaje convierten el error en nutriente.
- **Escala sin complejidad organizacional**: agregar un proyecto no requiere crear nuevas estructuras de gestión — solo asignarle un nivel.
