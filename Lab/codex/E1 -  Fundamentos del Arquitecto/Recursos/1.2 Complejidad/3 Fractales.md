# 🌀 Módulo 3: Fractales

---

## 🔗 La conexión con el Módulo 2

¿Recuerdas el **diagrama de bifurcación** del mapa logístico? Ese diagrama — con sus ramas infinitas que se duplican una y otra vez — **es un fractal**. Tiene **autosimilitud**: si haces zoom en cualquier bifurcación, ves el mismo patrón que arriba.

> **El caos tiene una geometría. Esa geometría son los fractales.**

---

## 📐 ¿Qué es un fractal?

Un fractal es una figura que tiene **dos propiedades clave**:

| Propiedad | ¿Qué significa? | Ejemplo visual |
|---|---|---|
| **Autosimilitud** | Cada parte se parece al todo (amplificas y ves lo mismo) | Un brócoli romanesco, cada ramita es un brócoli en miniatura |
| **Dimensión fractal** | No es 1D, 2D ni 3D — tiene una **dimensión fraccionaria** | Entre una línea (1D) y un plano (2D) |

### Dimensión fractal — el truco mental

Una línea recta ocupa **1 dimensión**. Un cuadrado ocupa **2**. Pero una línea tan retorcida que casi llena el plano puede tener **dimensión ~1.26** — como el **Copo de nieve de Koch**:

```
Línea recta:         ——————    Dimensión = 1
Copo de Koch:        ╱╲         Dimensión = 1.26
Curva que llena:     〰️〰️       Dimensión = 2
```

**Fórmula**: si divides una figura en `N` copias, cada una reducida por un factor `r`, entonces:
```
Dimensión = log(N) / log(1/r)
```

---

## 🌿 Fractales en la naturaleza

Los fractales **no son inventos matemáticos** — son la forma en que la naturaleza construye cosas complejas con reglas simples:

| Fractal natural | Cómo aparece |
|---|---|
| 🌲 **Árboles** | Cada rama se divide en ramas más pequeñas, idénticas en forma |
| 🫁 **Pulmones** | 2,400 km de bronquiolos en un fractal que cabe en el pecho |
| 🧠 **Cerebro** | Circunvoluciones — arrugas fractales que maximizan superficie |
| ⚡ **Rayos** | El canal principal se bifurca en canales más pequeños |
| 🏔️ **Costas** | La línea de costa es infinita (paradoja de la longitud de costa) |
| 🥦 **Romanesco** | Cada cono contiene conos más pequeños idénticos |

> *"Los fractales son la geometría de la naturaleza"* — Benoit Mandelbrot

---

## 💎 El Conjunto de Mandelbrot

El fractal más famoso del mundo. Una ecuación **increíblemente simple**:

```
Z → Z² + C
```

Donde `Z` y `C` son **números complejos**. Se itera esta ecuación para cada punto `C`. Si la serie se dispara al infinito → el punto está **fuera** del conjunto (negro... bueno, en el dibujo tradicional es al revés). Si se mantiene acotada → está **dentro**.

Lo alucinante: el borde del conjunto de Mandelbrot tiene **detalle infinito**. Puedes hacer zoom millones de veces y siempre encuentras **nuevos patrones** que nunca se repiten exactamente (como el caos), pero tienen la misma **familia de formas** (autosimilitud).

---

Voy a crearte el HTML interactivo.

Página lista. Se abrió en tu navegador con **tres herramientas** para explorar fractales. Aquí te va el tour:

---

## 🧭 Qué hay en la página

### 1️⃣ Copo de Nieve de Koch ❄️
- Controla el **nivel de iteración** (0 a 6)
- Nivel 0: un triángulo simple
- Nivel 1: cada lado se divide en 4 → 12 segmentos
- Nivel 6: **4,096 segmentos** — un copo de nieve con perímetro **infinito** que encierra un área **finita**
- *Paradoja*: ¡puedes calcular su área pero no su perímetro exacto!

### 2️⃣ Conjunto de Mandelbrot 🌀
- **Arrástralo** para moverte por el plano complejo
- **Rueda del mouse** para hacer zoom (hasta 10¹²)
- Cada zona de color es una región donde la iteración converge a diferentes velocidades
- **Haz zoom en el borde** — verás copias en miniatura del conjunto completo (autosimilitud)

### 3️⃣ Triángulo de Sierpinski 🔺
- Controla el **nivel de iteración** (0 a 7)
- Nivel 0: un triángulo sólido
- Nivel 7: **¡4,782,969 triángulos!**
- Cada triángulo contiene 3 copias de sí mismo, reducidas a la mitad

---

## 🔬 Experimentos

| Fractal | Prueba | Qué observar |
|---------|--------|-------------|
| **Koch** | Sube nivel 0→6 | El perímetro crece infinitamente, el área apenas cambia |
| **Mandelbrot** | Click en el borde + zoom | Autosimilitud: ves mini-Mandelbrots |
| **Sierpinski** | Nivel 5+ | Cuenta los triángulos: `3^n` — crecimiento exponencial |
| **Mandelbrot** | Zoom en la costa + arrastra | El caos se vuelve geometría |

---

## 🧠 La lección

Tres fractales, tres formas distintas de llegar a la misma idea:

> **Reglas simples + iteración infinita = complejidad infinita**

- **Koch**: regla geométrica sobre un segmento
- **Mandelbrot**: ecuación cuadrática en el plano complejo
- **Sierpinski**: quitar el triángulo del medio

Todos producen **detalle infinito desde simplicidad**.

```html
<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>🌀 Módulo 3: Fractales — Complexity Explorer</title>
<style>
  * { box-sizing: border-box; margin: 0; padding: 0; }
  body { font-family: 'Segoe UI', system-ui, sans-serif; background: #0a0a1a; color: #e0e0e0; overflow-x: hidden; }
  .container { max-width: 1400px; margin: 0 auto; padding: 20px; }
  
  /* Header */
  header { text-align: center; padding: 30px 0; border-bottom: 1px solid #2a2a4a; margin-bottom: 30px; }
  header h1 { font-size: 2.5em; background: linear-gradient(135deg, #ff6b6b, #ffa94d, #ffd43b); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }
  header p { color: #888; margin-top: 8px; font-size: 1.1em; }
  header .sfi { color: #ffa94d; font-weight: bold; }

  /* Tabs */
  .tabs { display: flex; gap: 4px; margin-bottom: 20px; flex-wrap: wrap; }
  .tab { flex: 1; min-width: 120px; padding: 14px; text-align: center; background: #1a1a3a; border: none; color: #888; cursor: pointer; font-size: 1em; border-radius: 8px 8px 0 0; transition: all 0.3s; }
  .tab:hover { background: #2a2a5a; color: #ccc; }
  .tab.active { background: #2a2a5a; color: #ffd43b; border-bottom: 3px solid #ffd43b; }

  .panel { display: none; background: #12122a; border-radius: 0 12px 12px 12px; padding: 25px; border: 1px solid #2a2a4a; }
  .panel.active { display: block; }

  /* Canvas */
  canvas { display: block; margin: 0 auto; border-radius: 8px; background: #0a0a1a; }

  /* Controls */
  .controls { display: flex; flex-wrap: wrap; gap: 15px; align-items: center; margin: 15px 0; padding: 15px; background: #1a1a3a; border-radius: 8px; }
  .controls label { display: flex; align-items: center; gap: 10px; color: #aaa; font-size: 0.9em; }
  .controls input[type="range"] { width: 140px; accent-color: #ffa94d; }
  .controls .val { color: #ffd43b; font-weight: bold; min-width: 30px; display: inline-block; }
  .btn { padding: 8px 20px; border: none; border-radius: 6px; cursor: pointer; font-weight: bold; transition: all 0.2s; font-size: 0.9em; }
  .btn-primary { background: #ffa94d; color: #1a1a2a; }
  .btn-primary:hover { background: #ff922b; transform: scale(1.02); }
  .btn-secondary { background: #3a3a6a; color: #ccc; }
  .btn-secondary:hover { background: #4a4a8a; }

  .info-box { background: #1a1a3a; border-left: 4px solid #ffa94d; padding: 15px; margin: 15px 0; border-radius: 0 8px 8px 0; }
  .info-box strong { color: #ffd43b; }

  .stats { display: grid; grid-template-columns: repeat(auto-fit, minmax(180px, 1fr)); gap: 10px; margin: 15px 0; }
  .stat-card { background: #1a1a3a; padding: 15px; border-radius: 8px; text-align: center; }
  .stat-card .num { font-size: 1.8em; color: #ffd43b; font-weight: bold; }
  .stat-card .label { font-size: 0.8em; color: #888; margin-top: 4px; }

  .theory { margin-top: 20px; }
  .theory h3 { color: #ffa94d; margin: 15px 0 8px; }
  .theory p { line-height: 1.7; color: #b8b8d0; margin-bottom: 10px; }
  .theory .formula { background: #0a0a2a; padding: 12px 20px; border-radius: 8px; font-family: 'Courier New', monospace; font-size: 1.1em; color: #ffd43b; text-align: center; margin: 10px 0; border: 1px solid #2a2a4a; }
  .highlight { color: #ffa94d; font-weight: bold; }
  
  @media (max-width: 768px) {
    header h1 { font-size: 1.5em; }
    .tab { font-size: 0.8em; min-width: 80px; padding: 10px; }
    canvas { width: 100% !important; height: auto !important; }
  }
</style>
</head>
<body>

<div class="container">
  <header>
    <h1>🌀 Módulo 3: Fractales</h1>
    <p><span class="sfi">Santa Fe Institute</span> · Complexity Explorer · Introducción a la Complejidad</p>
    <p style="font-size:0.9em; margin-top:6px; color:#666;">Autosimilitud · Dimensión Fractal · La geometría del caos</p>
  </header>

  <!-- Tabs -->
  <div class="tabs">
    <button class="tab active" data-tab="koch">❄️ Copo de Koch</button>
    <button class="tab" data-tab="mandelbrot">🌀 Mandelbrot</button>
    <button class="tab" data-tab="sierpinski">🔺 Sierpinski</button>
    <button class="tab" data-tab="theory">📖 Teoría</button>
  </div>

  <!-- Panel: Koch -->
  <div class="panel active" id="panel-koch">
    <div class="controls">
      <label>Nivel de iteración: <input type="range" id="koch-level" min="0" max="6" value="3"> <span class="val" id="koch-level-val">3</span></label>
      <button class="btn btn-primary" id="koch-draw">❄️ Generar</button>
      <button class="btn btn-secondary" id="koch-animate">▶ Animación</button>
    </div>
    <div class="stats">
      <div class="stat-card"><div class="num" id="koch-segments">48</div><div class="label">Segmentos</div></div>
      <div class="stat-card"><div class="num" id="koch-length">2.37</div><div class="label">Perímetro (× base)</div></div>
      <div class="stat-card"><div class="num" id="koch-area">0.58</div><div class="label">Área (× triángulo inicial)</div></div>
    </div>
    <div style="text-align:center;">
      <canvas id="koch-canvas" width="700" height="500"></canvas>
    </div>
    <div class="info-box">
      <strong>🤯 Paradoja del copo de nieve:</strong> Con cada iteración el <strong>perímetro crece infinitamente</strong> (×4/3 cada nivel), pero el <strong>área converge</strong> a un valor finito. ¡Una línea infinita encerrando un área finita!
    </div>
  </div>

  <!-- Panel: Mandelbrot -->
  <div class="panel" id="panel-mandelbrot">
    <div class="controls">
      <label>Zoom: <input type="range" id="mandel-zoom" min="0" max="14" value="0" step="1"> <span class="val" id="mandel-zoom-val">1×</span></label>
      <label>Iteraciones: <input type="range" id="mandel-iter" min="20" max="500" value="80"> <span class="val" id="mandel-iter-val">80</span></label>
      <button class="btn btn-primary" id="mandel-reset">🔄 Reset vista</button>
      <button class="btn btn-secondary" id="mandel-render">🎨 Renderizar</button>
    </div>
    <div style="text-align:center; position:relative;">
      <canvas id="mandel-canvas" width="700" height="500" style="cursor:crosshair;"></canvas>
      <div style="margin-top:8px; color:#888; font-size:0.85em;">
        🖱️ Arrastra para moverte · Rueda para hacer zoom · Haz clic para centrar
      </div>
    </div>
    <div class="info-box">
      <strong>🌀 Autosimilitud en el borde:</strong> Haz zoom en el borde del conjunto — encontrarás <strong>mini-Mandelbrots</strong> completos que se parecen al original. ¡Detalle infinito!
    </div>
  </div>

  <!-- Panel: Sierpinski -->
  <div class="panel" id="panel-sierpinski">
    <div class="controls">
      <label>Nivel de iteración: <input type="range" id="sierp-level" min="0" max="7" value="4"> <span class="val" id="sierp-level-val">4</span></label>
      <button class="btn btn-primary" id="sierp-draw">🔺 Generar</button>
      <button class="btn btn-secondary" id="sierp-animate">▶ Animación</button>
    </div>
    <div class="stats">
      <div class="stat-card"><div class="num" id="sierp-triangles">81</div><div class="label">Triángulos</div></div>
      <div class="stat-card"><div class="num" id="sierp-area-pct">43.7</div><div class="label">% Área del original</div></div>
      <div class="stat-card"><div class="num" id="sierp-dim">1.585</div><div class="label">Dimensión fractal</div></div>
    </div>
    <div style="text-align:center;">
      <canvas id="sierp-canvas" width="700" height="500"></canvas>
    </div>
    <div class="info-box">
      <strong>🔺 Matemática pura:</strong> El triángulo de Sierpinski tiene <strong>dimensión fractal = 1.585</strong> — está entre una línea (1D) y un plano (2D). Más que una línea, menos que un cuadrado.
    </div>
  </div>

  <!-- Panel: Theory -->
  <div class="panel" id="panel-theory">
    <div class="theory">
      <h3>📐 ¿Qué es un fractal?</h3>
      <p>Un fractal es una figura geométrica que posee <span class="highlight">autosimilitud</span> a diferentes escalas. Esto significa que si amplificas una parte del fractal, obtienes una forma similar al todo. La palabra fue acuñada por <strong>Benoit Mandelbrot</strong> en 1975, del latín <em>fractus</em> ("fragmentado" o "irregular").</p>

      <h3>📏 Dimensión fractal</h3>
      <p>Mientras que la geometría euclidiana usa dimensiones enteras (0D: punto, 1D: línea, 2D: plano, 3D: volumen), los fractales tienen <span class="highlight">dimensión fraccionaria</span>.</p>
      <div class="formula">D = log(N) / log(1/r)</div>
      <p>Donde <strong>N</strong> = número de copias de sí mismo, y <strong>r</strong> = factor de reducción.</p>
      <p>Ejemplos: 
        <br>• Copo de Koch: D = log(4) / log(3) ≈ <span class="highlight">1.2619</span>
        <br>• Triángulo de Sierpinski: D = log(3) / log(2) ≈ <span class="highlight">1.5850</span>
        <br>• Curva que llena el plano: D = <span class="highlight">2.0</span>
      </p>

      <h3>🌿 Fractales en la naturaleza</h3>
      <p>La naturaleza usa fractales para maximizar superficie en espacio limitado:</p>
      <p>
        🫁 <strong>Pulmones</strong> — 2,400 km de bronquiolos en un fractal que cabe en el pecho<br>
        🧠 <strong>Cerebro</strong> — Circunvoluciones que multiplican la superficie cortical<br>
        🌲 <strong>Árboles</strong> — Cada rama repite el patrón de ramificación del tronco<br>
        🏔️ <strong>Costas</strong> — La longitud de una costa depende de la escala de medición (paradoja de Mandelbrot)<br>
        ⚡ <strong>Rayos</strong> — El canal principal se bifurca en canales más pequeños
      </p>

      <h3>🔄 Conexión con el caos</h3>
      <p>El <span class="highlight">diagrama de bifurcación</span> del mapa logístico (Módulo 2) es un fractal. Sus ramas que se duplican infinitamente tienen autosimilitud: al hacer zoom en una bifurcación ves el mismo patrón. El <strong>conjunto de Mandelbrot</strong> es en sí mismo una especie de "mapa" del comportamiento del mapa logístico para números complejos.</p>

      <h3>💎 El conjunto de Mandelbrot</h3>
      <div class="formula">Z<sub>n+1</sub> = Z<sub>n</sub>² + C</div>
      <p>Donde Z y C son números complejos. Si la órbita de Z escapa al infinito → el punto C está <strong>fuera</strong> del conjunto. Si permanece acotado → está <strong>dentro</strong>. El borde del conjunto es <span class="highlight">infinitamente detallado</span>: sin importar cuánto amplifiques, siempre hay nuevos patrones.</p>
      
      <h3>🔑 La lección clave</h3>
      <p style="font-size:1.2em; padding: 15px; background: #1a1a3a; border-radius: 8px; border-left: 4px solid #ffd43b;">
        "Los fractales nos muestran que la <span class="highlight">simplicidad</span> (reglas simples) más la <span class="highlight">iteración</span> produce <span class="highlight">complejidad infinita</span> — y esa complejidad tiene una <span class="highlight">geometría</span> que podemos medir."
      </p>
    </div>
  </div>
</div>

<script>
// ============== KOCH SNOWFLAKE ==============
const kochCanvas = document.getElementById('koch-canvas');
const kochCtx = kochCanvas.getContext('2d');

function kochLine(x1, y1, x2, y2, level) {
  if (level === 0) {
    kochCtx.lineTo(x2, y2);
    return;
  }
  const dx = x2 - x1, dy = y2 - y1;
  const xA = x1 + dx/3, yA = y1 + dy/3;
  const xB = x1 + 2*dx/3, yB = y1 + 2*dy/3;
  const mx = (x1 + x2)/2, my = (y1 + y2)/2;
  const h = Math.sqrt(3)/6 * Math.sqrt(dx*dx + dy*dy);
  const nx = (y1 - y2) / Math.sqrt(dx*dx + dy*dy) * h;
  const ny = (x2 - x1) / Math.sqrt(dx*dx + dy*dy) * h;
  const xC = mx + nx, yC = my + ny;
  kochLine(x1, y1, xA, yA, level-1);
  kochLine(xA, yA, xC, yC, level-1);
  kochLine(xC, yC, xB, yB, level-1);
  kochLine(xB, yB, x2, y2, level-1);
}

function drawKoch(level) {
  const w = kochCanvas.width, h = kochCanvas.height;
  kochCtx.clearRect(0, 0, w, h);
  const size = Math.min(w, h) * 0.38;
  const cx = w/2, cy = h/2 + 10;
  const x1 = cx - size/2, y1 = cy + size * Math.sqrt(3)/6;
  const x2 = cx + size/2, y2 = cy + size * Math.sqrt(3)/6;
  const x3 = cx, y3 = cy - size * Math.sqrt(3)/3;
  kochCtx.beginPath();
  kochCtx.moveTo(x1, y1);
  kochLine(x1, y1, x2, y2, level);
  kochLine(x2, y2, x3, y3, level);
  kochLine(x3, y3, x1, y1, level);
  kochCtx.closePath();
  const grad = kochCtx.createLinearGradient(0,0,w,h);
  grad.addColorStop(0, '#4fc3f7');
  grad.addColorStop(0.5, '#81c784');
  grad.addColorStop(1, '#ffb74d');
  kochCtx.fillStyle = 'rgba(20,40,80,0.5)';
  kochCtx.fill();
  kochCtx.strokeStyle = grad;
  kochCtx.lineWidth = 1.5;
  kochCtx.stroke();
  
  const segs = 3 * Math.pow(4, level);
  const len = Math.pow(4/3, level);
  const area = 1 + (level > 0 ? 1/3 * (1 - Math.pow(4/9, level)) / (1 - 4/9) * 3 : 0);
  document.getElementById('koch-segments').textContent = segs.toLocaleString();
  document.getElementById('koch-length').textContent = len.toFixed(3);
  document.getElementById('koch-area').textContent = area.toFixed(2);
}

document.getElementById('koch-level').addEventListener('input', function() {
  document.getElementById('koch-level-val').textContent = this.value;
});
document.getElementById('koch-draw').addEventListener('click', function() {
  drawKoch(parseInt(document.getElementById('koch-level').value));
});

let kochAnim = null;
document.getElementById('koch-animate').addEventListener('click', function() {
  if (kochAnim) { clearInterval(kochAnim); kochAnim = null; this.textContent = '▶ Animación'; return; }
  this.textContent = '⏸ Detener';
  let level = 0;
  drawKoch(level);
  document.getElementById('koch-level').value = level;
  document.getElementById('koch-level-val').textContent = level;
  kochAnim = setInterval(() => {
    level++;
    if (level > 6) { clearInterval(kochAnim); kochAnim = null; document.getElementById('koch-animate').textContent = '▶ Animación'; return; }
    drawKoch(level);
    document.getElementById('koch-level').value = level;
    document.getElementById('koch-level-val').textContent = level;
  }, 800);
});

// ============== MANDELBROT ==============
const mandelCanvas = document.getElementById('mandel-canvas');
const mandelCtx = mandelCanvas.getContext('2d');
let mandelState = { xMin: -2.5, xMax: 1.5, yMin: -1.25, yMax: 1.25, maxIter: 80 };
let isDragging = false, dragStart = {x:0,y:0};

function mandelbrot(cx, cy, maxIter) {
  let zx = 0, zy = 0, i = 0;
  while (i < maxIter && zx*zx + zy*zy < 4) {
    const tmp = zx*zx - zy*zy + cx;
    zy = 2*zx*zy + cy;
    zx = tmp;
    i++;
  }
  return i;
}

function renderMandel() {
  const w = mandelCanvas.width, h = mandelCanvas.height;
  const { xMin, xMax, yMin, yMax, maxIter } = mandelState;
  const imgData = mandelCtx.createImageData(w, h);
  const data = imgData.data;
  const xRange = xMax - xMin, yRange = yMax - yMin;
  
  for (let py = 0; py < h; py++) {
    for (let px = 0; px < w; px++) {
      const cx = xMin + (px / w) * xRange;
      const cy = yMin + (py / h) * yRange;
      const iter = mandelbrot(cx, cy, maxIter);
      const idx = (py * w + px) * 4;
      
      if (iter === maxIter) {
        data[idx] = 10; data[idx+1] = 10; data[idx+2] = 30;
      } else {
        const t = iter / maxIter;
        const r = Math.floor(9 * (1-t) * t * t * t * 255);
        const g = Math.floor(15 * (1-t) * (1-t) * t * t * 255);
        const b = Math.floor(8.5 * (1-t) * (1-t) * (1-t) * t * 255);
        data[idx] = Math.min(255, r);
        data[idx+1] = Math.min(255, g);
        data[idx+2] = Math.min(255, b);
      }
      data[idx+3] = 255;
    }
  }
  mandelCtx.putImageData(imgData, 0, 0);
  document.getElementById('mandel-iter-val').textContent = maxIter;
}

function mandelZoom(factor, cx, cy) {
  const { xMin, xMax, yMin, yMax } = mandelState;
  const xRange = xMax - xMin, yRange = yMax - yMin;
  const nx = cx !== undefined ? cx : (xMin + xMax) / 2;
  const ny = cy !== undefined ? cy : (yMin + yMax) / 2;
  mandelState.xMin = nx - xRange * factor / 2;
  mandelState.xMax = nx + xRange * factor / 2;
  mandelState.yMin = ny - yRange * factor / 2;
  mandelState.yMax = ny + yRange * factor / 2;
  renderMandel();
}

mandelCanvas.addEventListener('wheel', function(e) {
  e.preventDefault();
  const rect = this.getBoundingClientRect();
  const px = (e.clientX - rect.left) / rect.width;
  const py = (e.clientY - rect.top) / rect.height;
  const { xMin, xMax, yMin, yMax } = mandelState;
  const cx = xMin + px * (xMax - xMin);
  const cy = yMin + py * (yMax - yMin);
  const factor = e.deltaY > 0 ? 1.2 : 0.833;
  mandelZoom(factor, cx, cy);
  const zoomLevel = document.getElementById('mandel-zoom');
  const curZ = parseFloat(zoomLevel.value);
  zoomLevel.value = Math.min(14, Math.max(0, curZ + (e.deltaY > 0 ? 0.5 : -0.5)));
  document.getElementById('mandel-zoom-val').textContent = Math.pow(1.5, parseFloat(zoomLevel.value)).toFixed(1) + '×';
});

mandelCanvas.addEventListener('mousedown', function(e) {
  isDragging = true;
  const rect = this.getBoundingClientRect();
  dragStart.x = e.clientX - rect.left;
  dragStart.y = e.clientY - rect.top;
});

mandelCanvas.addEventListener('mousemove', function(e) {
  if (!isDragging) return;
});

mandelCanvas.addEventListener('mouseup', function(e) {
  if (!isDragging) return;
  isDragging = false;
  const rect = this.getBoundingClientRect();
  const endX = e.clientX - rect.left;
  const endY = e.clientY - rect.top;
  const dx = (endX - dragStart.x) / this.width;
  const dy = (endY - dragStart.y) / this.height;
  if (Math.abs(dx) < 0.01 && Math.abs(dy) < 0.01) {
    // click — center
    const px = endX / this.width;
    const py = endY / this.height;
    const { xMin, xMax, yMin, yMax } = mandelState;
    const cx = xMin + px * (xMax - xMin);
    const cy = yMin + py * (yMax - yMin);
    const xRange = xMax - xMin, yRange = yMax - yMin;
    mandelState.xMin = cx - xRange/2;
    mandelState.xMax = cx + xRange/2;
    mandelState.yMin = cy - yRange/2;
    mandelState.yMax = cy + yRange/2;
    renderMandel();
    return;
  }
  const { xMin, xMax, yMin, yMax } = mandelState;
  const xRange = xMax - xMin, yRange = yMax - yMin;
  mandelState.xMin -= dx * xRange;
  mandelState.xMax -= dx * xRange;
  mandelState.yMin -= dy * yRange;
  mandelState.yMax -= dy * yRange;
  renderMandel();
});

document.getElementById('mandel-reset').addEventListener('click', function() {
  mandelState.xMin = -2.5; mandelState.xMax = 1.5;
  mandelState.yMin = -1.25; mandelState.yMax = 1.25;
  document.getElementById('mandel-zoom').value = 0;
  document.getElementById('mandel-zoom-val').textContent = '1×';
  renderMandel();
});

document.getElementById('mandel-render').addEventListener('click', function() {
  mandelState.maxIter = parseInt(document.getElementById('mandel-iter').value);
  renderMandel();
});

document.getElementById('mandel-zoom').addEventListener('input', function() {
  const factor = Math.pow(1.5, parseFloat(this.value));
  document.getElementById('mandel-zoom-val').textContent = factor.toFixed(1) + '×';
});

document.getElementById('mandel-iter').addEventListener('input', function() {
  document.getElementById('mandel-iter-val').textContent = this.value;
});

// ============== SIERPINSKI ==============
const sierpCanvas = document.getElementById('sierp-canvas');
const sierpCtx = sierpCanvas.getContext('2d');

function drawSierpinski(level) {
  const w = sierpCanvas.width, h = sierpCanvas.height;
  sierpCtx.clearRect(0, 0, w, h);
  const size = Math.min(w, h) * 0.42;
  const cx = w/2, cy = h/2 + 10;
  const x1 = cx - size/2, y1 = cy + size * Math.sqrt(3)/6;
  const x2 = cx + size/2, y2 = cy + size * Math.sqrt(3)/6;
  const x3 = cx, y3 = cy - size * Math.sqrt(3)/3;

  function sierp(x1, y1, x2, y2, x3, y3, level) {
    if (level === 0) {
      sierpCtx.beginPath();
      sierpCtx.moveTo(x1, y1);
      sierpCtx.lineTo(x2, y2);
      sierpCtx.lineTo(x3, y3);
      sierpCtx.closePath();
      const grad = sierpCtx.createRadialGradient(
        (x1+x2+x3)/3, (y1+y2+y3)/3, 0,
        (x1+x2+x3)/3, (y1+y2+y3)/3, size/2
      );
      grad.addColorStop(0, '#ffa94d');
      grad.addColorStop(1, '#e65100');
      sierpCtx.fillStyle = grad;
      sierpCtx.fill();
      sierpCtx.strokeStyle = '#ffd43b';
      sierpCtx.lineWidth = 0.5;
      sierpCtx.stroke();
      return;
    }
    const mx1 = (x1 + x2) / 2, my1 = (y1 + y2) / 2;
    const mx2 = (x2 + x3) / 2, my2 = (y2 + y3) / 2;
    const mx3 = (x3 + x1) / 2, my3 = (y3 + y1) / 2;
    sierp(x1, y1, mx1, my1, mx3, my3, level-1);
    sierp(x2, y2, mx1, my1, mx2, my2, level-1);
    sierp(x3, y3, mx2, my2, mx3, my3, level-1);
  }
  sierp(x1, y1, x2, y2, x3, y3, level);
  
  const tris = Math.pow(3, level);
  const areaPct = Math.pow(3/4, level) * 100;
  const dim = Math.log(3) / Math.log(2);
  document.getElementById('sierp-triangles').textContent = tris.toLocaleString();
  document.getElementById('sierp-area-pct').textContent = areaPct.toFixed(1);
  document.getElementById('sierp-dim').textContent = dim.toFixed(3);
}

document.getElementById('sierp-level').addEventListener('input', function() {
  document.getElementById('sierp-level-val').textContent = this.value;
});

document.getElementById('sierp-draw').addEventListener('click', function() {
  drawSierpinski(parseInt(document.getElementById('sierp-level').value));
});

let sierpAnim = null;
document.getElementById('sierp-animate').addEventListener('click', function() {
  if (sierpAnim) { clearInterval(sierpAnim); sierpAnim = null; this.textContent = '▶ Animación'; return; }
  this.textContent = '⏸ Detener';
  let level = 0;
  drawSierpinski(level);
  document.getElementById('sierp-level').value = level;
  document.getElementById('sierp-level-val').textContent = level;
  sierpAnim = setInterval(() => {
    level++;
    if (level > 7) { clearInterval(sierpAnim); sierpAnim = null; document.getElementById('sierp-animate').textContent = '▶ Animación'; return; }
    drawSierpinski(level);
    document.getElementById('sierp-level').value = level;
    document.getElementById('sierp-level-val').textContent = level;
  }, 700);
});

// ============== TABS ==============
document.querySelectorAll('.tab').forEach(tab => {
  tab.addEventListener('click', function() {
    document.querySelectorAll('.tab').forEach(t => t.classList.remove('active'));
    document.querySelectorAll('.panel').forEach(p => p.classList.remove('active'));
    this.classList.add('active');
    document.getElementById('panel-' + this.dataset.tab).classList.add('active');
  });
});

// ============== INIT ==============
drawKoch(3);
renderMandel();
drawSierpinski(4);
</script>
</body>
</html>
```

---

## 🗺️ Lo que tienes en la página

### ❄️ **Copo de Koch** (pestaña 1)
- Controla el **nivel de iteración** (0 a 6)
- Mira cómo el **perímetro se dispara** mientras el **área casi no cambia**
- ¡Prueba la **animación** para verlo crecer solo!

### 🌀 **Mandelbrot** (pestaña 2)
- **Arrastra** para moverte por el plano complejo
- **Rueda del mouse** para hacer zoom infinito
- **Click** para centrarte en un punto
- Los colores muestran qué tan rápido escapa cada punto al infinito

### 🔺 **Sierpinski** (pestaña 3)
- Controla el nivel (0 a 7)
- Nivel 7: **2,187 triángulos** diminutos
- Su **dimensión fractal** es **1.585** — entre una línea y un plano

### 📖 **Teoría** (pestaña 4)
- Explicación completa de **dimensión fractal**, **autosimilitud**, la fórmula y conexión con el caos

