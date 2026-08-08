# self-driving-lab

> **Objetivo:** hacer que Bildung sea machine-parseable — que agentes y humanos puedan leer, consultar, actualizar y navegar el ecosistema con fricción mínima.

## Qué es este proyecto

Un laboratorio dentro del Laboratorio. Experimenta con la hipótesis de que Bildung puede volverse un sistema operativo cognitivo distribuido, donde agentes de IA operen como nodos semiautónomos gobernados por la ontología.

Actualmente en `1-workspace` — fase de exploración activa con 3 experimentos internos.

## Experimentos activos

| # | Experimento | Hipótesis | Vector |
|---|---|---|---|
| 01 | Branch Lab | Un agente puede operar como rama semiautónoma del Lab | Mariposa |
| 02 | EAL | El cuello de botella no es el contexto sino la entropía de la información | Infraestructura |
| 03 | Delegación Ontológica | Es posible delegar exploración sin delegar gobernanza | Mariposa |

## Direcciones estratégicas

- Sistemas
- Conocimiento
- Ecosistema

## Estructura

```
self-driving-lab/
├── .gob/          ← gobernanza interna
├── workdir/       ← experimentos activos (3)
├── docs/          ← conocimiento normalizado + referencias
└── archivo/       ← experimentos cerrados
```

## Estado

**Nivel actual:** 1-workspace → migrando a 2-modelado  
**Próximo paso:** formalizar hipótesis, iniciar bitácoras, definir criterios de salida por experimento.
