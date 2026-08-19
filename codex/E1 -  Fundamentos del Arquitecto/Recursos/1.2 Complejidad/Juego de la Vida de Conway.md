# Juego de la Vida de Conway

Un sistema con reglas **súper simples** que genera comportamientos **sorprendentemente complejos** — incluyendo **crecimiento no acotado** a partir de un patrón finito.

---

#### 🎮 Cómo usarlo

**Lo que ves**

Un tablero de **140×70** celdas donde cada una está **viva (brillante)** o **muerta (negra)**. Los colores indican cuántos vecinos tiene cada célula viva en el momento actual:

|Color|Significado|
|---|---|
|🟢 **Verde**|2 vecinos — condición de supervivencia|
|🔵 **Cian**|3 vecinos — condición de supervivencia o nacimiento|
|🔴 **Rojo**|≤1 vecino — va a morir de soledad|
|🟠 **Naranja**|≥4 vecinos — va a morir de sobrepoblación|

**Patrones para probar**

|Botón|¿Qué es?|¿Qué verás?|
|---|---|---|
|**Glider**|Una "nave" de 5 celdas|Viaja en diagonal **para siempre** — movimiento emergente|
|**Blinker**|3 celdas en línea|Parpadea (período 2) — oscilación que emerge de la nada|
|**Cañón Gosper**|36 células, descubierto por Bill Gosper en noviembre de 1970 en el MIT|Emite un glider nuevo **cada 30 generaciones** — fue el primer patrón finito conocido con crecimiento no acotado, y le ganó a Gosper un premio de $50 dólares que Conway había ofrecido a quien lo lograra|
|**Pulsar**|Patrón grande|Oscila cada 3 generaciones|
|**Aleatorio**|30% celdas vivas|Caos inicial → el sistema se estabiliza solo en un mix de patrones que sobreviven|

**Tips de exploración**

- **Haz clic** en cualquier celda para activarla/desactivarla.
- **▶ Play** para que corra solo, **⏭ Paso** para ir frame por frame.
- Pon **Aleatorio**, dale play y observa cómo el sistema **se auto-organiza**: el caos inicial se estabiliza en patrones que "sobreviven".
- **Nota técnica sobre el Cañón Gosper:** esta cuadrícula es toroidal (los bordes se conectan). El cañón está pensado para un plano infinito; aquí, sus gliders eventualmente le darán la vuelta a la pantalla y chocarán con el propio cañón — en un plano verdaderamente infinito, seguiría disparando para siempre.

---

#### 🧠 La lección en vivo

Esto **es** la ciencia de la complejidad en una pantalla:

> **Reglas locales (4 líneas de reglas) → comportamiento global que nadie anticipó**

Con el **Cañón de Gosper** se ve algo profundo: el Juego de la Vida es **Turing-completo** — dada la configuración inicial adecuada, puede simular cualquier computadora. El propio cañón fue la primera prueba constructiva de que un patrón finito podía escapar del límite de crecimiento que Conway conjeturaba imposible.


```html
<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Juego de la Vida - Conway</title>
<style>
  * { box-sizing: border-box; margin: 0; padding: 0; }
  body {
    background: #0a0a0f;
    color: #e0e0e0;
    font-family: 'Courier New', monospace;
    display: flex;
    justify-content: center;
    align-items: center;
    min-height: 100vh;
    padding: 20px;
  }
  .container {
    background: #111;
    border: 1px solid #333;
    border-radius: 12px;
    padding: 25px;
    max-width: 750px;
    width: 100%;
  }
  h1 {
    color: #00ff88;
    font-size: 1.3em;
    text-align: center;
    margin-bottom: 5px;
  }
  .subtitle {
    text-align: center;
    color: #888;
    font-size: 0.85em;
    margin-bottom: 15px;
  }
  .rules {
    background: #1a1a2e;
    border-left: 3px solid #00ff88;
    padding: 10px 15px;
    font-size: 0.8em;
    margin-bottom: 15px;
    border-radius: 0 6px 6px 0;
    color: #bbb;
  }
  .rules strong { color: #00ff88; }
  canvas {
    display: block;
    margin: 0 auto;
    border: 1px solid #333;
    border-radius: 4px;
    width: 100%;
    max-width: 700px;
    aspect-ratio: 700/350;
    image-rendering: pixelated;
    background: #000;
  }
  .stats {
    display: flex;
    justify-content: space-between;
    margin: 10px 0;
    font-size: 0.85em;
    color: #aaa;
  }
  .stats span { color: #00ff88; }
  .controls {
    display: flex;
    gap: 6px;
    flex-wrap: wrap;
    justify-content: center;
    margin-top: 12px;
  }
  button {
    background: #1a1a2e;
    color: #e0e0e0;
    border: 1px solid #333;
    padding: 7px 14px;
    border-radius: 6px;
    cursor: pointer;
    font-family: 'Courier New', monospace;
    font-size: 0.8em;
    transition: all 0.2s;
  }
  button:hover { background: #2a2a4e; border-color: #00ff88; }
  button.active { background: #00ff88; color: #000; border-color: #00ff88; }
  .pattern-label {
    font-size: 0.75em;
    color: #666;
    text-align: center;
    margin-top: 10px;
  }
  .emergence-note {
    margin-top: 15px;
    padding: 12px;
    background: #0d1a0d;
    border: 1px solid #00ff8833;
    border-radius: 6px;
    font-size: 0.8em;
    color: #8c8;
    text-align: center;
  }
  .caveat-note {
    margin-top: 10px;
    padding: 10px 12px;
    background: #1a140d;
    border: 1px solid #ffaa0033;
    border-radius: 6px;
    font-size: 0.75em;
    color: #cba;
    text-align: center;
  }
</style>
</head>
<body>
<div class="container">
  <h1>⚛ JUEGO DE LA VIDA</h1>
  <div class="subtitle">John Conway, 1970 · Reglas simples → Complejidad emergente</div>

  <div class="rules">
    <strong>▸</strong> Célula viva con &lt;2 vecinos → <strong>Muere</strong> (soledad)<br>
    <strong>▸</strong> Célula viva con 2-3 vecinos → <strong>Sobrevive</strong><br>
    <strong>▸</strong> Célula viva con &gt;3 vecinos → <strong>Muere</strong> (sobrepoblación)<br>
    <strong>▸</strong> Célula muerta con 3 vecinos exactos → <strong>Nace</strong>
  </div>

  <div class="stats">
    <span id="generacion">Generación: 0</span>
    <span id="poblacion">Población: 0</span>
  </div>

  <canvas id="canvas" width="700" height="350"></canvas>

  <div class="controls">
    <button id="btnPlayPause">▶</button>
    <button id="btnStep">⏭ Paso</button>
    <button id="btnClear">✕ Limpiar</button>
    <span style="color:#333;margin:0 5px">|</span>
    <button id="patGlider">Glider</button>
    <button id="patBlinker">Blinker</button>
    <button id="patGosper">Cañón Gosper</button>
    <button id="patPulsar">Pulsar</button>
    <button id="patRandom">Aleatorio</button>
  </div>
  <div class="pattern-label">💡 Haz clic en las celdas para editarlas</div>

  <div class="emergence-note">
    🧠 <strong>Emergencia:</strong> de reglas locales simples surgen patrones globales que nadie programó —
    naves, osciladores y hasta computación universal (el Juego de la Vida es Turing-completo).
    El <strong>Cañón Gosper</strong>, descubierto por Bill Gosper en noviembre de 1970 en el MIT,
    tiene exactamente <strong>36 células</strong> y emite un glider nuevo cada <strong>30 generaciones</strong> —
    fue el primer patrón finito con crecimiento no acotado, y le ganó a Gosper un premio de $50 dólares
    que Conway ofrecía a quien lo lograra.
  </div>

  <div class="caveat-note">
    ⚠️ Nota técnica: esta cuadrícula es <strong>toroidal</strong> (los bordes se conectan entre sí).
    El Cañón Gosper está pensado para un plano infinito; aquí, sus gliders eventualmente le darán
    la vuelta a la pantalla y chocarán con el propio cañón u otros gliders — en un plano
    verdaderamente infinito, seguiría disparando para siempre.
  </div>
</div>

<script>
const canvas = document.getElementById('canvas');
const ctx = canvas.getContext('2d');

const COLS = 140;
const ROWS = 70;
const CELL_W = canvas.width / COLS;
const CELL_H = canvas.height / ROWS;

let grid = Array(ROWS).fill().map(() => Array(COLS).fill(0));
let running = false;
let gen = 0;
let interval = null;

// ====== ACTUALIZAR VISTA ======
function draw() {
  ctx.fillStyle = '#000';
  ctx.fillRect(0, 0, canvas.width, canvas.height);

  let pop = 0;
  for (let y = 0; y < ROWS; y++) {
    for (let x = 0; x < COLS; x++) {
      if (grid[y][x]) {
        pop++;
        // Color basado en vecinos (un poco de variación visual)
        let neighbors = countNeighbors(grid, x, y);
        if (neighbors <= 1) ctx.fillStyle = '#ff4466';
        else if (neighbors === 2) ctx.fillStyle = '#00ff88';
        else if (neighbors === 3) ctx.fillStyle = '#44ddff';
        else ctx.fillStyle = '#ff8844';
        ctx.fillRect(x * CELL_W, y * CELL_H, CELL_W, CELL_H);
      }
    }
  }

  document.getElementById('generacion').textContent = `Generación: ${gen}`;
  document.getElementById('poblacion').textContent = `Población: ${pop}`;
}

function countNeighbors(g, x, y) {
  let count = 0;
  for (let dy = -1; dy <= 1; dy++) {
    for (let dx = -1; dx <= 1; dx++) {
      if (dx === 0 && dy === 0) continue;
      let nx = (x + dx + COLS) % COLS;
      let ny = (y + dy + ROWS) % ROWS;
      count += g[ny][nx];
    }
  }
  return count;
}

function nextGeneration() {
  const newGrid = Array(ROWS).fill().map(() => Array(COLS).fill(0));
  for (let y = 0; y < ROWS; y++) {
    for (let x = 0; x < COLS; x++) {
      let n = countNeighbors(grid, x, y);
      if (grid[y][x]) {
        newGrid[y][x] = (n === 2 || n === 3) ? 1 : 0;
      } else {
        newGrid[y][x] = (n === 3) ? 1 : 0;
      }
    }
  }
  grid = newGrid;
  gen++;
  draw();
}

// ====== PATRONES ======
function setPattern(cells, offsetX, offsetY) {
  clearGrid();
  for (const [dx, dy] of cells) {
    let x = (offsetX + dx) % COLS;
    let y = (offsetY + dy) % ROWS;
    if (x >= 0 && y >= 0) grid[y][x] = 1;
  }
  gen = 0;
  draw();
}

// Glider
const GLIDER = [[0,1],[1,2],[2,0],[2,1],[2,2]];
// Blinker
const BLINKER = [[0,0],[1,0],[2,0]];
// Pulsar
const PULSAR = [
  [2,0],[3,0],[4,0],[8,0],[9,0],[10,0],
  [0,2],[5,2],[7,2],[12,2],
  [0,3],[5,3],[7,3],[12,3],
  [0,4],[5,4],[7,4],[12,4],
  [2,5],[3,5],[4,5],[8,5],[9,5],[10,5],
  [2,7],[3,7],[4,7],[8,7],[9,7],[10,7],
  [0,8],[5,8],[7,8],[12,8],
  [0,9],[5,9],[7,9],[12,9],
  [0,10],[5,10],[7,10],[12,10],
  [2,12],[3,12],[4,12],[8,12],[9,12],[10,12]
];
// Gosper Glider Gun (36 células, período 30 — Bill Gosper, noviembre de 1970)
const GOSPER = [
  [0,4],[0,5],[1,4],[1,5],
  [10,4],[10,5],[10,6],[11,3],[11,7],[12,2],[12,8],[13,2],[13,8],
  [14,5],[15,3],[15,7],[16,4],[16,5],[16,6],[17,5],
  [20,2],[20,3],[20,4],[21,2],[21,3],[21,4],[22,1],[22,5],[24,0],[24,1],[24,5],[24,6],
  [34,2],[34,3],[35,2],[35,3]
];

function clearGrid() {
  grid = Array(ROWS).fill().map(() => Array(COLS).fill(0));
  gen = 0;
}

function randomGrid(density = 0.3) {
  clearGrid();
  for (let y = 0; y < ROWS; y++) {
    for (let x = 0; x < COLS; x++) {
      if (Math.random() < density) grid[y][x] = 1;
    }
  }
  draw();
}

// ====== CONTROLES ======
function togglePlay() {
  if (running) {
    clearInterval(interval);
    running = false;
    document.getElementById('btnPlayPause').textContent = '▶';
  } else {
    interval = setInterval(nextGeneration, 100);
    running = true;
    document.getElementById('btnPlayPause').textContent = '⏸';
  }
}

document.getElementById('btnPlayPause').addEventListener('click', togglePlay);
document.getElementById('btnStep').addEventListener('click', nextGeneration);
document.getElementById('btnClear').addEventListener('click', () => {
  if (running) togglePlay();
  clearGrid();
  draw();
});
document.getElementById('patGlider').addEventListener('click', () => {
  if (running) togglePlay();
  setPattern(GLIDER, 10, 10);
});
document.getElementById('patBlinker').addEventListener('click', () => {
  if (running) togglePlay();
  setPattern(BLINKER, 20, 20);
});
document.getElementById('patGosper').addEventListener('click', () => {
  if (running) togglePlay();
  setPattern(GOSPER, 20, 25);
});
document.getElementById('patPulsar').addEventListener('click', () => {
  if (running) togglePlay();
  setPattern(PULSAR, 25, 20);
});
document.getElementById('patRandom').addEventListener('click', () => {
  if (running) togglePlay();
  randomGrid(0.3);
});

// ====== CLICK EN CELDAS ======
canvas.addEventListener('click', (e) => {
  const rect = canvas.getBoundingClientRect();
  const scaleX = canvas.width / rect.width;
  const scaleY = canvas.height / rect.height;
  const x = Math.floor((e.clientX - rect.left) * scaleX / CELL_W);
  const y = Math.floor((e.clientY - rect.top) * scaleY / CELL_H);
  if (x >= 0 && x < COLS && y >= 0 && y < ROWS) {
    grid[y][x] = grid[y][x] ? 0 : 1;
    draw();
  }
});

// ====== INICIALIZAR ======
setPattern(GLIDER, 10, 10);
draw();
</script>
</body>
</html>
```

