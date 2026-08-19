# 🌐 Módulo 7: Redes

---

## 🔗 La idea central

Hasta ahora hemos visto sistemas con muchos agentes interactuando. Pero hay algo que hemos dado por sentado:

> **¿Quién interactúa con quién?**

La **estructura de las conexiones** — quién está conectado con quién — determina **cómo se comporta el sistema**. Un virus no se propaga igual en una aldea aislada que en una ciudad globalizada. Una idea no se difunde igual en una red jerárquica que en una red horizontal.

Las **redes** son el **esqueleto** de los sistemas complejos.

---

## 📜 Breve historia

| Año | Quién | Qué hizo |
|-----|-------|----------|
| **1736** | Euler | Resuelve el problema de los puentes de Königsberg — nace la **teoría de grafos** |
| **1959** | Erdős–Rényi | Modelo de **red aleatoria** — cada par de nodos tiene la misma probabilidad de conectarse |
| **1967** | Milgram | Experimento del **mundo pequeño** — "6 grados de separación" |
| **1998** | Watts–Strogatz | Modelo de **mundo pequeño** — pocos pasos entre nodos + alta agrupación local |
| **1999** | Barabási–Albert | Modelo **libre de escala** — pocos nodos tienen muchas conexiones, la mayoría tienen pocas |

---

## 📊 Tres tipos fundamentales de redes

### 1️⃣ Red Aleatoria (Erdős–Rényi)

```
Conexiones al azar.
Cada par de nodos: misma probabilidad de estar conectados
```

- **Distribución de grado:** campana de Gauss (poisson)
- La mayoría de nodos tienen ≈ el mismo número de conexiones
- **No hay hubs** (nodos muy conectados)

📸 *Ejemplo real:* casi ninguno. Las redes reales rara vez son así.

---

### 2️⃣ Red de Mundo Pequeño (Watts–Strogatz)

```
Alta agrupación local + pocos pasos entre cualquier par.
"Vecinos que se conocen entre sí" + "atajos"
```

- Amigos de tus amigos son amigos entre sí (triángulos)
- Pero puedes llegar a cualquiera en pocos pasos (6 grados)

📸 *Ejemplos reales:* redes sociales, redes neuronales, el metabolismo celular

---

### 3️⃣ Red Libre de Escala (Barabási–Albert)

```
Pocos nodos tienen MUCHAS conexiones (hubs).
La mayoría tiene muy pocas.
Ley de potencia: P(k) ∝ k^(-γ)
```

- **Crecimiento:** la red crece añadiendo nodos nuevos
- **Apego preferencial:** los nuevos nodos se conectan a los que ya tienen más conexiones ("el rico se hace más rico")

📸 *Ejemplos reales:* Internet, la Web, redes de citación, aeropuertos, el genoma

---

## 📏 Métricas clave de una red

| Métrica | ¿Qué mide? | Ejemplo |
|---------|-----------|---------|
| **Grado (degree)** | Número de conexiones de un nodo | Twitter: @elonmusk tiene 150M seguidores |
| **Coeficiente de agrupamiento** | Qué tanto los vecinos de un nodo se conocen entre sí | Probabilidad de que dos amigos tuyos sean amigos |
| **Camino más corto** | Pasos mínimos entre dos nodos | Separación en la red |
| **Diámetro** | El camino más largo entre cualquier par | Tamaño efectivo de la red |
| **Centralidad** | Qué tan "importante" es un nodo | PageRank de Google |
| **Modularidad** | Si la red tiene comunidades | Departamentos en una empresa |

---

## 🌍 Aplicaciones de redes en sistemas complejos

| Sistema | Lo que las redes revelan |
|---------|------------------------|
| 🦠 **Epidemias** | Los hubs determinan qué tan rápido se propaga un virus. Cortar los hubs → contener la pandemia |
| 🧠 **Cerebro** | El connectoma — las conexiones entre neuronas definen la cognición |
| 📱 **Redes sociales** | Cómo se difunden ideas, memes, noticias falsas |
| 🌐 **Internet** | La estructura de la web determina qué tan robusta es ante ataques |
| 🧬 **Biología** | Redes de proteínas, genes, metabolismo — entender enfermedades |
| 💼 **Economía** | Redes de bancos → riesgo sistémico (2008) |

---

## 🛡️ La paradoja de la robustez

Las redes **libres de escala** son increíblemente **resistentes** a fallos aleatorios:

> Si desconectas nodos al azar, la red sigue funcionando — la mayoría de nodos son irrelevantes.

Pero son **extremadamente frágiles** ante ataques dirigidos:

> Si desconectas los hubs (los pocos nodos súper conectados), la red se **desintegra**.

| Tipo de fallo | Red Aleatoria | Red Libre de Escala |
|--------------|--------------|-------------------|
| Fallo aleatorio | Se degrada gradualmente | Casi no se afecta |
| Ataque dirigido | Se degrada gradualmente | **Colapsa rápidamente** |

**Ejemplo real:** Internet — sobrevive a miles de routers cayéndose al día (fallo aleatorio), pero si atacas los servidores DNS raíz (hubs), el internet se cae.

---

## 🧩 La conexión con lo que ya vimos

| Módulo | Conexión con redes |
|--------|-------------------|
| **1** | Los sistemas complejos **son** redes |
| **2** | La dinámica en redes puede ser caótica o sincronizada |
| **3** | Hay redes **fractales** (autosimilitud en la estructura) |
| **4** | La **entropía** de una red mide qué tan predecible es su estructura |
| **5** | La auto-organización crea redes libres de escala naturalmente (apego preferencial) |
| **6** | Los MBAs a menudo modelan agentes sobre **redes** |

---

## 💎 La gran idea

> **La estructura de las conexiones importa tanto o más que las reglas de los agentes.**

Un modelo de propagación de enfermedades cambia radicalmente si pones a los agentes en una red aleatoria, una de mundo pequeño, o una libre de escala. **La red es el mensaje.**

O como dijo el físico Mark Newman:

> *"La ciencia de redes es el lenguaje de los sistemas complejos."*

```html
<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Módulo 7 — Redes</title>
<style>
  * { box-sizing: border-box; margin: 0; padding: 0; }
  body { font-family: 'Segoe UI', system-ui, sans-serif; background: #0b0e17; color: #e0e6f0; padding: 16px; }
  h1 { text-align: center; font-size: 2rem; margin: 10px 0 6px; background: linear-gradient(135deg,#64b5f6,#ce93d8); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }
  .subtitle { text-align: center; color: #8899b0; margin-bottom: 16px; font-size: 0.95rem; }
  .tabs { display: flex; gap: 4px; justify-content: center; flex-wrap: wrap; margin-bottom: 14px; }
  .tab { padding: 8px 18px; border-radius: 20px; background: #1a1f2e; color: #8899b0; cursor: pointer; border: 1px solid transparent; font-weight: 600; font-size: 0.9rem; transition: 0.2s; }
  .tab:hover { background: #252c3e; }
  .tab.active { background: #2d3a6e; color: #b0c9ff; border-color: #5b7bc4; }
  .panel { display: none; }
  .panel.active { display: block; }
  canvas { display: block; margin: 0 auto; border-radius: 12px; background: #0f1420; width: 100%; max-width: 700px; aspect-ratio: 1/1; }
  .controls { display: flex; flex-wrap: wrap; gap: 8px; justify-content: center; margin: 10px 0; }
  .controls label { font-size: 0.85rem; color: #a0b0c8; display: flex; align-items: center; gap: 6px; }
  .controls input[type="range"] { width: 110px; accent-color: #7c9ae6; }
  .controls select { background: #1a1f2e; color: #e0e6f0; border: 1px solid #334; border-radius: 6px; padding: 4px 8px; }
  .btn { background: #2d3a6e; color: #b0c9ff; border: none; border-radius: 8px; padding: 6px 16px; cursor: pointer; font-weight: 600; font-size: 0.85rem; transition: 0.2s; }
  .btn:hover { background: #3d4e8e; }
  .btn.danger { background: #6e2d2d; color: #ffb0b0; }
  .btn.danger:hover { background: #8e3d3d; }
  .stats { display: flex; flex-wrap: wrap; gap: 10px; justify-content: center; margin: 8px 0 14px; }
  .stat { background: #141a28; border-radius: 10px; padding: 8px 14px; text-align: center; min-width: 100px; }
  .stat .val { font-size: 1.3rem; font-weight: 700; color: #b0c9ff; }
  .stat .lbl { font-size: 0.7rem; color: #6a7a92; text-transform: uppercase; letter-spacing: 0.5px; }
  .info { background: #141a28; border-radius: 12px; padding: 16px; margin: 14px auto; max-width: 700px; font-size: 0.9rem; line-height: 1.6; color: #c0cce0; }
  .info h3 { color: #b0c9ff; margin-bottom: 6px; }
  .info ul { padding-left: 20px; }
  @media (max-width:500px) { h1 { font-size: 1.4rem; } canvas { max-width: 100%; } }
</style>
</head>
<body>
<h1>🌐 Redes</h1>
<p class="subtitle">Aleatoria · Mundo Pequeño · Libre de Escala · Propagación</p>

<div class="tabs" id="tabs">
  <div class="tab active" data-tab="viz">🔵 Visualizador</div>
  <div class="tab" data-tab="prop">🦠 Propagación</div>
  <div class="tab" data-tab="teoria">📖 Teoría</div>
</div>

<!-- Panel 1: Visualizador -->
<div class="panel active" id="panel-viz">
  <canvas id="cviz" width="600" height="600"></canvas>
  <div class="controls">
    <label>Modelo <select id="modelo">
      <option value="er">Aleatoria (ER)</option>
      <option value="ws" selected>Mundo Pequeño (WS)</option>
      <option value="ba">Libre de Escala (BA)</option>
    </select></label>
    <label>Nodos <input type="range" id="nNodos" min="20" max="150" value="80"></label>
    <label>Conectividad <input type="range" id="conect" min="0.02" max="0.3" step="0.01" value="0.08"></label>
    <button class="btn" id="regenerar">🔄 Regenerar</button>
  </div>
  <div class="stats" id="stats-viz">
    <div class="stat"><div class="val" id="s-nodos">0</div><div class="lbl">Nodos</div></div>
    <div class="stat"><div class="val" id="s-enlaces">0</div><div class="lbl">Enlaces</div></div>
    <div class="stat"><div class="val" id="s-grado">0.0</div><div class="lbl">Grado prom.</div></div>
    <div class="stat"><div class="val" id="s-clustering">0.00</div><div class="lbl">Clustering</div></div>
  </div>
  <div class="info" id="info-graph">
    <h3>📌 Mundo Pequeño</h3>
    Alta agrupación local + caminos cortos. Los vecinos de un nodo tienden a conocerse entre sí, pero hay "atajos" que conectan partes lejanas de la red.
  </div>
</div>

<!-- Panel 2: Propagación -->
<div class="panel" id="panel-prop">
  <canvas id="cprop" width="600" height="600"></canvas>
  <div class="controls">
    <label>Red <select id="prop-modelo">
      <option value="er">Aleatoria</option>
      <option value="ws" selected>Mundo Pequeño</option>
      <option value="ba">Libre de Escala</option>
    </select></label>
    <label><span id="lbl-infected">🟥 Infectados: 0</span></label>
    <label><span id="lbl-recovered">🟩 Recuperados: 0</span></label>
    <button class="btn" id="prop-step">⏭️ Propagar</button>
    <button class="btn" id="prop-reset">🔄 Reiniciar</button>
  </div>
  <div class="info" id="info-prop">
    <h3>🦠 Propagación en redes</h3>
    Un nodo infectado (rojo) puede contagiar a sus vecinos. Observa cómo la velocidad y forma de la propagación cambia según el tipo de red. En redes libre de escala, los hubs aceleran el contagio.
  </div>
</div>

<!-- Panel 3: Teoría -->
<div class="panel" id="panel-teoria">
  <div class="info" style="max-width:700px; margin:0 auto;">
    <h3>📐 ¿Qué es una red?</h3>
    <p>Un conjunto de <strong>nodos</strong> conectados por <strong>enlaces</strong>. Las redes son el esqueleto de los sistemas complejos.</p>
    <h3>📊 Los tres modelos fundamentales</h3>
    <ul>
      <li><strong>Aleatoria (Erdős–Rényi):</strong> Cada par tiene igual probabilidad de conectarse. Distribución de grado: Poisson. Sin hubs.</li>
      <li><strong>Mundo Pequeño (Watts–Strogatz):</strong> Alta agrupación local + pocos pasos entre nodos. Como las redes sociales reales.</li>
      <li><strong>Libre de Escala (Barabási–Albert):</strong> Pocos nodos (hubs) tienen muchas conexiones; la mayoría tiene pocas. Ley de potencia. Internet, citaciones, aeropuertos.</li>
    </ul>
    <h3>📏 Métricas clave</h3>
    <ul>
      <li><strong>Grado:</strong> Conexiones por nodo</li>
      <li><strong>Coeficiente de agrupamiento:</strong> Probabilidad de que vecinos estén conectados entre sí</li>
      <li><strong>Camino más corto:</strong> Pasos para llegar de un nodo a otro</li>
      <li><strong>Diámetro:</strong> Máximo camino más corto</li>
    </ul>
    <h3>🛡️ Robustez vs Fragilidad</h3>
    <p>Las redes libre de escala son muy resistentes a fallos aleatorios pero muy frágiles ante ataques dirigidos a los hubs. Las aleatorias se degradan gradualmente en ambos casos.</p>
  </div>
</div>

<script>
// ============== UTIL ==============
const ctxViz = document.getElementById('cviz').getContext('2d');
const ctxProp = document.getElementById('cprop').getContext('2d');
const W = 600, H = 600;

function rand(min, max) { return Math.random() * (max - min) + min; }
function randInt(min, max) { return Math.floor(rand(min, max + 1)); }

// ============== RED ==============
function genER(n, p) {
  const adj = Array.from({length: n}, () => []);
  for (let i = 0; i < n; i++)
    for (let j = i + 1; j < n; j++)
      if (Math.random() < p) { adj[i].push(j); adj[j].push(i); }
  return adj;
}

function genWS(n, k, p) {
  // k debe ser par
  k = Math.floor(k);
  if (k % 2 !== 0) k++;
  const adj = Array.from({length: n}, () => []);
  // anillo regular
  for (let i = 0; i < n; i++)
    for (let d = 1; d <= k/2; d++) {
      const j = (i + d) % n;
      adj[i].push(j); adj[j].push(i);
    }
  // rewiring
  for (let i = 0; i < n; i++)
    for (let j of [...adj[i]]) {
      if (j > i && Math.random() < p) {
        // reconectar
        adj[i] = adj[i].filter(x => x !== j);
        adj[j] = adj[j].filter(x => x !== i);
        let nuevo;
        do { nuevo = randInt(0, n - 1); } while (nuevo === i || adj[i].includes(nuevo));
        adj[i].push(nuevo); adj[nuevo].push(i);
      }
    }
  return adj;
}

function genBA(n, m) {
  const adj = Array.from({length: n}, () => []);
  if (n < 2) return adj;
  // empezar con clique de m+1 nodos
  for (let i = 0; i < Math.min(m + 1, n); i++)
    for (let j = i + 1; j < Math.min(m + 1, n); j++) {
      adj[i].push(j); adj[j].push(i);
    }
  // añadir nodos con apego preferencial
  for (let i = Math.min(m + 1, n); i < n; i++) {
    const targets = new Set();
    while (targets.size < Math.min(m, i)) {
      const grados = adj.map((a, idx) => idx < i ? a.length : 0);
      const total = grados.reduce((a, b) => a + b, 0);
      let r = Math.random() * total;
      for (let j = 0; j < i; j++) {
        r -= grados[j];
        if (r <= 0 && !targets.has(j) && j !== i) { targets.add(j); break; }
      }
    }
    for (let t of targets) { adj[i].push(t); adj[t].push(i); }
  }
  return adj;
}

function posiciones(n) {
  const pts = [];
  for (let i = 0; i < n; i++) {
    const ang = (i / n) * Math.PI * 2 - Math.PI / 2;
    const r = 220 + (Math.random() - 0.5) * 30;
    pts.push({ x: W/2 + Math.cos(ang)*r, y: H/2 + Math.sin(ang)*r });
  }
  return pts;
}

// ============== DIBUJO ==============
function dibujarRed(ctx, adj, pts, colores = null, highlights = null) {
  ctx.clearRect(0, 0, W, H);
  const n = adj.length;
  // enlaces
  ctx.strokeStyle = '#2a3550';
  ctx.lineWidth = 1.2;
  for (let i = 0; i < n; i++) {
    for (let j of adj[i]) {
      if (j > i) {
        ctx.beginPath();
        ctx.moveTo(pts[i].x, pts[i].y);
        ctx.lineTo(pts[j].x, pts[j].y);
        ctx.stroke();
      }
    }
  }
  // nodos
  const r = Math.max(4, Math.min(10, 300 / Math.sqrt(n)));
  for (let i = 0; i < n; i++) {
    ctx.beginPath();
    ctx.arc(pts[i].x, pts[i].y, r, 0, Math.PI * 2);
    if (colores && colores[i]) {
      ctx.fillStyle = colores[i];
    } else if (highlights && highlights.has(i)) {
      ctx.fillStyle = '#ffcc00';
    } else {
      const g = Math.min(adj[i].length * 8, 200);
      ctx.fillStyle = `rgb(${70 + g}, ${100 + g}, ${180 + g})`;
    }
    ctx.fill();
    if (highlights && highlights.has(i)) {
      ctx.strokeStyle = '#ffcc00';
      ctx.lineWidth = 2;
      ctx.stroke();
    }
  }
}

function metricas(adj) {
  const n = adj.length;
  const grados = adj.map(a => a.length);
  const gradoProm = grados.reduce((a,b) => a+b, 0) / n;
  let triangulos = 0, triplas = 0;
  for (let i = 0; i < n; i++) {
    const vecinos = new Set(adj[i]);
    const deg = adj[i].length;
    triplas += deg * (deg - 1) / 2;
    for (let j of adj[i]) {
      if (j > i) {
        for (let k of adj[j]) {
          if (k > j && vecinos.has(k)) triangulos++;
        }
      }
    }
  }
  const clustering = triplas > 0 ? (2 * triangulos) / triplas : 0;
  const enlaces = adj.reduce((s, a) => s + a.length, 0) / 2;
  return { nodos: n, enlaces, gradoProm: gradoProm.toFixed(2), clustering: clustering.toFixed(3) };
}

// ============== PANEL VIZ ==============
let adjViz = [], ptsViz = [];

function generarViz() {
  const n = parseInt(document.getElementById('nNodos').value);
  const tipo = document.getElementById('modelo').value;
  const p = parseFloat(document.getElementById('conect').value);
  let adj;
  if (tipo === 'er') adj = genER(n, p);
  else if (tipo === 'ws') adj = genWS(n, Math.max(4, Math.floor(n * p * 0.5)), 0.15);
  else adj = genBA(n, Math.max(1, Math.floor(p * n * 0.5)));
  adjViz = adj;
  ptsViz = posiciones(n);
  const m = metricas(adj);
  document.getElementById('s-nodos').textContent = m.nodos;
  document.getElementById('s-enlaces').textContent = m.enlaces;
  document.getElementById('s-grado').textContent = m.gradoProm;
  document.getElementById('s-clustering').textContent = m.clustering;
  let desc = '';
  if (tipo === 'er') desc = '<h3>🔵 Aleatoria (Erdős–Rényi)</h3>Conexiones al azar. Distribución de grado: campana de Gauss. Cada par tiene igual probabilidad de estar conectado.';
  else if (tipo === 'ws') desc = '<h3>🟢 Mundo Pequeño (Watts–Strogatz)</h3>Alta agrupación local + caminos cortos. Los vecinos de un nodo tienden a conocerse entre sí, pero hay atajos.';
  else desc = '<h3>🔴 Libre de Escala (Barabási–Albert)</h3>Pocos hubs con muchas conexiones, la mayoría con pocas. Ley de potencia. Crecimiento + apego preferencial.';
  document.getElementById('info-graph').innerHTML = desc;
  dibujarRed(ctxViz, adj, ptsViz);
}

document.getElementById('regenerar').addEventListener('click', generarViz);
document.getElementById('modelo').addEventListener('change', generarViz);
document.getElementById('nNodos').addEventListener('input', generarViz);
document.getElementById('conect').addEventListener('input', generarViz);

// ============== PANEL PROPAGACIÓN ==============
let adjProp = [], ptsProp = [], stateProp = []; // 0=sano,1=infectado,2=recuperado
let infectedCount = 0, recoveredCount = 0;

function generarProp() {
  const n = 80;
  const tipo = document.getElementById('prop-modelo').value;
  let adj;
  if (tipo === 'er') adj = genER(n, 0.06);
  else if (tipo === 'ws') adj = genWS(n, 6, 0.15);
  else adj = genBA(n, 2);
  adjProp = adj;
  ptsProp = posiciones(n);
  stateProp = new Array(n).fill(0);
  // infectar al primero
  stateProp[0] = 1;
  infectedCount = 1; recoveredCount = 0;
  actualizarLabelsProp();
  dibujarProp();
}

function dibujarProp() {
  const colores = stateProp.map(s => {
    if (s === 1) return '#ff4444';
    if (s === 2) return '#44cc66';
    return '#3a4a6e';
  });
  dibujarRed(ctxProp, adjProp, ptsProp, colores);
}

function actualizarLabelsProp() {
  document.getElementById('lbl-infected').textContent = `🟥 Infectados: ${infectedCount}`;
  document.getElementById('lbl-recovered').textContent = `🟩 Recuperados: ${recoveredCount}`;
}

function pasoPropagacion() {
  const nuevos = [];
  for (let i = 0; i < stateProp.length; i++) {
    if (stateProp[i] === 1) {
      // contagiar vecinos
      for (let j of adjProp[i]) {
        if (stateProp[j] === 0 && Math.random() < 0.4) {
          nuevos.push(j);
        }
      }
      // recuperar con probabilidad
      if (Math.random() < 0.2) {
        stateProp[i] = 2;
        infectedCount--;
        recoveredCount++;
      }
    }
  }
  for (let n of nuevos) {
    if (stateProp[n] === 0) {
      stateProp[n] = 1;
      infectedCount++;
    }
  }
  actualizarLabelsProp();
  dibujarProp();
}

document.getElementById('prop-step').addEventListener('click', pasoPropagacion);
document.getElementById('prop-reset').addEventListener('click', generarProp);
document.getElementById('prop-modelo').addEventListener('change', generarProp);

// ============== TABS ==============
document.querySelectorAll('.tab').forEach(tab => {
  tab.addEventListener('click', () => {
    document.querySelectorAll('.tab').forEach(t => t.classList.remove('active'));
    document.querySelectorAll('.panel').forEach(p => p.classList.remove('active'));
    tab.classList.add('active');
    document.getElementById('panel-' + tab.dataset.tab).classList.add('active');
    if (tab.dataset.tab === 'viz') setTimeout(() => dibujarRed(ctxViz, adjViz, ptsViz), 50);
    if (tab.dataset.tab === 'prop') setTimeout(() => dibujarProp(), 50);
  });
});

// ============== INIT ==============
generarViz();
generarProp();
</script>
</body>
</html>
```

---

## 🗺️ Lo que contiene

### 🔵 Pestaña "Visualizador"
- Tres tipos de red seleccionables:
  - **Aleatoria (ER)** — conexiones al azar, sin hubs
  - **Mundo Pequeño (WS)** — alta agrupación local + atajos
  - **Libre de Escala (BA)** — hubs + ley de potencia
- Controles para **nodos** y **conectividad**
- **Métricas en vivo**: grado promedio, coeficiente de agrupamiento, enlaces totales
- La info se actualiza al cambiar de modelo

### 🦠 Pestaña "Propagación"
- Mismos tres tipos de red
- Un nodo inicial infectado (rojo) que contagia vecinos
- **Paso a paso**: aprieta "Propagar" para ver cómo avanza la infección
- Recuperados aparecen en verde
- Observa cómo en **libre de escala** la infección se propaga mucho más rápido por los hubs

### 📖 Pestaña "Teoría"
- Explicación de los tres modelos
- Métricas clave
- Robustez vs fragilidad

---

## 🔬 Experimentos clave

| Prueba | Qué observar |
|--------|-------------|
| **Aleatoria** con muchos nodos | Distribución pareja, sin nodos dominantes |
| **Mundo Pequeño** | Ves grupos locales pero un nodo puede llegar a otro lejano en pocos pasos |
| **Libre de Escala** | Aparecen **hubs** — nodos con muchísimas conexiones |
| **Propagación en libre de escala** | La infección explota rápido porque los hubs la dispersan |
| **Propagación en aleatoria** | Se propaga más lento y de forma más uniforme |
