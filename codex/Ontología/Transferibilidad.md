---
tags: [ontología, avanzado, filtro]
date: 2025-07-28
status: v0
---

# Transferibilidad

## Definición operativa

La transferibilidad (o transferibilidad topológica) es la propiedad de un patrón, modelo o concepto que **mantiene su validez estructural cuando se trasplanta de un dominio a otro fundamentalmente distinto**.

Es el criterio de validación más importante del [[Marco de Investigación Bildung]] (Filtro 2) y el mecanismo que distingue un [[Isomorfismo]] genuino de una simple analogía superficial.

## Pregunta clave

> ¿Funciona esto en software, negocios, personas Y sistemas?

Si la respuesta es sí, el patrón tiene alta probabilidad de ser un **invariante estructural**, no ruido de dominio.

## El test de transferibilidad

Un concepto es transferible si:

1. Se formuló originalmente en un dominio A
2. Se aplica en un dominio B (estructuralmente distinto de A)
3. Conserva su poder explicativo/predictivo en B
4. Idealmente, sobrevive en un tercer dominio C

| Concepto | Dominio A | Dominio B | Dominio C | ¿Transferible? |
|---|---|---|---|---|
| [[Sistema]] (stocks, flujos, feedback) | Ingeniería | Biología | Economía | ✅ |
| Cuello de botella | Manufactura | Software | Cognición personal | ✅ |
| [[Compostaje]] | Biología | [[Bildung]] | Gestión de proyectos | ✅ |
| "Sinergia" (como buzzword) | Management | — | — | ❌ (no sobrevive al testeo riguroso) |

## Rol en Bildung

La transferibilidad es el **anticuerpo contra la deuda cognitiva**. El [[Codex Bildung]] no acepta un modelo solo porque funcione en un dominio. Exige que funcione en al menos tres antes de compilarlo como invariante.

Esto evita:

- **Verdades locales**: modelos que solo aplican en el contexto donde se descubrieron
- **Sesgo de dominio**: pensar que la estructura de tu industria es la estructura del mundo
- **Falsos isomorfismos**: metáforas atractivas pero estructuralmente vacías

## Transferibilidad y [[Apalancamiento]]

Un concepto transferible tiene apalancamiento masivo: cada dominio donde se aplica multiplica su valor sin coste adicional. El [[Isomorfismo]] es la detección; la transferibilidad es la validación de que la detección fue correcta.

## Anti-patrón

**Transferibilidad forzada**: estirar un concepto para que "aplique" en un dominio donde no encaja. Si necesitas ignorar partes importantes del dominio B para que el modelo "funcione", no es transferible.

**Rechazo de transferibilidad legítima**: asumir que "mi dominio es especial" y rechazar patrones que sí aplicarían porque "aquí es diferente". La especificidad de dominio es real, pero también es la excusa favorita para no aprender de otros campos.

## Relaciones

- [[Isomorfismo]] — la transferibilidad es la validación del isomorfismo
- [[Marco de Investigación Bildung]] — el filtro que exige transferibilidad
- [[Codex Bildung]] — solo almacena conceptos transferibles como invariantes
- [[Metamodelos]] — el catálogo de patrones que han demostrado transferibilidad
- [[Complejidad]] — los sistemas complejos de distintos dominios frecuentemente comparten estructuras transferibles
