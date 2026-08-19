# 🤖 Módulo 6: Modelos Basados en Agentes

---

## 🕹️ ¿Qué es un modelo basado en agentes (MBA)?

Un MBA es una forma de simular sistemas complejos partiendo de lo más simple: “agentes” individuales autónomos que siguen reglas locales. La clave: no programas el resultado global, sino el comportamiento de los agentes… y observas qué surge.

> “Los modelos basados en agentes permiten explorar cómo reglas simples a nivel individual producen patrones complejos a nivel global.”

---

### 🧑‍🤝‍🧑 ¿Qué es un “agente”?

- Una entidad autónoma (persona, animal, partícula, robot, célula, empresa, coche, etc.)
- Interactúa con su entorno (y con otros agentes)
- Sigue reglas simples (no conoce el sistema completo)
- Tiene memoria, percepción, puede adaptarse y aprender (en modelos avanzados)
- Piensa local, actúa local… pero el resultado puede ser global

---

## 🔬 Ejemplos clásicos de modelos basados en agentes

| Modelo         | Agentes           | Lo que emerge                               |
|----------------|-------------------|---------------------------------------------|
| **Juego de la Vida** | Celdas vivas/muertas | Patrones complejos y turing-completos        |
| **Schelling**         | Individuos con preferencias      | Segregación urbana espontánea                |
| **Boids**         | Pájaros/individuos          | Bandadas/colectivos sin líder                |
| **Mercados artificiales**   | Empresas, consumidores         | Precios, burbujas, distribución de riqueza   |
| **Modelos de epidemias**    | Personas sanas/enfermas           | Curvas SIR, epidemias y su control           |
| **Hormigas**         | Hormigas con feromonas           | Caminos óptimos y coordinación increíble      |

---

## 📈 ¿Cómo funciona un MBA?

1. **Define el entorno:** una cuadrícula, un grafo, un mapa, etc.
2. **Define los agentes:** atributos, percepciones, reglas de comportamiento.
3. **Simula el tiempo:** turnos/discretización o continuo.
4. **Observa:** ¿Qué patrones emergen? ¿Qué variables globales puedes medir?

---

## 🎲 Ventajas de los MBAs

- Capturan dinámica y heterogeneidad (cada agente puede ser único)
- Fáciles de modificar y experimentar (“¿Qué pasa si cambio la regla X?”)
- Pueden producir fenómenos imprevistos (patrones realmente emergentes)
- Algunos incluso pueden aprender (“agentes inteligentes”)
- Te permiten jugar a ser “dios”: modificas reglas y ves qué pasa

---

## 🧱 Ejemplo: Modelo de Schelling – Segregación espontánea

Un clásico de la complejidad social (Thomas Schelling, 1971):

- Agentes de dos tipos (por ejemplo, círculos rojos y azules) viven en una cuadrícula.
- Cada uno se mueve si su proporción de vecinos “distintos” supera un umbral de tolerancia (por ejemplo, solo quiero que el 30% de mis vecinos sean diferentes a mí).
- Aunque cada agente solo tiene una leve preferencia, el resultado global es… ¡segregación total! No lo pidió nadie, simplemente surge.

---

## 👾 Herramientas populares

- **NetLogo** (muy usado para aprendizaje y prototipos)
- **Mesa** (Python)
- **Repast** (Java)
- **MASON** (Java)
- **Swarm** (histórico, C)

Muchos modelos en Complexity Explorer se muestran con NetLogo, pero los conceptos aplican a cualquier lenguaje.

---

## 🎥 ¿Por qué importa esta idea?

Es la mejor forma de explorar la magia de la emergencia: ves cómo comportamientos locales (individuales, egoístas, estúpidos) generan resultados “inteligentes”, “sociales” o “bellos” al nivel sistémico, **sin programar el resultado**.

Una cita de Melanie Mitchell:
> "Agent-based models allow us to analyze the connection between micro-level rules and macro-level patterns — the central question of complexity science."

---

## 🚨 Lo fundamental: No programas el resultado, programas las reglas

El MBA es una “caja de sorpresas”: modificas reglas simples y el sistema puede cambiar completamente… ¡o no!

```html
<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Módulo 6 - Modelos Basados en Agentes · Segregación de Schelling</title>
<style>
*{box-sizing:border-box;margin:0;padding:0}
body{font-family:'Segoe UI',system-ui,sans-serif;background:#0f0f1a;color:#e0e0e0;display:flex;flex-direction:column;align-items:center;padding:20px;min-height:100vh}
h1{font-size:1.6rem;margin-bottom:4px;background:linear-gradient(135deg,#f7971e,#ffd200);-webkit-background-clip:text;-webkit-text-fill-color:transparent}
.sub{color:#888;font-size:.9rem;margin-bottom:18px}
.card{background:#1a1a2e;border-radius:16px;padding:20px;max-width:1000px;width:100%;box-shadow:0 8px 32px rgba(0,0,0,.5);margin-bottom:20px}
canvas{display:block;margin:0 auto;border-radius:8px;background:#16213e;width:100%;max-width:600px;height:auto;aspect-ratio:1;cursor:pointer;touch-action:none}
.controls{display:flex;flex-wrap:wrap;gap:12px;justify-content:center;margin-top:16px;align-items:center}
.controls label{font-size:.85rem;color:#aaa;display:flex;align-items:center;gap:8px}
.controls input[type=range]{width:100px;accent-color:#f7971e}
.controls input[type=number]{width:60px;background:#0f0f1a;border:1px solid #333;color:#fff;border-radius:6px;padding:4px 8px;font-size:.85rem}
button{padding:8px 18px;border:none;border-radius:8px;font-weight:600;font-size:.9rem;cursor:pointer;transition:.2s}
.btn-play{background:#2ecc71;color:#fff}
.btn-play:hover{background:#27ae60}
.btn-step{background:#3498db;color:#fff}
.btn-step:hover{background:#2980b9}
.btn-reset{background:#e74c3c;color:#fff}
.btn-reset:hover{background:#c0392b}
.btn-pause{background:#f39c12;color:#fff}
.btn-pause:hover{background:#d68910}
.stats{display:flex;flex-wrap:wrap;gap:16px;justify-content:center;margin-top:12px;font-size:.85rem}
.stat{background:#0f0f1a;padding:8px 16px;border-radius:8px;border:1px solid #2a2a3e}
.stat span{color:#f7971e;font-weight:700}
.graph-container{width:100%;max-width:600px;margin:16px auto 0;background:#0f0f1a;border-radius:8px;padding:10px;position:relative;height:70px}
.graph-canvas{width:100%;height:60px;border-radius:4px}
.info-box{background:#16213e;border-radius:8px;padding:14px;margin-top:14px;font-size:.85rem;line-height:1.6;border-left:3px solid #f7971e}
.info-box strong{color:#ffd200}
.tabs{display:flex;gap:8px;margin-bottom:14px;flex-wrap:wrap}
.tab{padding:8px 16px;border-radius:8px;cursor:pointer;font-size:.85rem;background:#0f0f1a;border:1px solid #2a2a3e;color:#aaa;transition:.2s}
.tab.active{background:#f7971e;color:#1a1a2e;border-color:#f7971e;font-weight:700}
.tab-content{display:none}
.tab-content.active{display:block}
</style>
</head>
<body>
<h1>🤖 Modelos Basados en Agentes</h1>
<div class="sub">Módulo 6 · Segregación de Schelling — el orden emerge sin que nadie lo pida</div>

<div class="card">
<div class="tabs">
<div class="tab active" onclick="switchTab('sim')">🎮 Simulador</div>
<div class="tab" onclick="switchTab('info')">📖 Teoría</div>
</div>

<div id="tab-sim" class="tab-content active">
<canvas id="canvas" width="600" height="600"></canvas>

<div class="controls">
<label>🟦🟥 Tolerancia: <input type="range" id="tolerance" min="0" max="100" value="30"> <span id="tolLabel">30%</span></label>
<label>Vacíos: <input type="range" id="empties" min="5" max="40" value="15"> <span id="empLabel">15%</span></label>
</div>

<div class="controls">
<button class="btn-play" id="playBtn">▶️ Play</button>
<button class="btn-step" id="stepBtn">⏭️ Paso</button>
<button class="btn-reset" id="resetBtn">🔄 Reiniciar</button>
</div>

<div class="stats">
<div class="stat">Agentes <span id="agentCount">0</span></div>
<div class="stat">Insatisfechos <span id="unhappyCount">0</span></div>
<div class="stat">Segregación <span id="segIndex">0%</span></div>
</div>

<div class="graph-container">
<canvas id="graphCanvas" class="graph-canvas" width="580" height="60"></canvas>
</div>

<div class="info-box">
<strong>🧠 El experimento clave:</strong> Pon tolerancia en <strong>30%</strong> y dale Play. Cada agente acepta hasta un 30% de vecinos <em>diferentes</em>. Nadie quiere segregación total... pero el sistema <strong>termina segregado</strong>. Eso es <strong>emergencia</strong>.<br>
<strong>💡 Dato:</strong> Thomas Schelling ganó el Nobel de Economía en 2005 por este modelo.
</div>
</div>

<div id="tab-info" class="tab-content">
<div class="info-box" style="border-left-color:#3498db">
<p><strong>📐 ¿Qué es un Modelo Basado en Agentes?</strong></p>
<p>Un <strong>MBA</strong> simula sistemas complejos desde lo más simple: agentes individuales con reglas locales. No programas el resultado global — programas el comportamiento de cada agente y observas qué <strong>emerge</strong>.</p>
<br>
<p><strong>🔬 El Modelo de Schelling (1971)</strong></p>
<p>Thomas Schelling creó este modelo con monedas sobre un tablero de ajedrez. Demostró que <strong>preferencias leves</strong> por tener vecinos similares llevan a <strong>segregación extrema</strong> — sin que nadie la desee ni la planee.</p>
<br>
<p><strong>🧩 Los ingredientes:</strong></p>
<ul style="padding-left:20px;line-height:1.8">
<li>🟦 <strong>Agentes azules</strong> (tipo A)</li>
<li>🟥 <strong>Agentes rojos</strong> (tipo B)</li>
<li>⬛ <strong>Espacios vacíos</strong> — donde los insatisfechos se mudan</li>
<li>📏 <strong>Tolerancia</strong> — % máximo de vecinos <em>diferentes</em> que cada agente acepta</li>
</ul>
<br>
<p><strong>⚙️ Regla de cada agente:</strong></p>
<p style="font-style:italic;color:#f7971e;text-align:center;padding:8px;background:#0f0f1a;border-radius:6px">
"Si el % de vecinos diferentes a mí supera mi tolerancia → me mudo a un espacio vacío"
</p>
<br>
<p><strong>📊 ¿Por qué es importante?</strong></p>
<p>Muestra que <strong>problemas sociales masivos</strong> (segregación, burbujas, polarización) pueden surgir de <strong>preferencias individuales inocentes</strong>. Cambiar el comportamiento global requiere cambiar <strong>reglas sistémicas</strong>, no solo convencer a individuos.</p>
<br>
<p><strong>🌍 Más allá de Schelling:</strong> Los MBAs se usan para modelar epidemias, tráfico, mercados financieros, ecosistemas, dinámica de opiniones, inteligencia de enjambres y mucho más.</p>
</div>
</div>
</div>

<script>
const canvas=document.getElementById('canvas');
const ctx=canvas.getContext('2d');
const graphCanvas=document.getElementById('graphCanvas');
const gctx=graphCanvas.getContext('2d');

const SIZE=50;
const CELL=canvas.width/SIZE;
let grid=[];
let running=false;
let intervalId=null;
let stepCount=0;
let historySeg=[];
const MAX_HISTORY=200;

function initGrid(emptyPct, tol){
  grid=[];
  for(let r=0;r<SIZE;r++){
    grid[r]=[];
    for(let c=0;c<SIZE;c++){
      const val=Math.random()*100;
      if(val<emptyPct) grid[r][c]=0;
      else if(val<(100-emptyPct)/2+emptyPct) grid[r][c]=1;
      else grid[r][c]=2;
    }
  }
  stepCount=0;
  historySeg=[];
  if(running) togglePlay();
  draw();
  updateStats(tol);
}

function countNeighbors(r,c,type){
  let same=0,diff=0,empty=0;
  for(let dr=-1;dr<=1;dr++){
    for(let dc=-1;dc<=1;dc++){
      if(dr===0&&dc===0) continue;
      const nr=(r+dr+SIZE)%SIZE;
      const nc=(c+dc+SIZE)%SIZE;
      const v=grid[nr][nc];
      if(v===0) empty++;
      else if(v===type) same++;
      else diff++;
    }
  }
  return {same,diff,empty,total:same+diff+empty};
}

function isHappy(r,c,tol){
  const v=grid[r][c];
  if(v===0) return true;
  const n=countNeighbors(r,c,v);
  if(n.total===0) return false;
  const pctDiff=n.total>0?(n.diff/n.total)*100:0;
  return pctDiff<=tol;
}

function step(tol){
  const unhappy=[];
  for(let r=0;r<SIZE;r++){
    for(let c=0;c<SIZE;c++){
      if(grid[r][c]!==0&&!isHappy(r,c,tol)){
        unhappy.push({r,c});
      }
    }
  }
  if(unhappy.length===0){if(running)togglePlay();draw();updateStats(tol);return;}
  
  const empties=[];
  for(let r=0;r<SIZE;r++)for(let c=0;c<SIZE;c++)if(grid[r][c]===0)empties.push({r,c});
  
  for(const u of unhappy){
    if(empties.length===0) break;
    const ri=Math.floor(Math.random()*empties.length);
    const e=empties.splice(ri,1)[0];
    grid[e.r][e.c]=grid[u.r][u.c];
    grid[u.r][u.c]=0;
  }
  
  stepCount++;
  draw();
  updateStats(tol);
}

function calcSegregation(){
  let totalPairs=0,samePairs=0;
  for(let r=0;r<SIZE;r++){
    for(let c=0;c<SIZE;c++){
      if(grid[r][c]===0)continue;
      for(let dr=-1;dr<=1;dr++){
        for(let dc=-1;dc<=1;dc++){
          if(dr===0&&dc===0) continue;
          const nr=(r+dr+SIZE)%SIZE;
          const nc=(c+dc+SIZE)%SIZE;
          if(grid[nr][nc]!==0&&grid[nr][nc]===grid[r][c]) samePairs++;
          if(grid[nr][nc]!==0) totalPairs++;
        }
      }
    }
  }
  return totalPairs>0?(samePairs/totalPairs)*100:0;
}

function draw(){
  ctx.clearRect(0,0,canvas.width,canvas.height);
  for(let r=0;r<SIZE;r++){
    for(let c=0;c<SIZE;c++){
      const v=grid[r][c];
      if(v===0) ctx.fillStyle='#1a1a2e';
      else if(v===1) ctx.fillStyle='#3498db';
      else ctx.fillStyle='#e74c3c';
      ctx.fillRect(c*CELL+0.5,r*CELL+0.5,CELL-1,CELL-1);
    }
  }
}

function updateStats(tol){
  let agents=0,unhappy=0;
  for(let r=0;r<SIZE;r++){
    for(let c=0;c<SIZE;c++){
      if(grid[r][c]!==0){
        agents++;
        if(!isHappy(r,c,tol)) unhappy++;
      }
    }
  }
  document.getElementById('agentCount').textContent=agents;
  document.getElementById('unhappyCount').textContent=unhappy;
  const seg=calcSegregation();
  document.getElementById('segIndex').textContent=seg.toFixed(1)+'%';
  
  historySeg.push(seg);
  if(historySeg.length>MAX_HISTORY) historySeg.shift();
  drawGraph();
}

function drawGraph(){
  gctx.clearRect(0,0,graphCanvas.width,graphCanvas.height);
  if(historySeg.length<2) return;
  const w=graphCanvas.width,h=graphCanvas.height;
  const maxSeg=100;
  gctx.strokeStyle='#f7971e';
  gctx.lineWidth=2;
  gctx.beginPath();
  for(let i=0;i<historySeg.length;i++){
    const x=(i/(historySeg.length-1))*w;
    const y=h-(historySeg[i]/maxSeg)*h;
    if(i===0) gctx.moveTo(x,y);
    else gctx.lineTo(x,y);
  }
  gctx.stroke();
  
  gctx.fillStyle='#555';
  gctx.font='10px sans-serif';
  gctx.fillText('0%',2,h-2);
  gctx.fillText('50%',w/2-6,h-2);
  gctx.fillText('100%',w-22,h-2);
}

function togglePlay(){
  const tol=parseFloat(document.getElementById('tolerance').value);
  running=!running;
  const btn=document.getElementById('playBtn');
  if(running){
    btn.textContent='⏸️ Pausa';
    btn.className='btn-pause';
    intervalId=setInterval(()=>step(tol),80);
  }else{
    btn.textContent='▶️ Play';
    btn.className='btn-play';
    clearInterval(intervalId);
  }
}

function switchTab(id){
  document.querySelectorAll('.tab').forEach(t=>t.classList.remove('active'));
  document.querySelectorAll('.tab-content').forEach(t=>t.classList.remove('active'));
  event.target.classList.add('active');
  document.getElementById('tab-'+id).classList.add('active');
}

document.getElementById('tolerance').addEventListener('input',function(){
  document.getElementById('tolLabel').textContent=this.value+'%';
  if(!running) draw();
});
document.getElementById('empties').addEventListener('input',function(){
  document.getElementById('empLabel').textContent=this.value+'%';
});
document.getElementById('playBtn').addEventListener('click',togglePlay);
document.getElementById('stepBtn').addEventListener('click',function(){
  if(running) togglePlay();
  const tol=parseFloat(document.getElementById('tolerance').value);
  step(tol);
});
document.getElementById('resetBtn').addEventListener('click',function(){
  if(running) togglePlay();
  const empty=parseFloat(document.getElementById('empties').value);
  const tol=parseFloat(document.getElementById('tolerance').value);
  initGrid(empty,tol);
});

initGrid(15,30);
</script>
</body>
</html>
```

---

## 🗺️ Qué tienes

### 🎮 Pestaña "Simulador"
- **Modelo de Segregación de Schelling** — agentes azules y rojos en un tablero de 50×50
- **Tolerancia**: ajusta qué % de vecinos diferentes tolera un agente antes de mudarse
- **% Vacíos**: qué tan denso está el tablero
- **▶️ Play / ⏭ Paso / 🔄 Reiniciar**
- **Gráfico de segregación** en vivo — la línea naranja sube mientras el sistema se segrega

### 📖 Pestaña "Teoría"
- Explicación de qué son los MBAs
- Cómo funciona Schelling
- Por qué ganó el Nobel con esto
- Aplicaciones reales (epidemias, tráfico, mercados)

---

### 🔬 Experimentos

| Tolerancia | Resultado |
|------------|-----------|
| **50%** | Cada agente acepta hasta la mitad de vecinos diferentes. Aún así... el sistema se segrega ~70-80% |
| **30%** | El punto dulce. Nadie quiere segregación, pero la consiguen |
| **70%** | Alta tolerancia — la segregación es menor pero sigue presente |
| **10%** | Extremo — segregación casi total en pocos pasos |

La lección: **aunque todos sean tolerantes, el sistema puede segregarse**. Eso es **emergencia** — el resultado global no está en las intenciones individuales.
