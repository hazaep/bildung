---
tags: [ontología, gobernanza, laboratorio]
date: 2025-08-02
status: v0
---

# Infraestructura

## Definición operativa

La infraestructura es un **componente cristalizado del ecosistema [[Bildung/Lab/codex/Ontología/Bildung]] que no solo genera valor — modifica la capacidad del sistema para generar valor**. Es un multiplicador de segundo orden: mientras un [[Bildung/Lab/codex/Ontología/Activo]] produce output, la infraestructura **altera las reglas, los flujos y las conexiones** que determinan qué puede producirse.

Si Bildung fuera una fábrica, los activos serían los productos. La infraestructura sería la cinta transportadora, el sistema eléctrico, el layout del taller — cosas que no se venden, pero sin las cuales nada se produce igual.

## Pregunta clave

> ¿Qué modifica al sistema mismo?

## Diferencia con Activo

| | Activo | Infraestructura |
|---|---|---|
| **Genera...** | Valor para el exterior (o para el creador) | Capacidad interna del ecosistema |
| **Responde a...** | ¿Qué opera sin mí? | ¿Qué modifica cómo opero? |
| **Su ausencia...** | Se pierde un output | Se pierde una *forma de producir* |
| **Ejemplo** | Un script que automatiza reportes | El servidor que ejecuta todos los scripts |
| **Nivel del pipeline** | 4 | 5 |

Un mismo proyecto puede ser ambas cosas. `docker-server` es activo (sirve archivos) e infraestructura (habilita despliegues, reduce fricción de experimentación). La distinción no es ontológica sino funcional: **¿a quién le sirve? ¿Al mundo exterior o al ecosistema Bildung?**

## Propiedades

1. **Multiplicador de segundo orden**: no produce output directo — produce *capacidad de producir output*.
2. **Personalizada**: no es genérica. Dos servidores idénticos pueden ser infraestructura en Bildung y solo un gasto en otro contexto. La diferencia es la integración con el ecosistema.
3. **Transferible parcialmente**: la infraestructura puede documentarse y replicarse, pero su valor real está en cómo se acopla a Bildung específicamente.
4. **Habilita experimentos**: la infraestructura reduce la [[Bildung/Lab/codex/Ontología/Fricción Sistémica]] de iniciar nuevos experimentos. Un entorno de desarrollo bien configurado no es un activo — pero sin él, cada experimento nuevo cuesta el doble.

## Taxonomía

| Tipo | Ejemplos Bildung | Qué modifica |
|---|---|---|
| **Computacional** | Servidores, entornos de desarrollo, CI/CD | Velocidad y fricción de experimentación |
| **Cognitiva** | [[Bildung/Lab/codex/Ontología/Codex Bildung\|Codex]], Ontología, metodologías de modelado | Resolución con que se observa el sistema |
| **De captura** | Herramientas de ingesta de datos, pipelines de procesamiento | Qué entra al Lab y con qué estructura |
| **De conexión** | APIs, protocolos, estándares de documentación | Cómo se comunican los componentes entre sí |
| **Física** | Hardware, redes, espacio de trabajo | El sustrato material del ecosistema |

## Relación con las Escuelas

| Escuela | Rol |
|---|---|
| Escuela 2 | Construye infraestructura (Materialización) |
| Escuela 4 | Decide qué infraestructura merece mantenerse |
| Escuela 5 | Asegura que la infraestructura opere sin dependencia del creador |

## El pipeline y la infraestructura

En el gradiente de resolución del [[Bildung/Lab/codex/Ontología/Laboratorio Bildung|Laboratorio]], la infraestructura ocupa el nivel 5 — un paso más allá del activo:

```
4-activos:        Cristal estable. Opera sin mí. Genera valor.
5-infraestructura: Cristal funcional. Modifica al sistema.
                   Enriquece Bildung. Crea conexiones nuevas.
```

Para que un proyecto se promueva de `3-dev` a `5-infraestructura` (saltando `4-activos` o después de haber sido activo), debe demostrar que su output **modifica los flujos internos del Lab**: reduce fricción sistémica, crea nuevas conexiones entre componentes, o desbloquea experimentos que antes eran imposibles.

## Anti-patrón

**Infraestructura zombie**: sistemas que se mantienen "por si acaso" pero que no habilitan ningún experimento nuevo. Consumen energía sin generar multiplicación. La gobernanza debe auditar este nivel con la pregunta: "¿qué experimento nuevo habilitó esto en los últimos 3 meses?"

**Confundir herramienta con infraestructura**: una herramienta se usa. La infraestructura **sostiene**. Usar Docker para un proyecto es herramienta. Tener un entorno Docker estandarizado donde todos los experimentos nacen con la misma base es infraestructura.

**Infraestructura sin documentación**: si solo el creador sabe cómo opera, no es infraestructura — es conocimiento tácito con dependencia personal. La infraestructura debe ser operable por otro (humano o agente).

## Relaciones

- [[Bildung/Lab/codex/Ontología/Activo]] — el concepto del que se distingue; un mismo proyecto puede ser ambos
- [[Bildung/Lab/codex/Ontología/Laboratorio Bildung]] — el ecosistema que la infraestructura modifica
- [[Bildung/Lab/codex/Ontología/Cristalización]] — el proceso por el cual un experimento se convierte en infraestructura
- [[Bildung/Lab/codex/Ontología/Codex Bildung]] — el caso más extremo de infraestructura cognitiva: emergió del Lab y ahora reescribe sus reglas
- [[Bildung/Lab/codex/Ontología/Fricción Sistémica]] — lo que la infraestructura reduce sistémicamente
- [[Bildung/Lab/codex/Ontología/Gobernanza Adaptativa]] — decide qué infraestructura se mantiene, qué se composta
- [[Bildung/Lab/codex/Ontología/Normalización]] — la infraestructura cognitiva requiere normalización previa
