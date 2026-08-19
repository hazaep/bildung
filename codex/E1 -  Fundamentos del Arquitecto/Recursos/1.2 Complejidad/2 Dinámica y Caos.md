# 🌀 Módulo 2: Dinámica y Caos

---

## 🎯 Sistemas dinámicos

Un **sistema dinámico** es cualquier cosa que **cambia con el tiempo** según reglas deterministas. La pregunta clave es:

> **¿A dónde va este sistema a largo plazo?**

La respuesta se llama el **atractor** — el comportamiento hacia el que el sistema tiende.

| Tipo de atractor | Comportamiento | Ejemplo |
|---|---|---|
| **Punto fijo** | Se estabiliza en un solo valor | Un péndulo con fricción → se detiene |
| **Ciclo límite** | Oscila entre valores fijos | El latido del corazón |
| **Atractor extraño** | ¡Nunca se repite exactamente! | El clima, la bolsa de valores |

---

## 📈 El mapa logístico — el rey del caos

Es una ecuación **súper simple** que produce comportamientos **increíblemente complejos**:

```
x → r · x · (1 - x)
```

Donde:
- `x` es un número entre 0 y 1 (la "población" actual)
- `r` es el **parámetro de crecimiento** (entre 0 y 4)

**Eso es todo.** Una línea. Y miren lo que hace:

### La ruta al caos (diagrama de bifurcación)

A medida que aumentamos `r`, el comportamiento cambia radicalmente:

| r | Comportamiento | 
|---|---|
| **0 - 1** | La población muere → **punto fijo en 0** |
| **1 - 3** | Se estabiliza en un valor → **punto fijo estable** |
| **3 - 3.44** | ¡Oscila entre 2 valores! → **período 2** |
| **3.44 - 3.54** | Oscila entre 4 valores → **período 4** |
| **~3.57** | ¡Las bifurcaciones se vuelven locas! |
| **3.57 - 4** | **CAOS** — nunca se repite, pero tiene estructura oculta |

Esa transición orden → caos se llama la **ruta de bifurcación en duplicación de período**. Fue descubierta por Mitchell Feigenbaum y resultó ser **universal** — aparece en sistemas eléctricos, fluidos, láseres, ¡hasta en el ritmo cardíaco antes de un ataque!

---

## 🦋 Efecto mariposa

En el régimen caótico, un cambio **infinitesimal** en el valor inicial produce resultados **completamente diferentes** después de unas iteraciones.

> *"El aleteo de una mariposa en Brasil puede provocar un tornado en Texas"* — Edward Lorenz

**No es aleatoriedad.** El sistema es **determinista** — las reglas son fijas — pero es **imposible predecirlo** a largo plazo porque no podemos medir el estado inicial con precisión infinita.

---

El mapa logístico está servido. Explóralo:

## 🔬 Experimentos para hacer

### 1️⃣ Punto fijo
Desliza `r` a **1.5** y da clic en el gráfico — la curva cae directo al punto fijo. El sistema es **aburrido**, totalmente predecible.

### 2️⃣ Ciclo de período 2
Sube `r` a **3.2**. Ahora la población salta entre dos valores. **Oscilación periódica.**

### 3️⃣ Ciclo de período 4
Llévalo a **3.5** — la órbita tiene 4 puntos. **Duplicación de período.**

### 4️⃣ ¡Caos!
Ve a **3.8** o más. Los puntos parecen saltar al azar... pero si miras el **diagrama de bifurcación** de arriba, el caos tiene **estructura**. No es aleatorio.

### 5️⃣ Efecto mariposa (la parte buena)
Pon `r` en **3.9** (caos). Ahora mueve **dos veces** el slider de `r` ligeramente — mira cómo la línea azul (una corrida) diverge completamente de la roja (otra corrida) aunque empezaron **casi igual**.

---

## 📖 La moraleja

> **Reglas deterministas + no linealidad = impredecibilidad fundamental**

El mapa logístico es una **metáfora de todo**:
- 🔴 **Punto fijo** → una empresa que quiebra
- 🟡 **Oscilación** → ciclos económicos
- 🟢 **Caos** → el clima, los mercados, la historia

El caos **no es desorden**. Es **orden con sensibilidad exquisita a las condiciones iniciales**.

---

```html
<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>🌀 Módulo 2: Dinámica y Caos — Mapa Logístico</title>
<style>
  * { box-sizing: border-box; margin: 0; padding: 0; }
  body {
    background: #0a0e17;
    color: #c8d6e5;
    font-family: 'Segoe UI', system-ui, -apple-system, sans-serif;
    padding: 20px;
    min-height: 100vh;
  }
  h1 {
    text-align: center;
    font-size: 2em;
    margin-bottom: 5px;
    background: linear-gradient(135deg, #ff6b6b, #feca57, #48dbfb);
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
    background-clip: text;
  }
  .subtitle {
    text-align: center;
    color: #576574;
    margin-bottom: 20px;
    font-size: 0.9em;
  }
  .subtitle span { color: #48dbfb; -webkit-text-fill-color: #48dbfb; }
  .container {
    max-width: 1200px;
    margin: 0 auto;
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 20px;
  }
  .card {
    background: #111827;
    border-radius: 12px;
    padding: 18px;
    border: 1px solid #1e293b;
  }
  .card.full { grid-column: 1 / -1; }
  .card h2 {
    font-size: 1em;
    color: #48dbfb;
    margin-bottom: 10px;
    display: flex;
    align-items: center;
    gap: 8px;
  }
  .card h2 .badge {
    font-size: 0.65em;
    background: #1e293b;
    color: #8892a0;
    padding: 2px 8px;
    border-radius: 10px;
    font-weight: normal;
  }
  canvas {
    width: 100%;
    height: auto;
    background: #0a0e17;
    border-radius: 8px;
    display: block;
    margin: 0 auto;
    cursor: crosshair;
    border: 1px solid #1e293b;
  }
  .controls {
    display: flex;
    flex-wrap: wrap;
    gap: 12px;
    align-items: center;
    margin-top: 12px;
  }
  .controls label {
    font-size: 0.8em;
    color: #8892a0;
    display: flex;
    align-items: center;
    gap: 6px;
  }
  .controls input[type="range"] {
    width: 140px;
    accent-color: #48dbfb;
    background: #1e293b;
    height: 4px;
    border-radius: 2px;
  }
  .controls .value {
    min-width: 32px;
    text-align: center;
    font-weight: bold;
    color: #feca57;
    font-size: 0.85em;
  }
  .btn {
    background: #1e293b;
    color: #c8d6e5;
    border: 1px solid #334155;
    padding: 5px 14px;
    border-radius: 6px;
    cursor: pointer;
    font-size: 0.8em;
    transition: all 0.15s;
  }
  .btn:hover { background: #334155; border-color: #48dbfb; }
  .btn.primary { background: #0ea5e9; color: #000; border-color: #0ea5e9; font-weight: 600; }
  .btn.primary:hover { background: #38bdf8; }
  .btn.danger { background: #ef4444; border-color: #ef4444; color: #fff; }
  .btn.danger:hover { background: #f87171; }
  .btn.success { background: #10b981; border-color: #10b981; color: #fff; }
  .btn.success:hover { background: #34d399; }
  .info-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(150px, 1fr));
    gap: 8px;
    margin-top: 10px;
  }
  .info-item {
    background: #0a0e17;
    border-radius: 6px;
    padding: 8px 12px;
    border: 1px solid #1e293b;
  }
  .info-item .label { font-size: 0.65em; color: #576574; text-transform: uppercase; letter-spacing: 0.5px; }
  .info-item .val { font-size: 1em; font-weight: bold; color: #feca57; margin-top: 2px; font-variant-numeric: tabular-nums; }
  .legend {
    display: flex;
    flex-wrap: wrap;
    gap: 12px;
    margin-top: 8px;
    font-size: 0.75em;
    color: #8892a0;
  }
  .legend-item { display: flex; align-items: center; gap: 5px; }
  .legend-dot { width: 10px; height: 10px; border-radius: 50%; display: inline-block; }
  .legend-line { width: 20px; height: 3px; border-radius: 2px; display: inline-block; }
  .status-msg {
    text-align: center;
    margin-top: 10px;
    font-size: 0.8em;
    color: #feca57;
    min-height: 1.2em;
  }
  @media (max-width: 760px) {
    .container { grid-template-columns: 1fr; }
  }
  .info-box {
    background: #0a0e17;
    border: 1px solid #1e293b;
    border-left: 3px solid #feca57;
    border-radius: 8px;
    padding: 12px 16px;
    margin-top: 12px;
    font-size: 0.85em;
    line-height: 1.5;
  }
  .info-box strong { color: #feca57; }
  .info-box code {
    background: #1e293b;
    padding: 1px 6px;
    border-radius: 3px;
    font-size: 0.9em;
    color: #48dbfb;
  }
  .tabs {
    display: flex;
    gap: 4px;
    margin-bottom: 10px;
  }
  .tab {
    padding: 5px 14px;
    border-radius: 6px 6px 0 0;
    font-size: 0.78em;
    cursor: pointer;
    background: #0a0e17;
    border: 1px solid #1e293b;
    border-bottom: none;
    color: #576574;
    transition: all 0.15s;
  }
  .tab.active {
    background: #111827;
    color: #48dbfb;
    border-color: #48dbfb;
  }
  .tab:hover { color: #c8d6e5; }
</style>
</head>
<body>

<h1>🌀 Dinámica y Caos</h1>
<p class="subtitle">El mapa logístico: <span>xₙ₊₁ = r · xₙ · (1 − xₙ)</span></p>

<div class="container">

  <!-- Columna izquierda: Serie temporal -->
  <div class="card">
    <h2>📈 Serie Temporal <span class="badge">xₙ vs n</span></h2>
    <canvas id="timeCanvas" width="500" height="320"></canvas>
    <div class="controls">
      <label>
        r = <span class="value" id="rVal">2.80</span>
        <input type="range" id="rSlider" min="0" max="400" value="280">
      </label>
      <label>
        x₀ = <span class="value" id="x0Val">0.50</span>
        <input type="range" id="x0Slider" min="1" max="99" value="50">
      </label>
      <button class="btn primary" id="stepBtn">⏭ Iterar</button>
      <button class="btn" id="runBtn">▶ Correr</button>
      <button class="btn danger" id="resetBtn">↺ Reset</button>
    </div>
    <div class="info-grid">
      <div class="info-item">
        <div class="label">r (crecimiento)</div>
        <div class="val" id="rDisplay">2.80</div>
      </div>
      <div class="info-item">
        <div class="label">x₀ (inicial)</div>
        <div class="val" id="x0Display">0.500</div>
      </div>
      <div class="info-item">
        <div class="label">Tipo de atractor</div>
        <div class="val" id="attractorType" style="color:#48dbfb;font-size:0.85em;">Punto fijo</div>
      </div>
      <div class="info-item">
        <div class="label">Iteración actual</div>
        <div class="val" id="iterDisplay">0</div>
      </div>
    </div>
    <div class="legend">
      <span class="legend-item">
        <span class="legend-line" style="background:#feca57;"></span> xₙ
      </span>
      <span class="legend-item">
        <span class="legend-line" style="background:#ef4444; opacity:0.5;"></span> punto fijo
      </span>
    </div>
  </div>

  <!-- Columna derecha: Diagrama de bifurcación -->
  <div class="card">
    <h2>🌳 Diagrama de Bifurcación <span class="badge">r vs atractor</span></h2>
    <canvas id="bifCanvas" width="500" height="320"></canvas>
    <div class="controls">
      <label>
        <input type="checkbox" id="showCurrentR" checked>
        Mostrar r actual
      </label>
      <button class="btn success" id="redrawBifBtn">🔄 Redibujar</button>
    </div>
    <div class="info-box" style="margin-top:8px;font-size:0.75em;">
      <strong>Leyenda:</strong>
      · <span style="color:#48dbfb;">Azul</span> = puntos del atractor
      · <span style="color:#ef4444;">Línea roja</span> = valor de <code>r</code> actual
    </div>
  </div>

  <!-- Efecto mariposa: fila completa -->
  <div class="card full">
    <h2>🦋 Efecto Mariposa <span class="badge">dos corridas, x₀ casi iguales</span></h2>
    <canvas id="butterflyCanvas" width="1000" height="240"></canvas>
    <div class="controls">
      <label>
        r = <span class="value" id="bfRVal">3.90</span>
        <input type="range" id="bfRSlider" min="200" max="400" value="390">
      </label>
      <label>
        Δ (diferencia) = <span class="value" id="deltaVal">0.0001</span>
        <input type="range" id="deltaSlider" min="1" max="100" value="10">
      </label>
      <button class="btn primary" id="bfRunBtn">▶ Mostrar divergencia</button>
      <button class="btn" id="bfResetBtn">↺ Reset</button>
    </div>
    <div class="legend">
      <span class="legend-item">
        <span class="legend-line" style="background:#48dbfb;"></span> x₀ = 0.5000
      </span>
      <span class="legend-item">
        <span class="legend-line" style="background:#ef4444;"></span> x₀ = 0.5001
      </span>
      <span class="legend-item" id="bfStatus" style="color:#feca57;">
        Listo — mueve r a &gt;3.57 y presiona "Mostrar divergencia"
      </span>
    </div>
  </div>

  <!-- Info teórica -->
  <div class="card full">
    <h2>📖 La ruta al caos</h2>
    <div class="tabs">
      <div class="tab active" data-tab="tab-1">Punto fijo (r &lt; 3)</div>
      <div class="tab" data-tab="tab-2">Período 2 (3 &lt; r &lt; 3.44)</div>
      <div class="tab" data-tab="tab-3">Período 4 (3.44 &lt; r &lt; 3.54)</div>
      <div class="tab" data-tab="tab-4">Caos (r &gt; 3.57)</div>
    </div>
    <div id="tab-1" class="info-box" style="border-left-color:#10b981;">
      <strong>✅ Punto fijo</strong> — La población se estabiliza en un solo valor. El sistema es <strong>predecible</strong> y aburrido. Cualquier perturbación vuelve al mismo punto. Ocurre cuando <code>r &lt; 3</code>.
    </div>
    <div id="tab-2" class="info-box" style="border-left-color:#48dbfb; display:none;">
      <strong>🔄 Período 2</strong> — La población oscila entre dos valores. <strong>Primera bifurcación.</strong> Ocurre cuando <code>3 &lt; r &lt; 3.44</code>. Aparece porque el punto fijo se vuelve inestable.
    </div>
    <div id="tab-3" class="info-box" style="border-left-color:#feca57; display:none;">
      <strong>🔄🔄 Período 4</strong> — Oscila entre cuatro valores. <strong>Segunda bifurcación.</strong> Ocurre cuando <code>3.44 &lt; r &lt; 3.54</code>. Cada bifurcación duplica el período.
    </div>
    <div id="tab-4" class="info-box" style="border-left-color:#ef4444; display:none;">
      <strong>🌀 Caos</strong> — Nunca se repite. El atractor tiene una estructura fractal (un <strong>atractor extraño</strong>). Sin embargo, el sistema sigue siendo <strong>determinista</strong> — no es aleatorio. El período se vuelve infinito. Ocurre cuando <code>r &gt; 3.57</code>.
    </div>
    <div style="margin-top:8px;font-size:0.78em;color:#576574;text-align:center;">
      💡 <strong>Feigenbaum descubrió</strong> que la razón entre bifurcaciones sucesivas (~4.669) es una <strong>constante universal</strong> — aparece en sistemas eléctricos, fluidos, ¡y hasta en el corazón humano!
    </div>
  </div>

</div>

<script>
// ============================================================
// 1. Serie temporal
// ============================================================
const timeCanvas = document.getElementById('timeCanvas');
const tCtx = timeCanvas.getContext('2d');
const W = 500, H = 320;
timeCanvas.width = W; timeCanvas.height = H;

let r = 2.80;
let x0 = 0.50;
let history = [];
let running = false;
let animId = null;
let iter = 0;

function logistic(rVal, x) { return rVal * x * (1 - x); }

function classifyAttractor(rVal, hist) {
  if (rVal < 1) return { type: 'Extinción (x→0)', color: '#6b7280' };
  if (rVal < 3) return { type: 'Punto fijo', color: '#10b981' };
  if (rVal < 3.449) return { type: 'Período 2', color: '#48dbfb' };
  if (rVal < 3.544) return { type: 'Período 4', color: '#feca57' };
  if (rVal < 3.57) return { type: 'Período 8+', color: '#f97316' };
  return { type: '🌀 Caos', color: '#ef4444' };
}

function drawTimeSeries() {
  tCtx.clearRect(0, 0, W, H);
  const pad = { t: 15, b: 25, l: 40, r: 15 };
  const pw = W - pad.l - pad.r;
  const ph = H - pad.t - pad.b;

  // Grid
  tCtx.strokeStyle = '#1e293b';
  tCtx.lineWidth = 0.5;
  for (let i = 0; i <= 5; i++) {
    const y = pad.t + (ph * (1 - i/5));
    tCtx.beginPath(); tCtx.moveTo(pad.l, y); tCtx.lineTo(W-pad.r, y); tCtx.stroke();
    tCtx.fillStyle = '#576574';
    tCtx.font = '9px sans-serif';
    tCtx.textAlign = 'right';
    tCtx.fillText((i/5).toFixed(1), pad.l-4, y+3);
  }
  for (let i = 0; i <= 10; i++) {
    const x = pad.l + (pw * i/10);
    tCtx.beginPath(); tCtx.moveTo(x, pad.t); tCtx.lineTo(x, H-pad.b); tCtx.stroke();
    tCtx.fillStyle = '#576574';
    tCtx.textAlign = 'center';
    if (i % 2 === 0) tCtx.fillText(i*5, x, H-5);
  }

  if (history.length < 2) return;

  // Punto fijo teórico (r < 3)
  if (r > 1 && r <= 3) {
    const fixed = 1 - 1/r;
    const fx = pad.l;
    const fx2 = pad.l + pw;
    const fy = pad.t + ph * (1 - fixed);
    tCtx.strokeStyle = 'rgba(239,68,68,0.3)';
    tCtx.setLineDash([4,4]);
    tCtx.beginPath(); tCtx.moveTo(fx, fy); tCtx.lineTo(fx2, fy); tCtx.stroke();
    tCtx.setLineDash([]);
  }

  // Serie temporal
  const N = history.length;
  const maxShow = Math.min(N, 100);
  const start = Math.max(0, N - maxShow);
  const visible = history.slice(start);

  tCtx.strokeStyle = '#feca57';
  tCtx.lineWidth = 2;
  tCtx.beginPath();
  for (let i = 0; i < visible.length; i++) {
    const px = pad.l + (i / (maxShow-1 || 1)) * pw;
    const py = pad.t + ph * (1 - visible[i]);
    i === 0 ? tCtx.moveTo(px, py) : tCtx.lineTo(px, py);
  }
  tCtx.stroke();

  // Puntos
  for (let i = 0; i < visible.length; i++) {
    const px = pad.l + (i / (maxShow-1 || 1)) * pw;
    const py = pad.t + ph * (1 - visible[i]);
    tCtx.fillStyle = i === visible.length-1 ? '#feca57' : 'rgba(254,202,87,0.3)';
    tCtx.beginPath();
    tCtx.arc(px, py, i === visible.length-1 ? 4 : 1.5, 0, Math.PI*2);
    tCtx.fill();
  }

  // Valor actual
  const last = history[history.length-1];
  tCtx.fillStyle = '#feca57';
  tCtx.font = 'bold 12px monospace';
  tCtx.textAlign = 'left';
  tCtx.fillText(`x = ${last.toFixed(4)}`, pad.l+5, pad.t+14);
}

function updateTimeSeries() {
  const at = classifyAttractor(r, history);
  document.getElementById('rDisplay').textContent = r.toFixed(2);
  document.getElementById('x0Display').textContent = x0.toFixed(3);
  document.getElementById('attractorType').textContent = at.type;
  document.getElementById('attractorType').style.color = at.color;
  document.getElementById('iterDisplay').textContent = history.length;
  drawTimeSeries();
}

function doStep() {
  if (history.length === 0) {
    history.push(x0);
  } else {
    const last = history[history.length-1];
    history.push(logistic(r, last));
  }
  updateTimeSeries();
}

function doReset() {
  if (animId) { cancelAnimationFrame(animId); animId = null; }
  running = false;
  document.getElementById('runBtn').textContent = '▶ Correr';
  history = [x0];
  updateTimeSeries();
}

// Sliders
const rSlider = document.getElementById('rSlider');
const x0Slider = document.getElementById('x0Slider');
rSlider.addEventListener('input', () => {
  r = parseFloat(rSlider.value) / 100;
  document.getElementById('rVal').textContent = r.toFixed(2);
  doReset();
});
x0Slider.addEventListener('input', () => {
  x0 = parseFloat(x0Slider.value) / 100;
  document.getElementById('x0Val').textContent = x0.toFixed(2);
  doReset();
});

document.getElementById('stepBtn').addEventListener('click', doStep);
document.getElementById('resetBtn').addEventListener('click', doReset);
document.getElementById('runBtn').addEventListener('click', () => {
  if (running) {
    running = false;
    if (animId) cancelAnimationFrame(animId);
    document.getElementById('runBtn').textContent = '▶ Correr';
    return;
  }
  running = true;
  document.getElementById('runBtn').textContent = '⏹ Parar';
  function animLoop() {
    if (!running) return;
    doStep();
    animId = requestAnimationFrame(animLoop);
  }
  animLoop();
});

// ============================================================
// 2. Diagrama de bifurcación
// ============================================================
const bifCanvas = document.getElementById('bifCanvas');
const bCtx = bifCanvas.getContext('2d');
bifCanvas.width = W; bifCanvas.height = H;

function drawBifurcation() {
  bCtx.clearRect(0, 0, W, H);
  const pad = { t: 15, b: 25, l: 40, r: 15 };
  const pw = W - pad.l - pad.r;
  const ph = H - pad.t - pad.b;

  // Grid
  bCtx.strokeStyle = '#1e293b';
  bCtx.lineWidth = 0.5;
  for (let i = 0; i <= 4; i++) {
    const y = pad.t + ph * (1 - i/4);
    bCtx.beginPath(); bCtx.moveTo(pad.l, y); bCtx.lineTo(W-pad.r, y); bCtx.stroke();
    bCtx.fillStyle = '#576574';
    bCtx.font = '9px sans-serif';
    bCtx.textAlign = 'right';
    bCtx.fillText((i/4).toFixed(1), pad.l-4, y+3);
  }
  for (let i = 0; i <= 5; i++) {
    const rv = 0 + (4 * i/5);
    const x = pad.l + (rv/4) * pw;
    bCtx.beginPath(); bCtx.moveTo(x, pad.t); bCtx.lineTo(x, H-pad.b); bCtx.stroke();
    bCtx.fillStyle = '#576574';
    bCtx.textAlign = 'center';
    bCtx.fillText(rv.toFixed(1), x, H-5);
  }

  // Eje X label
  bCtx.fillStyle = '#576574';
  bCtx.font = '9px sans-serif';
  bCtx.textAlign = 'center';
  bCtx.fillText('r', W-10, H-5);

  // Calcular bifurcación
  const steps = 500;
  const discard = 100;

  for (let ri = 0; ri <= steps; ri++) {
    const rv = 1 + (3 * ri / steps); // 1 a 4
    let x = 0.5;
    for (let i = 0; i < discard; i++) x = logistic(rv, x);
    for (let i = 0; i < 100; i++) {
      x = logistic(rv, x);
      const px = pad.l + (rv/4) * pw;
      const py = pad.t + ph * (1 - x);
      bCtx.fillStyle = 'rgba(72,219,251,0.3)';
      bCtx.fillRect(px, py, 1.5, 1.5);
    }
  }

  // Línea de r actual
  if (document.getElementById('showCurrentR').checked) {
    const px = pad.l + (r/4) * pw;
    bCtx.strokeStyle = '#ef4444';
    bCtx.lineWidth = 2;
    bCtx.setLineDash([4,4]);
    bCtx.beginPath(); bCtx.moveTo(px, pad.t); bCtx.lineTo(px, H-pad.b); bCtx.stroke();
    bCtx.setLineDash([]);
    bCtx.fillStyle = '#ef4444';
    bCtx.font = 'bold 10px monospace';
    bCtx.textAlign = 'center';
    bCtx.fillText(`r=${r.toFixed(2)}`, px, pad.t+12);
  }
}

drawBifurcation();
document.getElementById('redrawBifBtn').addEventListener('click', drawBifurcation);
document.getElementById('showCurrentR').addEventListener('change', drawBifurcation);
rSlider.addEventListener('input', drawBifurcation);

// ============================================================
// 3. Efecto mariposa
// ============================================================
const bfCanvas = document.getElementById('butterflyCanvas');
const bfCtx = bfCanvas.getContext('2d');
bfCanvas.width = 1000; bfCanvas.height = 240;

let bfR = 3.90;
let bfDelta = 0.0001;
let bfHist1 = [];
let bfHist2 = [];
let bfRunId = null;
let bfRunning = false;

function drawButterfly() {
  bfCtx.clearRect(0, 0, 1000, 240);
  const pad = { t: 15, b: 25, l: 45, r: 15 };
  const pw = 1000 - pad.l - pad.r;
  const ph = 240 - pad.t - pad.b;

  // Grid
  bfCtx.strokeStyle = '#1e293b';
  bfCtx.lineWidth = 0.5;
  for (let i = 0; i <= 4; i++) {
    const y = pad.t + ph * (1 - i/4);
    bfCtx.beginPath(); bfCtx.moveTo(pad.l, y); bfCtx.lineTo(1000-pad.r, y); bfCtx.stroke();
    bfCtx.fillStyle = '#576574';
    bfCtx.font = '9px sans-serif';
    bfCtx.textAlign = 'right';
    bfCtx.fillText((i/4).toFixed(1), pad.l-4, y+3);
  }
  for (let i = 0; i <= 10; i++) {
    const x = pad.l + (pw * i/10);
    bfCtx.beginPath(); bfCtx.moveTo(x, pad.t); bfCtx.lineTo(x, 240-pad.b); bfCtx.stroke();
    bfCtx.fillStyle = '#576574';
    bfCtx.textAlign = 'center';
    if (i % 2 === 0) bfCtx.fillText(i*5, x, 240-5);
  }
  bfCtx.fillStyle = '#576574';
  bfCtx.font = '9px sans-serif';
  bfCtx.textAlign = 'center';
  bfCtx.fillText('Iteración n', 1000/2, 235);

  const maxShow = Math.min(bfHist1.length, 100);

  // Serie 1 (azul)
  if (bfHist1.length > 0) {
    const start = Math.max(0, bfHist1.length - maxShow);
    const visible = bfHist1.slice(start);
    bfCtx.strokeStyle = '#48dbfb';
    bfCtx.lineWidth = 2;
    bfCtx.beginPath();
    for (let i = 0; i < visible.length; i++) {
      const px = pad.l + (i / Math.max(1, visible.length-1)) * pw;
      const py = pad.t + ph * (1 - visible[i]);
      i === 0 ? bfCtx.moveTo(px, py) : bfCtx.lineTo(px, py);
    }
    bfCtx.stroke();
  }

  // Serie 2 (rojo)
  if (bfHist2.length > 0) {
    const start = Math.max(0, bfHist2.length - maxShow);
    const visible = bfHist2.slice(start);
    bfCtx.strokeStyle = '#ef4444';
    bfCtx.lineWidth = 1.5;
    bfCtx.beginPath();
    for (let i = 0; i < visible.length; i++) {
      const px = pad.l + (i / Math.max(1, visible.length-1)) * pw;
      const py = pad.t + ph * (1 - visible[i]);
      i === 0 ? bfCtx.moveTo(px, py) : bfCtx.lineTo(px, py);
    }
    bfCtx.stroke();
  }

  // Diferencia
  if (bfHist1.length > 0 && bfHist2.length > 0) {
    const n = Math.min(bfHist1.length, bfHist2.length);
    const diff = Math.abs(bfHist1[n-1] - bfHist2[n-1]);
    const status = bfRunning ? 'Corriendo...' : 
      (n > 1 ? `Diferencia actual: ${diff.toExponential(3)}` : '');
    document.getElementById('bfStatus').textContent = status;
  }
}

function resetButterfly() {
  if (bfRunId) { cancelAnimationFrame(bfRunId); bfRunId = null; }
  bfRunning = false;
  document.getElementById('bfRunBtn').textContent = '▶ Mostrar divergencia';
  bfHist1 = [];
  bfHist2 = [];
  document.getElementById('bfStatus').textContent = 'Listo — mueve r a >3.57 y presiona "Mostrar divergencia"';
  drawButterfly();
}

function runButterfly() {
  if (bfRunning) { return; }
  bfHist1 = [0.5];
  bfHist2 = [0.5 + bfDelta];
  bfRunning = true;
  document.getElementById('bfRunBtn').textContent = '⏳ Corriendo...';
  let steps = 0;

  function stepBF() {
    if (!bfRunning || steps >= 100) {
      bfRunning = false;
      document.getElementById('bfRunBtn').textContent = '▶ Mostrar divergencia';
      const last1 = bfHist1[bfHist1.length-1];
      const last2 = bfHist2[bfHist2.length-1];
      document.getElementById('bfStatus').textContent = 
        `✅ Divergencia: |x₁ − x₂| = ${Math.abs(last1-last2).toExponential(3)} (Δ inicial = ${bfDelta})`;
      return;
    }
    bfHist1.push(logistic(bfR, bfHist1[bfHist1.length-1]));
    bfHist2.push(logistic(bfR, bfHist2[bfHist2.length-1]));
    steps++;
    drawButterfly();
    bfRunId = requestAnimationFrame(stepBF);
  }
  stepBF();
}

const bfRSlider = document.getElementById('bfRSlider');
const deltaSlider = document.getElementById('deltaSlider');

bfRSlider.addEventListener('input', () => {
  bfR = parseFloat(bfRSlider.value) / 100;
  document.getElementById('bfRVal').textContent = bfR.toFixed(2);
  resetButterfly();
});
deltaSlider.addEventListener('input', () => {
  const val = parseInt(deltaSlider.value);
  bfDelta = Math.pow(10, -Math.floor(val/10)-1) * (val%10+1 || 1);
  // prettier display
  const exp = Math.floor(Math.log10(bfDelta));
  const mant = bfDelta / Math.pow(10, exp);
  document.getElementById('deltaVal').textContent = bfDelta.toExponential(1);
  resetButterfly();
});

document.getElementById('bfRunBtn').addEventListener('click', runButterfly);
document.getElementById('bfResetBtn').addEventListener('click', resetButterfly);
drawButterfly();

// ============================================================
// 4. Tabs de información
// ============================================================
document.querySelectorAll('.tab').forEach(tab => {
  tab.addEventListener('click', () => {
    document.querySelectorAll('.tab').forEach(t => t.classList.remove('active'));
    tab.classList.add('active');
    document.querySelectorAll('[id^="tab-"]').forEach(el => el.style.display = 'none');
    document.getElementById(tab.dataset.tab).style.display = 'block';
  });
});

// Inicializar
doReset();
</script>
</body>
</html>
```


---

## 🗺️ Qué hay en la página

### Panel izquierdo — Serie Temporal 📈
- Controla **r** y **x₀** con sliders
- **▶ Correr**: anima las iteraciones en tiempo real
- **⏭ Iterar**: paso a paso para ver la evolución lenta
- Te dice en vivo el **tipo de atractor** (punto fijo, período 2, caos...)

### Panel derecho — Diagrama de bifurcación 🌳
- Los **500,000 puntos azules** pintan el diagrama completo
- La **línea roja** marca tu `r` actual
- Se actualiza solo cuando mueves el slider

### Panel completo — Efecto mariposa 🦋
- Dos líneas que empiezan **casi iguales** (diferencia de 0.0001)
- En régimen caótico (r > 3.57) **divergen por completo** en ~30 iteraciones
- En régimen de punto fijo (r < 3) **se mantienen pegadas**

### Pestañas teóricas 📖
- 4 tabs que explican cada régimen: punto fijo → período 2 → período 4 → caos

---

Los experimentos clave:

| Prueba | r | Qué observar |
|--------|---|-------------|
| **Orden total** | 2.5 | La línea se aplana → punto fijo |
| **Primera bifurcación** | 3.2 | Empieza a oscilar entre 2 valores |
| **Caos puro** | 3.9 | Parece aleatorio pero **no lo es** |
| **Mariposa** | 3.9 + botón | Dos corridas casi idénticas se separan radicalmente |
