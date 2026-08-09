# AGENTS.md — Bildung

Este documento permite a un agente de IA entender y operar dentro del ecosistema
Bildung. No es un prompt. Es un manual de onboarding que describe la arquitectura,
reglas, flujos y criterios de decisión del sistema.

---

## Project Overview

Bildung es un **ecosistema de sistemas** organizado como un Sistema Adaptativo
Complejo (CAS) de I+D Ontológico. Su propósito es transformar complejidad en
libertad. No es una empresa, ni una metodología, ni un sistema de productividad.

El ecosistema se organiza en tres capas pre-Lab y un pipeline de resolución:

```
Fundación (Docs/)          ← propósito y principios
    ↓
Gobernanza (0-gobernanza/) ← criterios de asignación de recursos
    ↓
Pipeline (1- a 6-)         ← ejecución bajo criterios
    ↓
Codex (codex/)             ← ontología ejecutable, peer del Lab
```

## Essential Concepts

Antes de operar, el agente debe comprender estos conceptos. Las definiciones
canónicas están en `Lab/codex/Ontología/`.

| Concepto | Pregunta que responde |
|---|---|
| **Estrella Polar** | ¿Qué dirección orienta todas las decisiones? → Transformar complejidad en libertad |
| **Pipeline de Resolución** | ¿En qué estado está un proyecto? → Niveles 1 a 6 |
| **Codex** | ¿Dónde está la ontología compilada? → `Lab/codex/Ontología/` |
| **Gobernanza** | ¿Qué merece recursos? → `Lab/0-gobernanza/` |
| **Activo** | ¿Qué opera sin intervención? → Nivel 4 |
| **Infraestructura** | ¿Qué modifica al sistema mismo? → Nivel 5 |
| **Compostaje** | ¿Qué se hace con lo que no funciona? → Archivar con lecciones extraídas |
| **Cristalización** | ¿Qué se hace con lo que sí funciona? → Promover a Activo o Infraestructura |
| **Mariposa Potencial** | ¿Qué podría desbloquear esto que hoy no imagino? → Opcionalidad de 2º orden |

## Architecture: The Pipeline

El Laboratorio Bildung es un gradiente de resolución con 7 niveles (0-6):

| Nivel | Directorio | Estado | Pregunta clave | Criterio de salida |
|---|---|---|---|---|
| 0 | `0-gobernanza/` | Meta-nivel | ¿Qué merece recursos? | — (define criterios) |
| 1 | `1-workspace/` | Gas (caos) | ¿Qué está emergiendo? | Se detecta patrón o hipótesis |
| 2 | `2-modelado/` | Líquido | ¿Qué patrón detecto? | Hipótesis concreta, vector identificado |
| 3 | `3-dev/` | Sólido blando | ¿Qué hipótesis valido? | Resultado validado o falseado |
| 4 | `4-activos/` | Cristal | ¿Qué opera sin mí? | Demuestra modificar el sistema → infraestructura |
| 5 | `5-infraestructura/` | Cristal funcional | ¿Qué modifica al sistema? | Queda obsoleto → archivo |
| 6 | `6-archivo/` | Fósil | ¿Qué aprendimos? | — (terminal) |

**Reglas del pipeline:**
- El pipeline NO es lineal. Un proyecto puede saltar niveles, retroceder, o tomar rutas no secuenciales.
- Moverse de nivel requiere decisión explícita (revisión mensual o evaluación formal).
- La ubicación en el sistema de archivos ES el estado. No hay metadata externa.
- Ver `Lab/0-gobernanza/docs/pipeline_de_resolución.md` para criterios detallados.

### Proyectos con estructura interna fractal

Un proyecto en nivel 2+ puede contener múltiples experimentos internos. En ese
caso, usa esta topología:

```
proyecto/
├── README.md
├── evaluacion.yaml
├── .gob/                  ← gobernanza interna mínima
├── workdir/               ← experimentos activos
│   └── NN-experimento/
│       ├── hipotesis.md   ← ~30 líneas: hipótesis + criterios
│       └── bitacora.md    ← una línea por iteración
├── docs/                  ← conocimiento normalizado
└── archivo/               ← experimentos cerrados
```

**Reglas para experimentos internos:**
- Solo dos archivos obligatorios: `hipotesis.md` y `bitacora.md`.
- `evaluacion.yaml` del contenedor solo se completa en transiciones de fase.
- Si un experimento hijo crece demasiado, se gradúa a proyecto independiente.
- Ver `Lab/0-gobernanza/docs/estructura_interna_proyecto_experimental.md`.

## Gobernanza: Criterios de Decisión

Todo proyecto o experimento debe evaluarse contra la **Estrella Polar**:

> ¿Esto aumenta o disminuye mi libertad futura?

### Las 5 Direcciones Estratégicas

Todo proyecto debe fortalecer al menos una:

| Dirección | Objetivo |
|---|---|
| **Capital** | Independencia financiera |
| **Conocimiento** | Expandir resolución |
| **Sistemas** | Reducir dependencia operativa |
| **Ecosistema** | Personas y proyectos alineados |
| **Sostenibilidad** | Proteger el ecosistema interno |

### Filtro de entrada (5 preguntas)

Antes de asignar recursos a una idea nueva:

1. ¿Converge con la Estrella Polar?
2. ¿A qué dirección estratégica contribuye?
3. ¿Es activo o consumo?
4. ¿Cuál es el costo energético?
5. ¿Qué desbloquea?

### Ciclo de revisión

- **Bitácora mensual** en `Lab/0-gobernanza/bitacora/`. Preguntas: ¿Qué aumentó mi libertad? ¿Qué la redujo? ¿Qué drenó energía? ¿Qué debería recibir más/menos recursos?
- **Evaluación formal** con `Plantilla - Evaluación de Experimentos y Proyectos v1.0.md` solo en transiciones de fase (promoción, archivo).

## The Codex

El Codex (`Lab/codex/`) es la ontología ejecutable del ecosistema. No es una
biblioteca de notas — es un grafo topológico donde cada concepto existe en
función de sus conexiones con otros conceptos.

### Ontología

Las definiciones canónicas viven en `Lab/codex/Ontología/`. Cada archivo sigue
este formato:

```markdown
---
tags: [ontología, categoría]
date: YYYY-MM-DD
status: v0 | v1
---

# Concepto

## Definición operativa
...

## Pregunta clave
> ...

## Relaciones
- [[Concepto relacionado]]
```

### Niveles de integración

| Nivel | Estado | Descripción |
|---|---|---|
| 0 | Dato | Información cruda |
| 1 | Concepto | Definido |
| 2 | Modelo | Aplicado en ≥1 dominio |
| 3 | Herramienta | Usado habitualmente |
| 4 | Infraestructura | Parte del sistema |

### Reglas del Codex

- **No modificar la ontología sin pasar por Gobernanza.** Solo el arquitecto (humano) aprueba cambios al Codex.
- **No introducir conceptos incompatibles.** Toda definición nueva debe conectar con ≥2 conceptos existentes.
- **El Codex es un peer del Lab, no un subcomponente.** Co-evoluciona con Gobernanza.

## Documentación del Proyecto

### Documentos obligatorios por nivel

| Nivel | Requisitos |
|---|---|
| 1-workspace | Ninguno |
| 2-modelado | `README.md` (mínimo: ¿qué observo?, ¿qué patrón detecto?, hipótesis, posible vector) |
| 3-dev | `README.md` + `evaluacion.yaml` + bitácora de iteraciones |
| 4-activos | `README.md` + `evaluacion.yaml` + instrucciones de operación |
| 5-infraestructura | `README.md` + `evaluacion.yaml` + documentación de arquitectura |
| 6-archivo | `CIERRE.md` (¿qué se aprendió? ¿por qué terminó?) |

### Formatos de documentación

**`evaluacion.yaml`** — evaluación formal del proyecto. Estructura canónica en
`Lab/0-gobernanza/evaluacion.yaml`. Solo se completa en transiciones de fase.

**`hipotesis.md`** (experimentos internos) — ~30 líneas:
```markdown
# [Nombre]
## Hipótesis
> [Una frase. Clara. Falsable.]
## Criterios de validación
- [¿Cómo sabré si funcionó?]
## Criterios de falseación
- [¿Cómo sabré si falló?]
## Vector de potencial
- [ ] Activo  [ ] Infraestructura  [ ] Mariposa
## ¿Qué desbloquea si funciona?
```

**`bitacora.md`** — una línea por iteración:
```markdown
- YYYY-MM-DD: Hice X. Resultado: Y. Aprendizaje: Z. Próximo: W.
```

## Key Patterns

### Patrón: Detección de Asimetría → Captura de Valor

El valor en Bildung no se crea — se detecta en la asimetría entre lo que alguien
tiene (sin saberlo) y lo que alguien necesita (sin saber cómo acceder). Ver
`Lab/codex/Ontología/Asimetría.md` y `Lab/codex/Ontología/Valor.md`.

### Patrón: Fractalidad del Pipeline

La misma estructura que opera entre proyectos (workspace → modelado → dev → activos
→ infraestructura → archivo) puede operar dentro de un proyecto con suficiente
complejidad. Si un proyecto genera ≥2 experimentos con hipótesis independientes,
aplicar estructura interna fractal.

### Patrón: No linealidad

El pipeline no es una tubería. Un proyecto puede:
- Saltar de `1-workspace` a `3-dev` si la hipótesis es clara.
- Retroceder de `3-dev` a `2-modelado` si la hipótesis necesita reformulación.
- Ir de `3-dev` a `6-archivo` si el experimento falsea la hipótesis.

## Anti-Patterns (lo que NUNCA se debe hacer)

| Anti-patrón | Por qué es dañino |
|---|---|
| **Acumular sin decidir** | Proyectos que no se promueven ni se compostan. Drenan atención. |
| **Codex como biblioteca** | Definiciones sin conexiones activas. Un concepto sin bordes no pertenece al Codex. |
| **Gobernanza sin poda** | Decir "sí" a todo. Si nada se composta, el sistema se ahoga. |
| **Modificar Codex sin Gobernanza** | El Codex solo se modifica con aprobación explícita del arquitecto. |
| **Evaluar cada iteración** | `evaluacion.yaml` solo en transiciones de fase. Para el día a día, usar bitácora. |
| **Ignorar la Estrella Polar** | Un proyecto que genera dinero pero consume libertad es una derrota en Bildung. |

## Métricas del Sistema

| KPI | Definición |
|---|---|
| ΔF (Fricción Sistémica) | Reducción de resistencia en flujos |
| Mariposa Potencial | Caminos nuevos que habilita un experimento |
| ΔA (Autonomía) | Grado en que el sistema opera sin el arquitecto |
| Índice de Libertad Bildung | Si todo colapsa mañana, ¿cuánto tiempo puedo seguir haciendo lo que importa? |

---

## Resumen operativo para el agente

Antes de cualquier acción, verificar:

1. **¿Estoy modificando el Codex?** → Solo con aprobación explícita del arquitecto.
2. **¿Estoy moviendo un proyecto de nivel?** → Solo con evaluación formal.
3. **¿Estoy documentando una iteración?** → Usar `bitacora.md` (una línea).
4. **¿Estoy evaluando formalmente?** → Usar `evaluacion.yaml` (solo en transiciones).
5. **¿Mi acción converge con la Estrella Polar?** → "Transformar complejidad en libertad."
