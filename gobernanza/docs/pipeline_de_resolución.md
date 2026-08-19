# Pipeline de Resolución

> **No es un proceso lineal. Es un gradiente de entropía decreciente.**
>
> Cada nivel representa un estado de la materia — desde el caos gaseoso del workspace hasta el fósil inerte del archivo. Lo que fluye entre niveles no son "tareas completadas", sino **resolución ganada**.

---

## Visión general

```
ALTA ENTROPÍA (baja resolución)
    │
    1-workspace     ← Caos, ideas crudas, todo vale
    │
    2-modelado      ← Se detectan patrones, flujos, stocks
    │
    3-dev           ← Se interviene, se testean hipótesis
    │
    4-activos       ← Cristaliza: opera sin el creador
    │
    5-infraestructura ← Modifica al sistema mismo
    │
    6-archivo       ← Compostaje: nutrientes extraídos
    │
BAJA ENTROPÍA (alta resolución)
```

La gobernanza (`0-gobernanza/`) no está en la escala: es el **origen del sistema de coordenadas**. Define los criterios que determinan qué puede moverse y hacia dónde.

El Codex (`codex/`) tampoco está en la escala: es el **observador que emergió de lo observado**. Una Mariposa Potencial que cristalizó y ahora modifica las reglas del sistema que lo creó.

---

## Principios de operación

### 1. No linealidad
El pipeline no fuerza una secuencia. Un proyecto puede:
- Entrar en `2-modelado` si nace con suficiente claridad (bypassea workspace).
- Saltar de `1-workspace` a `3-dev` si los criterios de salida se cumplen sin modelado explícito.
- Retroceder de `3-dev` a `2-modelado` si la hipótesis falla y necesita reformulación.
- Pasar de `3-dev` directamente a `6-archivo` si el experimento enseña que no vale la pena continuar.

### 2. Transiciones gobernadas
Moverse de nivel requiere una decisión explícita (revisión mensual o evaluación formal). Nada se mueve solo por inercia.

### 3. Criterios de salida, no de entrada
Cada nivel define qué debe cumplir un proyecto para **salir** de él, no para entrar. La entrada es permisiva; la salida es exigente.

### 4. El nombre del directorio es el estado
No hace falta un archivo de metadata para saber en qué estado está un proyecto. Su ubicación en el sistema de archivos **es** su estado. Esto reduce fricción de clasificación y evita desincronización entre metadata y realidad.

---

## Niveles

### Nivel 1 — Workspace (Caos)

**Directorio:** `1-workspace/`

**Estado de la materia:** Gas — partículas chocando sin estructura.

**Pregunta clave:** ¿Qué está emergiendo?

**Qué va aquí:**
- Clones de repositorios externos para exploración.
- Scripts sueltos, pruebas rápidas, experimentos de un día.
- Proyectos recién migrados cuyo potencial aún no se entiende.
- Investigación exploratoria sin hipótesis clara.
- Referencias externas, datasets, material crudo.

**Reglas:**
- Sin estructura obligatoria.
- Sin criterios de calidad.
- Sin compromiso de continuidad.
- Única regla: no interferir con otros proyectos.

**Criterios de salida:**
- Se detecta un patrón, flujo o estructura recurrente.
- Se formula al menos una hipótesis comprobable.
- Hay una intuición fundada de que esto podría ser activo, infraestructura o mariposa.

**Destino típico:** `2-modelado/`

**Riesgo del nivel:** acumulación indefinida. Si un proyecto lleva mucho tiempo en workspace sin que emerja claridad, considerar moverlo directamente a `6-archivo/`. No todo necesita ser entendido.

---

### Nivel 2 — Modelado (Observación)

**Directorio:** `2-modelado/`

**Estado de la materia:** Líquido — flujos visibles, patrones de movimiento detectables.

**Pregunta clave:** ¿Qué sistema está produciendo este resultado?

**Qué va aquí:**
- Proyectos donde ya se detectaron patrones pero aún no se interviene.
- Modelado de flujos, stocks, feedback loops.
- Documentación de observaciones, diagramas, mapas del sistema.
- Evaluación preliminar de potencial: ¿activo, infraestructura o mariposa?

**Reglas:**
- Debe existir documentación de lo observado (aunque sea mínima).
- Debe haber una hipótesis sobre qué crea valor en este sistema.
- No se construye para producción. Se construye para entender.

**Criterios de salida:**
- La hipótesis es lo suficientemente concreta para ser testeada.
- Se identificó el vector de potencial: activo, infraestructura o mariposa.
- El proyecto converge con la Estrella Polar (evaluación de gobernanza).
- Se estimó el costo energético de la intervención y es sostenible.

**Destino típico:** `3-dev/`

**Riesgo del nivel:** parálisis por análisis. Modelar no es el objetivo — es el medio. Si el modelado no produce una hipótesis testeable en un tiempo razonable, el proyecto no está listo para consumir más recursos. Evaluar compostaje o retorno a workspace.

---

### Nivel 3 — Dev (Intervención)

**Directorio:** `3-dev/`

**Estado de la materia:** Sólido blando — estructura provisional, maleable, bajo prueba.

**Pregunta clave:** ¿Qué hipótesis estoy validando?

**Qué va aquí:**
- Desarrollo activo: código, documentación, prototipos funcionales.
- Experimentos con intervención directa sobre el sistema modelado.
- Pruebas de apalancamiento, feedback loops, palancas de cambio.
- Iteraciones rápidas: construir → medir → aprender.

**Reglas:**
- Debe existir una hipótesis explícita y criterios de validación.
- Debe usarse la Plantilla de Evaluación o `evaluacion.yaml`.
- Iteraciones documentadas (no cada línea de código, pero sí cada ciclo de aprendizaje).
- El fracaso es un output válido: enseña los límites del modelo.

**Criterios de salida hacia `4-activos/`:**
- El experimento reduce fricción de forma consistente ($\Delta F < 0$).
- Opera sin intervención constante (tiende a la autonomía).
- El patrón es transferible (funciona en al menos 2 contextos).
- Su mantenimiento no consume más de lo que produce.

**Criterios de salida hacia `5-infraestructura/`:**
- Además de lo anterior: el output modifica o enriquece al ecosistema Bildung mismo.
- Crea nuevas conexiones entre componentes del sistema.
- Reduce fricción en los flujos internos del Lab.
- Es personalizado para Bildung (si es genérico, probablemente sea activo, no infraestructura).

**Criterios de salida hacia `6-archivo/`:**
- La hipótesis fue falseada (el experimento "falló").
- El costo energético supera el valor potencial.
- Genera más dependencia que autonomía.
- En todos los casos: **se extraen lecciones antes de archivar** (anti-patrones, límites del modelo, telemetría de fricción).

**Riesgo del nivel:** no saber terminar. El dev puede volverse adictivo. La gobernanza debe forzar la pregunta: ¿este experimento ya enseñó lo que tenía que enseñar?

---

### Nivel 4 — Activos (Cristalización)

**Directorio:** `4-activos/`

**Estado de la materia:** Cristal — estructura estable, genera valor.

**Pregunta clave:** ¿Qué opera sin mí?

**Qué va aquí:**
- Sistemas que generan valor sin intervención constante del creador.
- Pueden requerir mantenimiento básico (delegable).
- Scripts, herramientas, metodologías, flujos automatizados.
- Proyectos cuya hipótesis fue validada y ahora están en operación.

**Reglas:**
- Operación documentada (otra persona o agente debería poder mantenerlo).
- Monitoreo mínimo: ¿sigue funcionando? ¿sigue alineado con la Estrella Polar?
- Si algo en este nivel empieza a fallar sistemáticamente, evaluar si vuelve a `3-dev` (refactorización) o se archiva.

**Criterios de salida hacia `5-infraestructura/`:**
- Demuestra que no solo genera valor — **modifica la forma en que el ecosistema opera**.
- Enriquece a Bildung: crea nuevas conexiones, reduce fricción sistémica, desvía flujos.
- Su existencia habilita experimentos que antes eran imposibles.

**Criterios de salida hacia `6-archivo/`:**
- Dejó de ser relevante (cambió el entorno, apareció una alternativa mejor).
- Fue reemplazado por otro activo o infraestructura que absorbió su función.
- Su mantenimiento ya no se justifica.

**Riesgo del nivel:** dependencia invisible. Un activo que "funciona solo" puede generar una dependencia no detectada. Si el activo falla y nadie sabe cómo opera, no era tan autónomo como parecía.

---

### Nivel 5 — Infraestructura (Cristal funcional)

**Directorio:** `5-infraestructura/`

**Estado de la materia:** Cristal funcional — no solo estable, sino que **transforma lo que toca**.

**Pregunta clave:** ¿Qué modifica al sistema mismo?

**Qué va aquí:**
- Sistemas que enriquecen Bildung: servidores, entornos de desarrollo, herramientas de captura.
- Componentes que crean nuevas conexiones o reducen fricción en los flujos internos.
- Infraestructura personalizada para el ecosistema (no genérica).
- Elementos cuya existencia modifica *cómo* se construye en el Lab.

**Diferencia con Activos:**
- Un **activo** genera valor para el mundo exterior (o para el creador directamente).
- La **infraestructura** modifica la capacidad del ecosistema para generar valor. Es un multiplicador de segundo orden.

**Reglas:**
- Debe ser transferible al menos en parte (no completamente acoplada a una máquina o contexto).
- Si un componente de infraestructura es idéntico a lo que existiría en cualquier otro sistema, probablemente es solo un activo.
- Documentación de arquitectura: ¿qué flujos modifica? ¿qué nuevas conexiones crea?

**Criterios de salida hacia `6-archivo/`:**
- Fue reemplazada por infraestructura superior.
- Se volvió obsoleta (cambió el stack tecnológico).
- Su mantenimiento consume más de lo que su multiplicación produce.

**Riesgo del nivel:** infraestructura zombie. Sistemas que nadie usa pero que se mantienen "por si acaso". Consumen energía sin generar multiplicación. La gobernanza debe auditar este nivel con especial atención.

---

### Nivel 6 — Archivo (Compostaje)

**Directorio:** `6-archivo/`

**Estado de la materia:** Fósil — inerte, informativo, no ejecutable.

**Pregunta clave:** ¿Qué aprendimos?

**Qué va aquí:**
- Proyectos completos que terminaron su ciclo de vida.
- Experimentos falseados cuyas lecciones fueron extraídas.
- Versiones deprecadas de proyectos que siguen vivos (en ese caso, se anidan en `proyecto/archivo/`).

**Reglas:**
- Todo proyecto archivado debe tener un resumen de cierre: ¿qué se aprendió? ¿por qué terminó?
- El aprendizaje extraído debe inyectarse en el Codex (anti-patrones, límites, restricciones).
- Si el proyecto es una versión deprecada de algo que sigue vivo, se archiva dentro del proyecto activo, no aquí.

**No se archiva:**
- Lo que no se entiende (eso va a workspace o se descarta).
- Lo que no se evaluó (sin extracción de lecciones, no hay compostaje real).

**Riesgo del nivel:** archivo como vertedero. Tirar cosas aquí sin procesar el aprendizaje. El costo no es espacio en disco — es **amnesia institucional**.

---

## El Codex como destino transversal

El Codex no es un nivel del pipeline. Es un peer del Lab. Pero el pipeline lo alimenta:

- De `3-dev`: patrones validados que sobrevivieron al testeo isomórfico.
- De `4-activos`: conceptos que demostraron generar valor en operación real.
- De `5-infraestructura`: reglas nuevas sobre cómo opera el ecosistema.
- De `6-archivo`: anti-patrones y límites extraídos del compostaje.

A su vez, el Codex modifica la gobernanza (afina criterios, revela nuevas direcciones estratégicas) y la gobernanza decide qué se compila al Codex. Es un bucle de resolución creciente.

---

## Diagrama de flujo

```
                    ┌─────────────────┐
                    │  0-gobernanza   │
                    │  (criterios)    │
                    └────────┬────────┘
                             │ decide
    ┌────────────────────────┼────────────────────────┐
    ▼                        ▼                        ▼
┌───────────┐         ┌───────────┐            ┌───────────┐
│1-workspace│────────▶│2-modelado │───────────▶│  3-dev    │
│  (caos)   │         │(observar) │            │(intervenir)│
└───────────┘         └───────────┘            └─────┬─────┘
                                                     │
                          ┌──────────────┬───────────┼───────────┐
                          ▼              ▼           │           ▼
                   ┌──────────┐  ┌──────────────┐   │    ┌──────────┐
                   │4-activos │  │5-infraestructura│  │    │6-archivo │
                   │(opera)   │  │(modifica)     │   │    │(aprende) │
                   └──────────┘  └──────────────┘   │    └──────────┘
                          │              │           │           │
                          └──────────────┴───────────┴───────────┘
                                         │
                                         ▼
                                  ┌───────────┐
                                  │  codex/   │
                                  │(ontología)│
                                  └───────────┘
```

---

## Anti-patrones del pipeline

| Anti-patrón | Síntoma | Corrección |
|---|---|---|
| **Workspace infinito** | Proyectos que llevan meses en `1-` sin claridad | Archivar sin culpa. No todo necesita ser entendido. |
| **Modelado sin intervención** | Documentación perfecta, cero experimentos | Forzar prototipo mínimo o archivar. |
| **Dev eterno** | Iterar sin criterio de salida | Definir deadline o criterio de falseación. |
| **Todo es activo** | Promover sin validación de autonomía real | Auditar: ¿esto realmente opera sin mí? |
| **Infraestructura zombie** | Sistemas que nadie usa pero se mantienen | Preguntar: ¿qué experimento nuevo habilitó esto en los últimos 3 meses? |
| **Archivo sin lecciones** | Carpeta `6-archivo/` llena, Codex sin anti-patrones nuevos | Extraer antes de archivar. Siempre. |
