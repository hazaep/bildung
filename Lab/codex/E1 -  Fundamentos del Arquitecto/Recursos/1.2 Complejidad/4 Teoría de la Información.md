# 📊 Módulo 4: Teoría de la Información

---

## 🔍 El problema de fondo

Hasta ahora hemos visto sistemas complejos, caos y fractales. Pero nos falta algo fundamental:

> **¿Cómo medimos la complejidad?**

No basta con decir "esto es complejo y esto no". Necesitamos una **regla cuantificable**. Ahí entra la **Teoría de la Información**.

---

## 📜 Claude Shannon — el padre de la era digital

En 1948, un ingeniero de los Bell Labs llamado **Claude Shannon** publicó *"A Mathematical Theory of Communication"*. Su pregunta era práctica:

> *"¿Cuánta información puedo mandar por un cable sin perderla?"*

Pero en el camino, sin querer, inventó una **teoría general de la incertidumbre y el conocimiento** que hoy usamos en todo: desde comprimir archivos ZIP hasta entender el cerebro.

---

## 🎲 Entropía de Shannon (H)

La idea genial de Shannon: **la información es sorpresa**.

> Si algo es predecible, no te da información.
> Si algo es inesperado, te da mucha información.

### La fórmula

```
H = - Σ p(i) · log₂(p(i))
```

Donde `p(i)` es la probabilidad de que ocurra el evento `i`.

Suena abstracto. Veámoslo con ejemplos concretos.

### Ejemplo 1: Una moneda justa 🪙

| Evento | Probabilidad |
|--------|------------|
| Águila | 0.5 |
| Sol | 0.5 |

```
H = -(0.5·log₂(0.5) + 0.5·log₂(0.5))
H = -(0.5·(-1) + 0.5·(-1))
H = 1 bit
```

Cada volado te da **exactamente 1 bit de información**. Máxima incertidumbre.

### Ejemplo 2: Una moneda trucada 🎭

| Evento | Probabilidad |
|--------|------------|
| Águila | 0.99 |
| Sol | 0.01 |

```
H = -(0.99·log₂(0.99) + 0.01·log₂(0.01))
H ≈ 0.08 bits
```

Casi no hay sorpresa — casi siempre sale águila. **Poca información.**

### Ejemplo 3: Algo que siempre ocurre

| Evento | Probabilidad |
|--------|------------|
| Águila | 1.0 |
| Sol | 0.0 |

```
H = -(1.0·log₂(1.0) + 0.0·log₂(0.0))
H = 0 bits
```

**Cero información.** No hay sorpresa alguna.

---

## 📏 El espectro de la entropía

```
Entropía baja (0) ←————————————————→ Entropía alta (log₂(n))
   Orden total                          Caos total
   Predecible                           Impredecible
   Sin información                      Máxima información
```

| Sistema | Entropía | Ejemplo |
|---------|----------|---------|
| Cristal | Muy baja | Todos los átomos en su lugar — nada que informar |
| Libro | Media | Tiene estructura, pero no sabes la siguiente palabra |
| Ruido blanco | Máxima | Estática de TV — pura sorpresa, pero sin patrón |

Y aquí viene la paradoja clave...

---

## ⚡ La paradoja de la complejidad

> **El ruido aleatorio tiene la máxima entropía... pero no es complejo.**

Un archivo de ruido blanco tiene entropía altísima, pero no contiene información útil. No hay patrón, no hay estructura, no hay emergencia.

**La complejidad real está en el medio:**

```
                          COMPLEJIDAD
                              🔥
Cristal ←—————————————————|—————————————→ Ruido blanco
Orden total                 │              Caos total
H = 0                       │              H = máxima
                            │
                    Estructura con
                    sorpresa controlada
```

Un sistema **complejo** tiene suficiente entropía para ser impredecible, pero suficiente estructura para tener significado. Una oración en español, una sinfonía de Beethoven, el genoma humano.

---

## 🔧 Complejidad de Kolmogorov

Otra forma de medir complejidad: ¿qué tan largo es el **programa más corto** que puede generar esa secuencia?

| Secuencia | Programa mínimo | Complejidad |
|-----------|----------------|-------------|
| `AAAAAAAAAA` | `print "A"*10` | Baja |
| `ABABABABAB` | `print "AB"*5` | Baja |
| `AABABBABBA...` | El programa más corto es la secuencia misma | Alta |

**La complejidad de Kolmogorov de algo es el tamaño de su descripción más comprimida posible.**

Un fractal tiene **alta complejidad de Kolmogorov** porque aunque la regla sea simple (`Z → Z² + C`), produce una estructura con detalle infinito.

---

## 🧠 Conexión con lo que ya vimos

### + Módulo 1 (Sistemas Complejos)
Los sistemas complejos están en el punto **justo entre orden y caos** — donde la entropía no es máxima ni mínima.

### + Módulo 2 (Caos)
El mapa logístico en régimen caótico (r = 3.9) tiene **alta entropía** pero su **complejidad de Kolmogorov es baja** porque la regla cabe en una línea de código.

### + Módulo 3 (Fractales)
El conjunto de Mandelbrot tiene **información infinita** en su borde (entropía infinita), pero una **descripción de 3 caracteres**: `Z² + C`.

---

## 💎 La gran idea

> **La complejidad no es cuánta información hay, sino cuánta estructura interesante tiene esa información.**

Shannon nos dio la herramienta para medir la incertidumbre. Kolmogorov nos dio la herramienta para medir la compresibilidad. Juntos nos dicen: lo complejo está **entre el orden rígido y el ruido sin sentido**.

```html
<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Módulo 4 — Teoría de la Información</title>
<style>
  * { box-sizing: border-box; margin: 0; padding: 0; }
  body { font-family: system-ui, -apple-system, sans-serif; background: #0a0a0f; color: #e0e0e0; min-height: 100vh; padding: 20px; }
  .container { max-width: 1200px; margin: 0 auto; }
  h1 { font-size: 2.2rem; color: #00d4ff; margin-bottom: 5px; }
  .subtitle { color: #888; margin-bottom: 30px; font-size: 1rem; }
  .grid { display: grid; grid-template-columns: 1fr 1fr; gap: 20px; margin-bottom: 20px; }
  @media (max-width: 768px) { .grid { grid-template-columns: 1fr; } }
  .card { background: #13131f; border-radius: 14px; padding: 20px; border: 1px solid #1e1e30; }
  .card h2 { font-size: 1.2rem; color: #ffd700; margin-bottom: 15px; display: flex; align-items: center; gap: 8px; }
  .card h2 .num { color: #555; font-weight: 400; }
  textarea { width: 100%; height: 120px; background: #0d0d18; border: 1px solid #2a2a40; border-radius: 8px; color: #e0e0e0; padding: 12px; font-size: 1rem; font-family: monospace; resize: vertical; }
  textarea:focus { outline: none; border-color: #00d4ff; }
  .stat-row { display: flex; justify-content: space-between; align-items: center; margin-top: 12px; flex-wrap: wrap; gap: 10px; }
  .entropy-value { font-size: 2rem; font-weight: 700; color: #00d4ff; font-family: monospace; }
  .entropy-unit { font-size: 1rem; color: #888; }
  .max-entropy { font-size: 0.9rem; color: #666; }
  .meter-container { margin-top: 10px; height: 20px; background: #1a1a2e; border-radius: 10px; overflow: hidden; position: relative; }
  .meter-fill { height: 100%; width: 0%; border-radius: 10px; transition: width 0.3s ease; }
  .meter-labels { display: flex; justify-content: space-between; font-size: 0.75rem; color: #666; margin-top: 3px; }
  .spectrum { margin-top: 15px; }
  .spectrum h3 { font-size: 0.9rem; color: #888; margin-bottom: 10px; }
  .spectrum-bar { display: flex; height: 40px; border-radius: 8px; overflow: hidden; cursor: pointer; }
  .spectrum-segment { flex: 1; display: flex; align-items: center; justify-content: center; font-size: 0.65rem; font-weight: 600; transition: opacity 0.2s; }
  .spectrum-segment:hover { opacity: 0.8; }
  .marker-row { position: relative; height: 24px; margin: 8px 0 5px 0; }
  .marker { position: absolute; width: 14px; height: 14px; border-radius: 50%; transform: translateX(-50%); top: 5px; transition: left 0.3s ease; box-shadow: 0 0 8px currentColor; }
  .marker-label { position: absolute; top: 22px; transform: translateX(-50%); font-size: 0.65rem; white-space: nowrap; }
  .btn-group { display: flex; gap: 10px; flex-wrap: wrap; margin-top: 15px; }
  .btn { padding: 10px 20px; border: none; border-radius: 8px; font-size: 0.9rem; cursor: pointer; font-weight: 600; transition: transform 0.1s, opacity 0.2s; }
  .btn:hover { transform: scale(1.03); }
  .btn:active { transform: scale(0.97); }
  .btn-cristal { background: #4a90d9; color: #fff; }
  .btn-complejo { background: #d9a84a; color: #000; }
  .btn-ruido { background: #d94a4a; color: #fff; }
  .examples { margin-top: 15px; }
  .example-tag { display: inline-block; padding: 4px 12px; border-radius: 20px; font-size: 0.8rem; margin: 3px; background: #1e1e30; color: #aaa; cursor: pointer; transition: all 0.2s; border: 1px solid transparent; }
  .example-tag:hover { border-color: #00d4ff; color: #fff; }
  .example-tag.active { border-color: #00d4ff; background: #0a2a3a; color: #00d4ff; }

  .full-width { grid-column: 1 / -1; }
  .universe-display { display: grid; grid-template-columns: 1fr 1fr 1fr; gap: 10px; margin-top: 10px; }
  .universe-box { padding: 12px; border-radius: 8px; text-align: center; font-size: 2rem; min-height: 60px; display: flex; align-items: center; justify-content: center; gap: 8px; font-family: monospace; letter-spacing: 2px; }
  .kolmogorov { margin-top: 15px; }
  .kolmogorov code { display: block; background: #0d0d18; padding: 10px; border-radius: 6px; font-size: 0.85rem; margin: 5px 0; border: 1px solid #1e1e30; }
  .kolmogorov .arrow { color: #ffd700; text-align: center; font-size: 1.2rem; }
  .kolmogorov .output { color: #4a4; }

  .tooltip { position: fixed; bottom: 20px; left: 50%; transform: translateX(-50%); background: #1a2a3a; color: #ddd; padding: 12px 24px; border-radius: 10px; border: 1px solid #2a4a5a; font-size: 0.9rem; opacity: 0; transition: opacity 0.4s; pointer-events: none; z-index: 100; }
  .tooltip.show { opacity: 1; }

  .info-box { background: #0d0d18; border-left: 3px solid #ffd700; padding: 12px 15px; border-radius: 0 8px 8px 0; margin-top: 15px; font-size: 0.85rem; color: #bbb; line-height: 1.5; }
  .info-box strong { color: #ffd700; }
</style>
</head>
<body>
<div class="container">
  <h1>📊 Teoría de la Información</h1>
  <div class="subtitle">Entropía de Shannon · Complejidad de Kolmogorov · El espectro orden–caos</div>

  <div class="grid">
    <!-- Calculadora de Entropía -->
    <div class="card" style="grid-column: 1 / -1;">
      <h2><span class="num">⌨️</span> Calculadora de Entropía</h2>
      <textarea id="inputText" placeholder="Escribe cualquier texto aquí...">Hola mundo</textarea>
      <div class="stat-row">
        <div>
          <span class="entropy-value" id="entropyDisplay">0.00</span>
          <span class="entropy-unit">bits / símbolo</span>
        </div>
        <div>
          <span class="max-entropy" id="maxEntropyDisplay">Máx: 0.00 bits</span>
          <span style="margin-left:15px;color:#888;font-size:0.85rem;" id="charCount">0 caracteres</span>
        </div>
      </div>
      <div class="meter-container">
        <div class="meter-fill" id="meterFill" style="width:0%;"></div>
      </div>
      <div class="meter-labels">
        <span style="color:#4a90d9;">Orden 🔵</span>
        <span style="color:#d9a84a;">Complejidad 🟡</span>
        <span style="color:#d94a4a;">Caos 🔴</span>
      </div>
      <div class="examples">
        <span class="example-tag" data-text="AAAAAAAAAAAAAAAAAAAA">AAAA...</span>
        <span class="example-tag" data-text="ABABABABABABABABABAB">ABAB...</span>
        <span class="example-tag" data-text="Hola mundo">Hola mundo</span>
        <span class="example-tag" data-text="La complejidad emerge de la simplicidad cuando las reglas locales generan comportamientos globales inesperados">Frase completa</span>
        <span class="example-tag" data-text="aB3#kL9$xQ2@mN7&pR5">Ruido simulado</span>
      </div>
    </div>

    <!-- Tres universos -->
    <div class="card">
      <h2><span class="num">🎮</span> Tres universos</h2>
      <div class="universe-display">
        <div class="universe-box" style="background:#0a1a2a;border:1px solid #4a90d9;" id="uniCristal">🧊</div>
        <div class="universe-box" style="background:#1a1a0a;border:1px solid #d9a84a;" id="uniComplejo">🧠</div>
        <div class="universe-box" style="background:#1a0a0a;border:1px solid #d94a4a;" id="uniRuido">📡</div>
      </div>
      <div style="display:flex;justify-content:space-between;font-size:0.7rem;color:#666;margin-top:4px;padding:0 5px;">
        <span>Cristal (H≈0)</span>
        <span>Complejo (H≈3.5)</span>
        <span>Ruido (H≈5)</span>
      </div>
      <div class="btn-group">
        <button class="btn btn-cristal" onclick="setPreset('cristal')">🧊 Cristal</button>
        <button class="btn btn-complejo" onclick="setPreset('complejo')">🧠 Complejo</button>
        <button class="btn btn-ruido" onclick="setPreset('ruido')">📡 Ruido</button>
      </div>
    </div>

    <!-- Complejidad de Kolmogorov -->
    <div class="card">
      <h2><span class="num">🔧</span> Complejidad de Kolmogorov</h2>
      <p style="font-size:0.85rem;color:#888;margin-bottom:10px;">La descripción más corta de una secuencia.</p>
      <div class="kolmogorov">
        <div><strong style="color:#4a9;">Regla:</strong> <code>print("10" * 5)</code></div>
        <div class="arrow">⬇️ 8 caracteres de código</div>
        <div><strong style="color:#4a4;">Salida:</strong> <code class="output" style="color:#4a4;">1010101010</code></div>
        <div style="margin-top:10px;font-size:0.8rem;color:#666;">Kolmogorov baja — el programa es <span style="color:#4a9;">más corto</span> que la salida</div>
      </div>
      <div class="kolmogorov" style="margin-top:10px;">
        <div><strong style="color:#4a9;">Regla (Mandelbrot):</strong> <code>Z → Z² + C</code></div>
        <div class="arrow">⬇️ 3 caracteres... pero genera detalle infinito</div>
        <div style="font-size:0.8rem;color:#666;">Kolmogorov baja (regla simple) pero produce <span style="color:#ffd700;">información infinita</span> — la paradoja de los fractales</div>
      </div>
    </div>

    <!-- Espectro -->
    <div class="card full-width">
      <h2><span class="num">📈</span> Espectro Orden → Complejidad → Caos</h2>
      <div class="spectrum-bar">
        <div class="spectrum-segment" style="background:#1a3050;color:#9bf;" onclick="setPreset('cristal')">Cristal</div>
        <div class="spectrum-segment" style="background:#2a3a20;color:#9d9;" onclick="setPreset('semi1')">Genoma</div>
        <div class="spectrum-segment" style="background:#3a3a10;color:#dd9;" onclick="setPreset('semi2')">Música</div>
        <div class="spectrum-segment" style="background:#4a3a10;color:#d9a84a;" onclick="setPreset('complejo')">🌐 Lenguaje</div>
        <div class="spectrum-segment" style="background:#3a1a10;color:#c97;" onclick="setPreset('semi3')">Tráfico</div>
        <div class="spectrum-segment" style="background:#2a0a0a;color:#d94a4a;" onclick="setPreset('ruido')">📡 Ruido</div>
        <div class="spectrum-segment" style="background:#1a0000;color:#a44;" onclick="setPreset('max')">Caos puro</div>
      </div>
      <div class="marker-row">
        <div class="marker" id="spectrumMarker" style="left:50%;background:#ffd700;color:#ffd700;"></div>
      </div>
      <div class="marker-row" style="margin-top:20px;">
        <div class="marker-label" style="left:5%;color:#4a90d9;">Orden<br><span style="font-size:0.6rem;color:#555;">Predecible</span></div>
        <div class="marker-label" style="left:50%;color:#ffd700;">🧠 Complejidad<br><span style="font-size:0.6rem;color:#555;">Estructura + sorpresa</span></div>
        <div class="marker-label" style="left:95%;color:#d94a4a;">Caos<br><span style="font-size:0.6rem;color:#555;">Sorpresa total</span></div>
      </div>
      <div class="info-box">
        <strong>💡 La paradoja:</strong> El ruido tiene la máxima entropía, pero <strong>no es complejo</strong>. La complejidad real está donde hay <strong>estructura con sorpresa controlada</strong> — justo en el centro del espectro.
      </div>
    </div>
  </div>
</div>

<div class="tooltip" id="tooltip"></div>

<script>
  const inputEl = document.getElementById('inputText');
  const entropyDisplay = document.getElementById('entropyDisplay');
  const maxEntropyDisplay = document.getElementById('maxEntropyDisplay');
  const meterFill = document.getElementById('meterFill');
  const charCount = document.getElementById('charCount');
  const marker = document.getElementById('spectrumMarker');
  const uniCristal = document.getElementById('uniCristal');
  const uniComplejo = document.getElementById('uniComplejo');
  const uniRuido = document.getElementById('uniRuido');

  function calcEntropy(text) {
    if (!text.length) return { h: 0, maxH: 0, freq: {} };
    const freq = {};
    for (const ch of text) freq[ch] = (freq[ch] || 0) + 1;
    const len = text.length;
    let h = 0;
    for (const ch in freq) {
      const p = freq[ch] / len;
      if (p > 0) h -= p * Math.log2(p);
    }
    const maxH = Math.log2(Math.min(Object.keys(freq).length, len));
    return { h, maxH, freq };
  }

  function updateMeter(h, maxH) {
    const pct = maxH > 0 ? (h / maxH) * 100 : 0;
    meterFill.style.width = Math.min(pct, 100) + '%';
    if (pct < 33) {
      meterFill.style.background = 'linear-gradient(90deg, #4a90d9, #5aa0e9)';
    } else if (pct < 66) {
      meterFill.style.background = 'linear-gradient(90deg, #d9a84a, #e9b85a)';
    } else {
      meterFill.style.background = 'linear-gradient(90deg, #d94a4a, #e95a5a)';
    }
  }

  function update() {
    const text = inputEl.value;
    const { h, maxH, freq } = calcEntropy(text);
    entropyDisplay.textContent = h.toFixed(4);
    maxEntropyDisplay.textContent = `Máx: ${maxH.toFixed(2)} bits`;
    charCount.textContent = `${text.length} caracteres, ${Object.keys(freq).length} símbolos únicos`;
    updateMeter(h, maxH);

    const pct = maxH > 0 ? (h / maxH) * 100 : 0;
    marker.style.left = Math.min(Math.max(pct, 2), 98) + '%';
  }

  function showTooltip(msg) {
    const t = document.getElementById('tooltip');
    t.textContent = msg;
    t.classList.add('show');
    clearTimeout(t._hide);
    t._hide = setTimeout(() => t.classList.remove('show'), 2000);
  }

  function setPreset(type) {
    const presets = {
      cristal: {
        text: '🧊 Cristal — Orden perfecto\nEntropía ~0 bits\nTodo es predecible.\nNo hay sorpresa.\nNo hay información.\n',
        uni: '🧊 Orden perfecto',
        msg: 'Cristal: H ≈ 0 — orden total, cero información'
      },
      semi1: {
        text: 'Genoma — Estructura con variación\nATCGATCGATCGATCG\nPatrón con mutaciones.\n',
        uni: '🧬 Genoma',
        msg: 'Genoma: H media-baja — estructura con variación local'
      },
      semi2: {
        text: 'Música — Patrones que sorprenden\nDo Re Mi Fa Sol La Si Do\nRe Mi Fa Sol La Si Do Re\nMi Fa Sol...\n',
        uni: '🎵 Música',
        msg: 'Música: estructura reconocible con sorpresa controlada'
      },
      complejo: {
        text: '🌐 Lenguaje — El punto dulce de la complejidad\nLa teoría de la información nos dice que la complejidad emerge en la frontera entre el orden y el caos, donde hay suficiente estructura para tener significado y suficiente sorpresa para ser interesante.\n',
        uni: '🧠 Lenguaje complejo',
        msg: 'Complejidad: H ~3.5 — estructura con sorpresa, el punto dulce'
      },
      semi3: {
        text: '🚗 Tráfico urbano — Emergencia no deseada\nCoche coche coche freno coche coche\nStop arranca coche coche atasco.\nEmergen sin controlador.\n',
        uni: '🚗 Tráfico',
        msg: 'Tráfico: H media — caos controlado, emergencia de patrones'
      },
      ruido: {
        text: '📡 Ruido — Máxima entropía, mínimo significado\nxK9#mP2$rT5@vN8&wQ1*yH4)zJ7+',
        uni: '📡 Ruido puro',
        msg: 'Ruido: H casi máxima — mucha sorpresa, poca estructura útil'
      },
      max: {
        text: '㏗㏚㏛㏜㏝㏞㏟㏠㏡㏢㏣㏤㏥㏦㏧㏨㏩㏪㏫㏬㏭㏮㏯㏰㏱㏲㏳㏴㏵㏶㏷㏸㏹㏺㏻㏼㏽㏾㏿㐀㐁㐂㐃㐄㐅㐆㐇㐈㐉㐊㐋㐌㐍㐎㐏',
        uni: '🔥 Caos puro',
        msg: 'Caos puro: H máxima — nada compresible, nada significativo'
      }
    };
    const p = presets[type];
    if (!p) return;
    inputEl.value = type === 'cristal' ? p.text : p.text;
    
    if (p.uni) {
      if (type === 'cristal') { uniCristal.textContent = p.uni; uniComplejo.textContent = '🧠'; uniRuido.textContent = '📡'; }
      else if (type === 'complejo') { uniComplejo.textContent = p.uni; uniCristal.textContent = '🧊'; uniRuido.textContent = '📡'; }
      else if (type === 'ruido') { uniRuido.textContent = p.uni; uniCristal.textContent = '🧊'; uniComplejo.textContent = '🧠'; }
      else { uniCristal.textContent = '🧊'; uniComplejo.textContent = '🧠'; uniRuido.textContent = '📡'; }
    }
    
    update();
    showTooltip(p.msg);
  }

  // Example tags
  document.querySelectorAll('.example-tag').forEach(el => {
    el.addEventListener('click', () => {
      document.querySelectorAll('.example-tag').forEach(e => e.classList.remove('active'));
      el.classList.add('active');
      inputEl.value = el.dataset.text;
      uniCristal.textContent = '🧊'; uniComplejo.textContent = '🧠'; uniRuido.textContent = '📡';
      update();
    });
  });

  inputEl.addEventListener('input', () => {
    document.querySelectorAll('.example-tag').forEach(e => e.classList.remove('active'));
    uniCristal.textContent = '🧊'; uniComplejo.textContent = '🧠'; uniRuido.textContent = '📡';
    update();
  });

  update();
</script>
</body>
</html>
```

---

## 🗺️ Lo que tienes

### ⌨️ Calculadora de Entropía
- **Escribe cualquier texto** y la entropía se calcula en tiempo real
- El medidor visual te coloca en el espectro: **orden 🟢 → complejidad 🟡 → caos 🔴**
- Prueba con los **tags de ejemplo** debajo del área de texto

### 🎮 Tres universos interactivos
- **🧊 Cristal** — H ~ 0, orden puro
- **🧠 Complejo** — H ~3.5, el punto dulce
- **📡 Ruido** — H ~5, máxima entropía pero sin significado

### 🔧 Complejidad de Kolmogorov
- Ejemplo visual: regla corta → secuencia larga
- La paradoja del Mandelbrot: 3 caracteres de regla, información infinita

### 📈 Espectro visual
- Barra completa de **Cristal → Genoma → Música → Lenguaje → Tráfico → Ruido → Caos puro**
- Un marcador en vivo que se mueve según tu texto
- Haz **clic en cualquier segmento** para cargar su ejemplo

---

## 🔬 Experimentos clave

| Prueba | Qué observar |
|--------|-------------|
| Escribe `AAAA...` | Entropía ~0, marcador a la izquierda |
| Escribe una frase tuya | La entropía se estabiliza entre 3–4.5 bits |
| Dale a **🧊 Cristal** | Orden perfecto, marcador al inicio |
| Dale a **📡 Ruido** | Máxima entropía, marcador a la derecha... pero sin estructura |
| Dale a **🧠 Complejo** | ¡El punto exacto! Estructura con sorpresa |
