# 🧬 Módulo 8: Evolución y Genética

---

## 🔄 Darwin, pero como sistema complejo

La evolución por selección natural es, en esencia, **el proceso de auto-organización más poderoso del universo conocido**. Y desde la perspectiva de la complejidad, es un **algoritmo** — el algoritmo que descubrió Darwin sin saber que era un algoritmo.

> *"Nothing in biology makes sense except in the light of evolution"* — Theodosius Dobzhansky

Y desde la ciencia de la complejidad:

> *"Nothing in complex systems makes sense except in the light of evolution"* — versión adaptada

---

## 🧪 La evolución como algoritmo

Darwin nos dio tres ingredientes que, juntos, forman una máquina de crear complejidad:

### 1️⃣ Variación
Los individuos de una población no son idénticos. Hay mutaciones, recombinación genética, errores de copia.

### 2️⃣ Selección
No todos sobreviven y se reproducen igual. Los que están mejor "ajustados" al entorno dejan más descendencia.

### 3️⃣ Herencia
Los hijos se parecen a los padres. Las características exitosas se transmiten.

```
Variación → Selección → Herencia → Repetir
```

Eso es **todo**. No hay un diseñador. No hay un objetivo. No hay una «dirección». Solo un ciclo ciego que, iterado millones de veces, produce:

- El ojo humano 🧐
- El vuelo de las aves 🦅
- El sistema inmune 🛡️
- La consciencia 🧠
- Tú leyendo esto

---

## 💻 Algoritmos Genéticos (AG)

En 1975, **John Holland** — también del Santa Fe Institute — tuvo una idea genial:

> Si la evolución natural puede resolver problemas tan complejos... ¿por qué no **simularla** para resolver problemas de computación?

Nacen los **Algoritmos Genéticos**.

### ¿Cómo funciona un AG?

```
1. GENERACIÓN 0:   Crea una población de soluciones al azar
2. EVALUACIÓN:     Prueba qué tan buena es cada solución ("fitness")
3. SELECCIÓN:      Las mejores soluciones se reproducen más
4. CRUCE (crossover):  Mezcla dos soluciones padres → hijos
5. MUTACIÓN:       Introduce pequeños cambios aleatorios
6. NUEVA GENERACIÓN:  Vuelve al paso 2
```

### Representación típica

Cada solución es un **cromosoma** — usualmente una cadena de bits (10110100...) o una secuencia de valores.

```
Padre 1:  1011|0100
Padre 2:  0010|1101
                  ↓ cruce
Hijo 1:   1011|1101
Hijo 2:   0010|0100
                  ↓ mutación (ocasional)
Hijo 1:   1011|1101 → 1011|1111  (un bit volteado)
```

### ¿Para qué sirven los AG?

| Problema | Ejemplo |
|----------|---------|
| Optimización | Encontrar la mejor ruta para un repartidor (TSP) |
| Diseño | Forma de un ala de avión con mínima resistencia |
| Aprendizaje automático | Evolucionar redes neuronales (NEAT) |
| Juegos | Evolucionar estrategias para jugar ajedrez, Go, etc. |
| Robótica | Evolucionar el control de un robot que camina |
| Arte | Imágenes generadas por evolución (Picbreeder) |

---

## 🧩 Dilema del Prisionero y evolución de la cooperación

Una de las preguntas más fascinantes que aborda este módulo:

> **¿Por qué existe la cooperación si la selección natural favorece al egoísta?**

En 1981, **Robert Axelrod** organizó un torneo: envía tu estrategia para el Dilema del Prisionero iterado. Ganó la estrategia más simple de todas:

### **Tit-for-Tat** (Ojo por Ojo)

```
1. Empieza cooperando
2. En cada ronda, haz lo que el otro hizo en la ronda anterior
```

Eso es todo. Esta estrategia:
- Es **amable** (empieza cooperando)
- Es **reciproca** (devuelve lo que recibe)
- Es **indulgente** (vuelve a cooperar si el otro coopera)
- Es **clara** (el otro entiende lo que hace)

**Resultado:** en un torneo con estrategias egoístas y complejas, la más simple y cooperativa ganó. La cooperación **emerge** naturalmente cuando hay interacciones repetidas.

---

## 🧠 Fitness landscapes — paisajes de aptitud

Imagina un mapa en 3D donde:
- **Eje X, Y** = posibles genotipos (soluciones)
- **Eje Z** = qué tan buena es cada solución (fitness)

La evolución «camina» por este paisaje, moviéndose cuesta arriba.

Pero aquí hay una trampa:

### Picos locales vs Pico global
```
                  ⛰️
     🏔️           |
     |           ⛰️|
⛰️   |     🏔️     |   ⛰️
| ⛰️ | 🏔️ | ⛰️ | ⛰️ | ⛰️
—————————————————————
```

Un algoritmo genético puede quedar **atrapado en un pico local** — una solución buena, pero no la mejor. Las mutaciones ayudan a escapar de esos picos.

La **dinámica adaptativa** (otro concepto del SFI) estudia exactamente esto: cómo las poblaciones navegan paisajes de fitness que **cambian** porque otros organismos también están evolucionando.

> *"En la evolución, no corres contra el paisaje. El paisaje corre contra ti."* — Van Valen (Carrera armamentista)

---

## 🔬 Ejemplos fascinantes

### 🧪 Evolución digital (Avida / Tierra)
En los 90, **Tom Ray** creó un mundo digital donde programas de computadora competían por tiempo de CPU y memoria. Descubrió que:
- Los programas evolucionaban para ser más eficientes
- Aparecían **parásitos** — programas que robaban el código de otros
- Los huéspedes evolucionaban **resistencia** a los parásitos
- La **cooperación** surgía entre linajes

Evolución real, en una computadora, sin intervención humana.

### 🧬 El ojo — evolución en 400 mil generaciones
Nilsson y Pelger (1994) simularon la evolución del ojo. Partiendo de una mancha fotosensible, paso a paso con pequeñas mejoras... en **400 mil generaciones** (un abrir y cerrar de ojos en tiempo evolutivo) llegaron a un ojo de vertebrado completo.

**La lección:** los órganos «irreductiblemente complejos» sí pueden evolucionar paso a paso si cada paso da una ventaja.

---

## 🧩 Conexión con los módulos anteriores

| Módulo | Conexión con evolución |
|--------|----------------------|
| **1** | La evolución es el proceso que genera sistemas complejos biológicos |
| **2** | Los paisajes de fitness son sistemas dinámicos con atractores |
| **3** | Los árboles filogenéticos son fractales naturales |
| **4** | La selección natural **reduce la entropía local** creando organismos complejos |
| **5** | La evolución **es** auto-organización a escala de poblaciones |
| **6** | Los algoritmos genéticos son MBAs donde los agentes son genomas |
| **7** | Las redes de genes, proteínas y especies son el sustrato de la evolución |

---

## 💎 La gran idea

> **La evolución no es una teoría sobre el pasado. Es un algoritmo atemporal para generar complejidad.**

La selección natural + variación + herencia = una máquina que, dada suficiente tiempo, produce:
- Soluciones óptimas a problemas de ingeniería
- Estructuras de belleza y eficiencia imposibles de diseñar a mano
- Organismos que se adaptan a entornos que cambian constantemente

Y aquí lo más profundo: **la evolución misma evoluciona**. Los mecanismos de variación y herencia cambian con el tiempo (evolvabilidad). Es meta-evolución. Complejidad sobre complejidad.
