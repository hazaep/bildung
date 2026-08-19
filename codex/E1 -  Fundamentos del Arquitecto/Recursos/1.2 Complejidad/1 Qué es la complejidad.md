# Módulo 1: ¿Qué es la complejidad?

_Basado en la Unidad 1 ("What is Complexity?") del curso Introduction to Complexity del Santa Fe Institute, impartido por Melanie Mitchell._

## La pregunta del millón

No hay una sola definición universal de "complejidad". De hecho, los propios científicos de la complejidad siguen debatiendo cómo definirla con precisión. Pero hay consenso en lo que **no** es:

> Complejidad ≠ Complicado

Un motor de avión es _complicado_. Un hormiguero es _complejo_.

|Complicado|Complejo|
|---|---|
|Muchas partes, pero predecible|Las interacciones generan sorpresas|
|Sabes exactamente qué va a pasar|El comportamiento emerge|
|Se puede desarmar y entender pieza por pieza|El todo es más que la suma de las partes|

## Definiciones que usa el SFI

Melanie Mitchell —profesora de Ciencias de la Computación en Portland State University, miembro del consejo científico del Santa Fe Institute y autora de _Complexity: A Guided Tour_ (Oxford University Press, 2009)— propone varias perspectivas complementarias, no excluyentes entre sí:

### 1. Complejidad como tamaño de la descripción

Algo es complejo si se necesitan muchas palabras o mucha información para describirlo con precisión. Un vaso de agua se describe con pocas reglas; el cerebro humano requiere una cantidad de información mucho mayor.

### 2. Complejidad como contenido de información

Relacionado con la entropía de Shannon y la complejidad de Kolmogorov: qué tan compresible es un sistema.

- Un patrón perfectamente regular (por ejemplo, una secuencia que se repite de forma idéntica) es simple: se describe con una regla corta.
- El ruido puramente aleatorio también es "simple" en este sentido específico: no tiene estructura que explotar, así que tampoco requiere más que "es ruido" para describirlo.
- Lo genuinamente complejo está **en medio**: tiene suficiente estructura como para no ser puro ruido, pero no tanta regularidad como para reducirse a una regla trivial.

Un ejemplo concreto y verificable de esta frontera son los **autómatas celulares elementales** estudiados por Stephen Wolfram, que clasificó su comportamiento en cuatro clases:

- **Clase 1 y 2 (orden):** el patrón se estabiliza en un estado fijo o se vuelve periódico y predecible. La Regla 90, por ejemplo, genera el triángulo de Sierpinski — una estructura fractal, pero completamente determinista y compresible a una regla simple.
- **Clase 3 (caos):** el patrón se vuelve aparentemente aleatorio, sin estructura reconocible. La Regla 30 es el ejemplo clásico — de hecho, Mathematica la usa como uno de sus generadores de números pseudoaleatorios, precisamente porque su comportamiento pasa las pruebas estadísticas de aleatoriedad.
- **Clase 4 (complejidad, "el borde del caos"):** ni completamente ordenado ni completamente caótico. Aparecen estructuras localizadas que interactúan entre sí de forma impredecible pero no puramente aleatoria. La Regla 110 es el ejemplo de referencia — Matthew Cook demostró en 1994 (publicado en 2004) que es **Turing-completa**: con la configuración inicial correcta, puede simular cualquier computadora.

Esta clasificación de Wolfram es, en esencia, una demostración computacional directa de la idea "la complejidad está en la frontera entre el orden y el caos": tres reglas de producción igual de simples (una línea de código cada una) producen comportamientos radicalmente distintos según en qué clase caigan.

### 3. Complejidad como capacidad de computación

Qué tan "interesante" es el comportamiento de un sistema: si puede procesar información, adaptarse y evolucionar. El Juego de la Vida de Conway (que veremos en el Módulo 6, dedicado a autómatas celulares) es el ejemplo por excelencia de esta perspectiva — reglas triviales producen un sistema capaz de computación universal.

## Propiedades de los sistemas complejos

|Propiedad|Qué significa|Ejemplo|
|---|---|---|
|Componentes múltiples|Muchas partes interactuando|Neuronas, hormigas, personas|
|Interacciones no lineales|Efectos pequeños pueden tener grandes consecuencias|El "efecto mariposa" de Edward Lorenz (1972): el aleteo de una mariposa en Brasil, en principio, podría alterar la formación de un tornado en Texas semanas después|
|Emergencia|El comportamiento global no está en las partes individuales|La consciencia, el tráfico, un mercado|
|Auto-organización|El orden surge sin un controlador central|Bandadas de aves, formaciones de estorninos|
|Adaptación/evolución|El sistema cambia en respuesta al entorno|Sistema inmune, economía, Internet|
|Sin equilibrio|Nunca se quedan quietos|Ecosistemas, clima, ciudades|

## Ejemplos clásicos de sistemas complejos

|Sistema|Componentes|Comportamiento emergente|
|---|---|---|
|Colonia de hormigas|Hormigas individuales con reglas simples|Caminos óptimos, división del trabajo|
|El cerebro|~86 mil millones de neuronas|Consciencia, pensamiento, memoria|
|Sistema inmunológico|Células, anticuerpos, proteínas|Defensa contra patógenos|
|Internet|Computadoras, routers, servidores|La web, buscadores, redes sociales|
|La economía|Personas comprando y vendiendo|Precios, mercados, ciclos económicos|
|Tráfico urbano|Autos, semáforos, calles|Embotellamientos (emergencia no deseada)|
|Una ciudad|Personas, edificios, infraestructura|Cultura, crimen, innovación|

## La idea clave

La ciencia de la complejidad busca entender cómo reglas simples a nivel local generan comportamientos complejos a nivel global — "cómo surge la complejidad a partir de la simplicidad", en palabras de Melanie Mitchell.

## Fuentes

- Melanie Mitchell, _Complexity: A Guided Tour_ (Oxford University Press, 2009).
- Santa Fe Institute / Complexity Explorer, curso _Introduction to Complexity_, Unidad 1: "What is Complexity?" (complexityexplorer.org).
- Stephen Wolfram, _A New Kind of Science_ (Wolfram Media, 2002) — clasificación de las cuatro clases de autómatas celulares elementales.
- Matthew Cook, "Universality in Elementary Cellular Automata", _Complex Systems_ 15 (2004) — demostración de que la Regla 110 es Turing-completa.
- Edward Lorenz, "Predictability: Does the Flap of a Butterfly's Wings in Brazil Set Off a Tornado in Texas?", charla de 1972 ante la American Association for the Advancement of Science.
- Suzana Herculano-Houzel — investigación sobre el conteo de neuronas en el cerebro humano (~86 mil millones).

---

## Material ilustrativo

- **Complicado vs. complejo**: engranajes girando de forma predecible (SVG animado simple) al lado de un "enjambre" de puntos con movimiento local aleatorio que ilustra el comportamiento tipo hormiguero — ambos son animaciones pasivas, sin controles, para no romper el criterio de "interacción mínima".
- **La frontera orden-caos-complejidad**: tres botones (uno por regla) que dibujan la evolución real de los autómatas celulares elementales de Wolfram — Regla 90 (orden, triángulo de Sierpinski), Regla 110 (complejidad, Turing-completa), Regla 30 (caos, usada por Mathematica como generador pseudoaleatorio).


```html
<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Módulo 1 - ¿Qué es la complejidad?</title>
<style>
  * { box-sizing: border-box; margin: 0; padding: 0; }
  body {
    background: #0a0a0f;
    color: #e0e0e0;
    font-family: 'Segoe UI', system-ui, sans-serif;
    display: flex;
    justify-content: center;
    padding: 24px 16px;
    min-height: 100vh;
  }
  .container { max-width: 820px; width: 100%; }
  h1 {
    text-align: center;
    font-size: 1.5em;
    background: linear-gradient(135deg, #8a7cff, #4dd0e1);
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
    margin-bottom: 4px;
  }
  .sub { text-align: center; color: #888; font-size: 0.85em; margin-bottom: 24px; }
  .card {
    background: #14141f;
    border: 1px solid #2a2a3a;
    border-radius: 14px;
    padding: 22px;
    margin-bottom: 22px;
  }
  .card h2 { font-size: 1.05em; color: #8a7cff; margin-bottom: 14px; }

  /* ---- Complicado vs Complejo ---- */
  .compare { display: flex; gap: 16px; flex-wrap: wrap; }
  .panel {
    flex: 1;
    min-width: 220px;
    background: #0f0f1a;
    border-radius: 10px;
    padding: 16px;
    text-align: center;
  }
  .panel h3 { font-size: 0.95em; margin-bottom: 10px; }
  .panel.complicado h3 { color: #4dd0e1; }
  .panel.complejo h3 { color: #ff8a65; }
  .gears { width: 100%; height: 110px; }
  .swarm { width: 100%; height: 110px; }
  .panel p { font-size: 0.78em; color: #999; margin-top: 10px; line-height: 1.5; }

  /* ---- Autómatas celulares ---- */
  .ca-controls { display: flex; gap: 8px; flex-wrap: wrap; justify-content: center; margin-bottom: 14px; }
  button {
    background: #1a1a2e;
    color: #e0e0e0;
    border: 1px solid #333;
    padding: 8px 16px;
    border-radius: 8px;
    cursor: pointer;
    font-size: 0.82em;
    transition: all 0.2s;
  }
  button:hover { border-color: #8a7cff; }
  button.active { background: #8a7cff; color: #0a0a0f; border-color: #8a7cff; font-weight: 600; }
  canvas#caCanvas {
    display: block;
    margin: 0 auto;
    width: 100%;
    max-width: 700px;
    border-radius: 6px;
    background: #000;
    image-rendering: pixelated;
  }
  .ca-label {
    text-align: center;
    font-size: 0.8em;
    color: #aaa;
    margin-top: 10px;
    line-height: 1.5;
  }
  .ca-label strong { color: #8a7cff; }
  .legend { display: flex; gap: 16px; justify-content: center; margin-top: 10px; font-size: 0.75em; color: #888; flex-wrap: wrap; }
</style>
</head>
<body>
<div class="container">
  <h1>¿Qué es la complejidad?</h1>
  <div class="sub">Módulo 1 · Material ilustrativo — sin simulación continua, solo contraste visual</div>

  <div class="card">
    <h2>Complicado ≠ Complejo</h2>
    <div class="compare">
      <div class="panel complicado">
        <h3>⚙ Complicado (motor)</h3>
        <svg class="gears" viewBox="0 0 200 110">
          <g id="gearA" style="transform-origin:60px 55px">
            <circle cx="60" cy="55" r="30" fill="none" stroke="#4dd0e1" stroke-width="6" stroke-dasharray="8 6"/>
            <circle cx="60" cy="55" r="6" fill="#4dd0e1"/>
          </g>
          <g id="gearB" style="transform-origin:140px 55px">
            <circle cx="140" cy="55" r="22" fill="none" stroke="#4dd0e1" stroke-width="6" stroke-dasharray="6 5"/>
            <circle cx="140" cy="55" r="5" fill="#4dd0e1"/>
          </g>
        </svg>
        <p>Muchas piezas, pero el movimiento es <strong>totalmente predecible</strong>: conocido el engranaje, se conoce el resultado para siempre.</p>
      </div>
      <div class="panel complejo">
        <h3>🐜 Complejo (hormiguero)</h3>
        <svg class="swarm" viewBox="0 0 200 110">
          <g id="ants"></g>
        </svg>
        <p>Reglas locales simples (seguir feromona) generan <strong>caminos y patrones globales</strong> que nadie diseñó de antemano.</p>
      </div>
    </div>
  </div>

  <div class="card">
    <h2>La complejidad está en la frontera entre el orden y el caos</h2>
    <p style="font-size:0.82em;color:#999;margin-bottom:14px;line-height:1.6">
      Tres autómatas celulares elementales (Stephen Wolfram), cada uno definido por una única regla de una línea,
      generan comportamientos radicalmente distintos. Cada fila de la imagen es una generación; el patrón crece hacia abajo.
    </p>
    <div class="ca-controls">
      <button id="btnOrder" class="active">Regla 90 — Orden</button>
      <button id="btnComplex">Regla 110 — Complejidad</button>
      <button id="btnChaos">Regla 30 — Caos</button>
    </div>
    <canvas id="caCanvas" width="700" height="260"></canvas>
    <div class="ca-label" id="caLabel"></div>
  </div>
</div>

<script>
/* ---- Animación simple de engranajes (Complicado) ---- */
let angleA = 0, angleB = 0;
function spinGears() {
  angleA = (angleA + 1.2) % 360;
  angleB = (angleB - 1.8) % 360;
  document.getElementById('gearA').style.transform = `rotate(${angleA}deg)`;
  document.getElementById('gearB').style.transform = `rotate(${angleB}deg)`;
  requestAnimationFrame(spinGears);
}
spinGears();

/* ---- Enjambre simple de "hormigas" (Complejo) ---- */
const NUM_ANTS = 14;
const antsGroup = document.getElementById('ants');
const ants = [];
for (let i = 0; i < NUM_ANTS; i++) {
  const dot = document.createElementNS('http://www.w3.org/2000/svg', 'circle');
  dot.setAttribute('r', 2.4);
  dot.setAttribute('fill', '#ff8a65');
  antsGroup.appendChild(dot);
  ants.push({
    el: dot,
    x: Math.random() * 200,
    y: Math.random() * 110,
    a: Math.random() * Math.PI * 2
  });
}
function moveAnts() {
  for (const ant of ants) {
    ant.a += (Math.random() - 0.5) * 0.6;
    ant.x += Math.cos(ant.a) * 0.9;
    ant.y += Math.sin(ant.a) * 0.9;
    if (ant.x < 0) ant.x = 200; if (ant.x > 200) ant.x = 0;
    if (ant.y < 0) ant.y = 110; if (ant.y > 110) ant.y = 0;
    ant.el.setAttribute('cx', ant.x);
    ant.el.setAttribute('cy', ant.y);
  }
  requestAnimationFrame(moveAnts);
}
moveAnts();

/* ---- Autómatas celulares elementales ---- */
const caCanvas = document.getElementById('caCanvas');
const caCtx = caCanvas.getContext('2d');
const CA_COLS = 140;
const CA_ROWS = 130;
const CA_CELL = caCanvas.width / CA_COLS;

const LABELS = {
  90:  "<strong>Regla 90 (orden):</strong> genera el triángulo de Sierpinski — una estructura fractal, pero completamente determinista y predecible a partir de una regla simple.",
  110: "<strong>Regla 110 (complejidad):</strong> ni fijo ni aleatorio — aparecen estructuras localizadas que interactúan. Matthew Cook demostró en 2004 que esta regla es Turing-completa.",
  30:  "<strong>Regla 30 (caos):</strong> comportamiento aparentemente aleatorio a partir de una regla determinista — Mathematica la usa como generador de números pseudoaleatorios."
};

function ruleToBits(rule) {
  const bits = [];
  for (let i = 7; i >= 0; i--) bits.push((rule >> i) & 1);
  return bits; // bits[0] = patrón 111, bits[7] = patrón 000
}

function drawCA(rule) {
  const bits = ruleToBits(rule);
  let row = new Array(CA_COLS).fill(0);
  row[Math.floor(CA_COLS / 2)] = 1; // una sola célula activa al centro

  caCtx.fillStyle = '#000';
  caCtx.fillRect(0, 0, caCanvas.width, caCanvas.height);
  caCtx.fillStyle = '#8a7cff';

  for (let r = 0; r < CA_ROWS; r++) {
    for (let c = 0; c < CA_COLS; c++) {
      if (row[c]) caCtx.fillRect(c * CA_CELL, r * CA_CELL, CA_CELL, CA_CELL);
    }
    const next = new Array(CA_COLS).fill(0);
    for (let c = 0; c < CA_COLS; c++) {
      const l = row[(c - 1 + CA_COLS) % CA_COLS];
      const m = row[c];
      const rr = row[(c + 1) % CA_COLS];
      const pattern = (l << 2) | (m << 1) | rr; // 0..7
      next[c] = bits[7 - pattern];
    }
    row = next;
  }
  document.getElementById('caLabel').innerHTML = LABELS[rule];
}

document.getElementById('btnOrder').addEventListener('click', function() {
  setActive(this); drawCA(90);
});
document.getElementById('btnComplex').addEventListener('click', function() {
  setActive(this); drawCA(110);
});
document.getElementById('btnChaos').addEventListener('click', function() {
  setActive(this); drawCA(30);
});
function setActive(btn) {
  document.querySelectorAll('.ca-controls button').forEach(b => b.classList.remove('active'));
  btn.classList.add('active');
}

drawCA(90);
</script>
</body>
</html>
```

