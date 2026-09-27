<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
<title>Hamburguesas Contrarreloj</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Space+Grotesk:wght@500;600;700&family=Inter:wght@400;500;600&display=swap" rel="stylesheet">
<style>
  :root {
    --bg-a: #3a1c0f;
    --bg-b: #241209;
    --bun: #e8b473;
    --cheese: #ffcb47;
    --tomato: #e6483c;
    --lettuce: #7cbf62;
    --ink: #fff6e8;
    --border: rgba(255,246,232,0.16);
    --card: rgba(255,246,232,0.06);
    --panel: #2c150b;
    box-sizing: border-box;
    padding-top: env(safe-area-inset-top, 0px);
    padding-bottom: env(safe-area-inset-bottom, 0px);
  }
  *, *::before, *::after { box-sizing: inherit; }
  html { scroll-padding-top: env(safe-area-inset-top, 0px); }
  html, body { height: 100%; margin: 0; background: var(--bg-b); }
  body {
    font-family: 'Inter', system-ui, sans-serif;
    color: var(--ink);
    background: radial-gradient(120% 90% at 50% 0%, var(--bg-a) 0%, var(--bg-b) 70%);
    min-height: 100%;
    display: flex; flex-direction: column; align-items: center;
    overflow-x: hidden;
  }
  h1, h2, .display { font-family: 'Space Grotesk', sans-serif; }

  .wrap { width: 100%; max-width: 460px; padding: 16px 16px 26px; display: flex; flex-direction: column; gap: 12px; min-height: 100%; }

  header { display: flex; align-items: baseline; justify-content: space-between; gap: 12px; }
  header h1 { font-size: 1.1rem; font-weight: 600; margin: 0; }
  header .subtitle { font-size: 0.7rem; color: rgba(255,246,232,0.55); margin-top: 2px; }
  .peak-count { font-family: 'Space Grotesk', sans-serif; font-size: 0.82rem; color: var(--cheese); white-space: nowrap; }
  .coin-count {
    font-family: 'Space Grotesk', sans-serif; font-size: 0.78rem; color: #ffe08a;
    white-space: nowrap; transition: transform 0.2s;
  }
  .coin-count.pulse { animation: coinPulse 0.4s ease-out; }
  @keyframes coinPulse {
    0% { transform: scale(1); }
    40% { transform: scale(1.3); }
    100% { transform: scale(1); }
  }

  .hud { display: flex; justify-content: space-between; align-items: center; font-size: 0.76rem; color: rgba(255,246,232,0.75); }
  .hud .level-name { color: var(--ink); font-weight: 600; }
  .lives { display: flex; align-items: center; gap: 6px; }
  .lives .lives-label { font-size: 0.68rem; color: rgba(255,246,232,0.5); }
  .life-dot { width: 9px; height: 9px; border-radius: 50%; background: var(--lettuce); box-shadow: 0 0 6px rgba(124,191,98,0.6); }
  .life-dot.lost { background: rgba(255,246,232,0.15); box-shadow: none; }

  .timer-track { position: relative; height: 14px; border-radius: 8px; background: rgba(255,246,232,0.1); overflow: hidden; }
  .timer-fill { height: 100%; width: 100%; background: linear-gradient(90deg, var(--lettuce), var(--cheese)); border-radius: 8px; transition: width 0.12s linear, background 0.3s; }
  .timer-num {
    position: absolute; inset: 0; display: flex; align-items: center; justify-content: center;
    font-size: 0.62rem; font-weight: 600; color: #241209; letter-spacing: 0.02em;
  }

  .stage {
    position: relative;
    width: 100%;
    min-height: 380px;
    border-radius: 18px;
    overflow: hidden;
    background:
      repeating-linear-gradient(45deg, rgba(255,246,232,0.025) 0 10px, transparent 10px 20px),
      var(--panel);
    border: 1px solid var(--border);
    display: flex;
    flex-direction: column;
    padding: 12px;
    gap: 10px;
  }
  .stage.shake { animation: shake 0.3s; }
  @keyframes shake {
    0%, 100% { transform: translateX(0); }
    20% { transform: translateX(-8px); }
    40% { transform: translateX(7px); }
    60% { transform: translateX(-5px); }
    80% { transform: translateX(4px); }
  }

  .awning {
    height: 14px;
    margin: -12px -12px 6px -12px;
    background: repeating-linear-gradient(90deg, var(--tomato) 0 18px, var(--ink) 18px 36px);
    border-bottom: 2px solid rgba(0,0,0,0.3);
    flex-shrink: 0;
  }

  .customer-scene { display: flex; gap: 10px; align-items: flex-end; }
  .customer-face {
    font-size: 2.3rem;
    line-height: 1;
    filter: drop-shadow(0 4px 5px rgba(0,0,0,0.35));
    transition: transform 0.25s ease-out;
    flex-shrink: 0;
  }
  .speech-bubble {
    position: relative;
    flex: 1;
    background: rgba(255,246,232,0.06);
    border: 1px dashed rgba(255,246,232,0.25);
    border-radius: 10px;
    padding: 8px 10px;
    display: flex;
    flex-direction: column;
    gap: 4px;
  }
  .speech-bubble::after {
    content: "";
    position: absolute;
    left: -7px; bottom: 12px;
    width: 0; height: 0;
    border-top: 6px solid transparent;
    border-bottom: 6px solid transparent;
    border-right: 8px solid rgba(255,246,232,0.14);
  }
  .floor-check {
    height: 8px;
    border-radius: 4px;
    flex-shrink: 0;
    background: repeating-linear-gradient(90deg, rgba(255,246,232,0.14) 0 10px, rgba(0,0,0,0.18) 10px 20px);
  }
  .ticket-label { font-size: 0.6rem; letter-spacing: 0.08em; color: var(--cheese); }
  .ticket-row { display: flex; gap: 4px; flex-wrap: wrap; align-items: center; }
  .ticket-item {
    width: 26px; height: 26px; display: flex; align-items: center; justify-content: center;
    font-size: 1.05rem; border-radius: 6px; background: rgba(255,246,232,0.08);
    transition: opacity 0.2s, transform 0.2s;
  }
  .ticket-item.done { opacity: 0.28; transform: scale(0.85); }
  .ticket-item.next { box-shadow: 0 0 0 2px var(--cheese); }

  .build-zone {
    flex: 1;
    display: flex;
    flex-direction: column-reverse;
    align-items: center;
    justify-content: flex-start;
    gap: 2px;
    padding: 6px 0 2px;
    min-height: 150px;
  }
  .plate {
    width: 100px; height: 14px;
    border-radius: 50%;
    background: rgba(255,246,232,0.18);
    box-shadow: 0 0 0 4px rgba(255,246,232,0.06);
    margin-top: 4px;
  }
  .stack-item {
    width: 84px; height: 26px;
    display: flex; align-items: center; justify-content: center;
    font-size: 1.3rem;
    border-radius: 40% 40% 30% 30%;
    background: rgba(255,246,232,0.08);
    animation: popIn 0.22s ease-out;
  }
  @keyframes popIn {
    from { transform: scale(0.4) translateY(10px); opacity: 0; }
    to { transform: scale(1) translateY(0); opacity: 1; }
  }

  .tray {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 8px;
  }
  .ing-btn {
    display: flex; flex-direction: column; align-items: center; justify-content: center;
    gap: 3px;
    padding: 9px 4px;
    border-radius: 12px;
    border: 1px solid var(--border);
    background: rgba(255,246,232,0.05);
    color: var(--ink);
    font-family: 'Inter', sans-serif;
    -webkit-tap-highlight-color: transparent;
  }
  .ing-btn .emo { font-size: 1.4rem; }
  .ing-btn .lbl { font-size: 0.6rem; color: rgba(255,246,232,0.7); text-align: center; line-height: 1.1; }
  .ing-btn:active:not(:disabled) { background: rgba(255,246,232,0.14); transform: scale(0.97); }
  .ing-btn:disabled { opacity: 0.25; }
  .ing-btn.wrong-flash { animation: wrongFlash 0.35s; }
  @keyframes wrongFlash {
    0%, 100% { background: rgba(255,246,232,0.05); }
    40% { background: rgba(230,72,60,0.35); border-color: var(--tomato); }
  }

  .msg-banner { text-align: center; font-size: 0.76rem; color: rgba(255,246,232,0.6); min-height: 1.2em; }

  .overlay {
    position: absolute; inset: 0;
    background: rgba(36,18,9,0.95);
    backdrop-filter: blur(2px);
    display: flex; flex-direction: column; align-items: center; justify-content: center;
    padding: 22px; text-align: center; gap: 14px; z-index: 10;
  }
  .overlay.hidden { display: none; }
  .overlay h2 { font-size: 1.1rem; margin: 0; }
  .overlay p { font-size: 0.84rem; color: rgba(255,246,232,0.72); margin: 0; line-height: 1.5; }
  .primary-btn {
    padding: 12px 26px; border-radius: 30px; border: none;
    background: var(--cheese); color: #241209;
    font-family: 'Space Grotesk', sans-serif; font-weight: 600; font-size: 0.9rem; cursor: pointer;
  }
  .primary-btn:active { transform: scale(0.98); }

  .question-card { width: 100%; text-align: left; display: flex; flex-direction: column; gap: 12px; }
  .question-tag { font-size: 0.64rem; letter-spacing: 0.05em; color: var(--cheese); }
  .question-text { font-size: 0.94rem; color: var(--ink); line-height: 1.5; }
  .options { display: flex; flex-direction: column; gap: 8px; }
  .option-btn {
    text-align: left; padding: 11px 14px; border-radius: 10px; border: 1px solid var(--border);
    background: rgba(255,246,232,0.05); color: var(--ink);
    font-family: 'Inter', sans-serif; font-size: 0.85rem; cursor: pointer;
  }
  .option-btn:active { background: rgba(255,246,232,0.11); }
  .option-btn.correct { border-color: var(--lettuce); background: rgba(124,191,98,0.18); }
  .option-btn.wrong { border-color: var(--tomato); background: rgba(230,72,60,0.18); }
  .option-btn:disabled { cursor: default; }
  .feedback { font-size: 0.8rem; line-height: 1.5; color: rgba(255,246,232,0.75); min-height: 1.2em; }

  footer { text-align: center; font-size: 0.66rem; color: rgba(255,246,232,0.35); }

  @media (max-width: 360px) { header h1 { font-size: 1rem; } }
</style>
</head>
<body>
<div class="wrap">

  <header>
    <div>
      <h1>Hamburguesas Contrarreloj</h1>
      <div class="subtitle">Arma el pedido en orden antes de que se acabe el tiempo</div>
    </div>
    <div style="display:flex; flex-direction:column; align-items:flex-end; gap:2px;">
      <div class="peak-count" id="peakCount">Nivel 1 / 10</div>
      <div class="coin-count" id="coinCount">🪙 0</div>
    </div>
  </header>

  <div class="hud">
    <div class="level-name" id="levelName">Nivel 1</div>
    <div class="lives">
      <span class="lives-label">Errores</span>
      <div id="lives" style="display:flex; gap:5px;"></div>
    </div>
  </div>

  <div class="timer-track">
    <div class="timer-fill" id="timerFill"></div>
    <div class="timer-num" id="timerNum">0.0 s</div>
  </div>

  <div class="stage" id="stage">
    <div class="awning"></div>

    <div class="customer-scene">
      <div class="customer-face" id="customerFace">😊</div>
      <div class="speech-bubble">
        <div class="ticket-label">¡Quiero esto, y rápido!</div>
        <div class="ticket-row" id="ticketRow"></div>
      </div>
    </div>

    <div class="build-zone" id="buildZone">
      <div class="plate"></div>
    </div>

    <div class="floor-check"></div>

    <div class="tray" id="tray"></div>

    <div class="overlay" id="startOverlay">
      <h2>Hamburguesas Contrarreloj</h2>
      <p>Toca los ingredientes en el orden del pedido, de la base a la tapa, antes de que el tiempo llegue a cero. Cada nivel trae hamburguesas más largas, menos tiempo y ejercicios de optimización y multiplicadores de Lagrange cada vez más difíciles. Ganas monedas por cada hamburguesa y por cada ejercicio bien resuelto.</p>
      <button class="primary-btn" id="startBtn">Empezar nivel 1</button>
    </div>

    <div class="overlay hidden" id="questionOverlay">
      <div class="question-card">
        <div class="question-tag" id="questionTag">EJERCICIO · NIVEL 1</div>
        <div class="question-text" id="questionText">Cargando pregunta…</div>
        <div class="options" id="optionsList"></div>
        <div class="feedback" id="feedback"></div>
      </div>
      <button class="primary-btn hidden" id="continueBtn">Continuar</button>
    </div>

    <div class="overlay hidden" id="winOverlay">
      <h2>¡Cocina dominada!</h2>
      <p>Completaste los 10 niveles de Hamburguesas Contrarreloj. Armas pedidos y resuelves cálculo en varias variables sin despeinarte.</p>
      <button class="primary-btn" id="restartBtn">Jugar de nuevo</button>
    </div>
  </div>

  <div class="msg-banner" id="msgBanner">Toca los ingredientes en el orden exacto del pedido de arriba.</div>

  <footer>Cada nivel, lo logres o falles, trae un ejercicio de derivadas parciales o Lagrange.</footer>
</div>

<script>
(function () {
  "use strict";

  // ---------- Question bank ----------
  const QUESTIONS = {
    1: [
      { q: "Para f(x,y) = x² + y² − 6x + 2y + 3, el punto crítico es:", options: ["(3, −1)", "(6, −2)", "(−3, 1)", "(3, 1)"], correct: 0 },
      { q: "Para f(x,y) = 2x² + y² + 4x − 2y, el punto crítico es:", options: ["(−1, 1)", "(1, −1)", "(−2, 2)", "(2, −1)"], correct: 0 },
      { q: "Para f(x,y) = x² + 4y² − 2x − 8y, el punto crítico es:", options: ["(1, 1)", "(2, 2)", "(1, 2)", "(2, 1)"], correct: 0 }
    ],
    2: [
      { q: "Para f(x,y) = x² + xy + y² + 3x, el punto crítico es (−2, 1). Con fxx=2, fyy=2, fxy=1, D = fxxfyy − fxy² = 3 > 0 y fxx > 0. Ese punto es un:", options: ["mínimo", "máximo", "punto de silla", "indeterminado"], correct: 0 },
      { q: "Para f(x,y) = x² − xy + y² en (0,0): fxx=2, fyy=2, fxy=−1, así D = 4 − 1 = 3 > 0. El punto (0,0) es un:", options: ["mínimo", "máximo", "punto de silla", "indeterminado"], correct: 0 },
      { q: "Para f(x,y) = xy en (0,0): fxx=0, fyy=0, fxy=1, así D = 0 − 1 = −1 < 0. El punto (0,0) es un:", options: ["punto de silla", "mínimo", "máximo", "no existe"], correct: 0 }
    ],
    3: [
      { q: "Para f(x,y) = x³ − 3x + y² − 4y, resolviendo ∂f/∂x=0 y ∂f/∂y=0 se obtienen los puntos críticos:", options: ["(1, 2) y (−1, 2)", "(1, 2) y (1, −2)", "solo (1, 2)", "(3, 4) y (−3, 4)"], correct: 0 },
      { q: "Para la misma f, en (1, 2): fxx=6, fyy=2, fxy=0, así D = 12 > 0 con fxx > 0. Ese punto es un:", options: ["mínimo", "máximo", "punto de silla", "indeterminado"], correct: 0 },
      { q: "Para la misma f, en (−1, 2): fxx=−6, fyy=2, fxy=0, así D = −12 < 0. Ese punto es un:", options: ["punto de silla", "mínimo", "máximo", "indeterminado"], correct: 0 }
    ],
    4: [
      { q: "Para maximizar f(x,y) sujeto a g(x,y) = c, la condición de Lagrange es:", options: ["∇f = λ∇g", "∇f = ∇g", "f = λg", "∇f + ∇g = 0"], correct: 0 },
      { q: "Para f(x,y) = x² + y² sujeto a xy = 1, la ecuación ∂f/∂x = λ∂g/∂x da:", options: ["2x = λy", "x = λy", "2x = λ", "x² = λy"], correct: 0 },
      { q: "Para f(x,y) = x + y sujeto a x² + y² = 1, la ecuación ∂f/∂y = λ∂g/∂y da:", options: ["1 = 2λy", "1 = λy", "y = 2λ", "1 = λ"], correct: 0 }
    ],
    5: [
      { q: "Minimiza f(x,y) = x² + 2y² sujeto a x + y = 3. El sistema de Lagrange da x = 2y. ¿Cuál es el punto crítico?", options: ["(2, 1)", "(1, 2)", "(3, 0)", "(1.5, 1.5)"], correct: 0 },
      { q: "Maximiza f(x,y) = 3x + 4y sujeto a x² + y² = 25. ¿Cuál es el punto crítico (con valores positivos)?", options: ["(3, 4)", "(4, 3)", "(5, 0)", "(0, 5)"], correct: 0 },
      { q: "Minimiza f(x,y) = x² + y² sujeto a 2x + y = 5. El sistema da x = 2y. ¿Cuál es el punto crítico?", options: ["(2, 1)", "(1, 2)", "(2.5, 0)", "(0, 5)"], correct: 0 }
    ],
    6: [
      { q: "Siguiendo el ejercicio de minimizar f(x,y)=x²+2y² sujeto a x+y=3 con punto crítico (2,1), el valor mínimo de f es:", options: ["6", "5", "9", "3"], correct: 0 },
      { q: "Siguiendo el ejercicio de maximizar f(x,y)=3x+4y sujeto a x²+y²=25 con punto crítico (3,4), el valor máximo de f es:", options: ["25", "20", "15", "7"], correct: 0 },
      { q: "Siguiendo el ejercicio de minimizar f(x,y)=x²+y² sujeto a 2x+y=5 con punto crítico (2,1), el valor mínimo de f es:", options: ["5", "4", "10", "2"], correct: 0 }
    ],
    7: [
      { q: "Minimiza f(x,y) = x² + y² sujeto a xy = 9 (x,y > 0). El sistema de Lagrange da x = y. ¿Cuál es el punto crítico?", options: ["(3, 3)", "(9, 1)", "(1, 9)", "(4.5, 2)"], correct: 0 },
      { q: "Siguiendo el ejercicio anterior, el valor mínimo de f = x² + y² en (3,3) es:", options: ["18", "9", "36", "6"], correct: 0 },
      { q: "Maximiza f(x,y) = xy sujeto a x² + y² = 8. El sistema da x = y, y el punto crítico (positivo) es (2,2). El valor máximo de f es:", options: ["4", "8", "2", "16"], correct: 0 }
    ],
    8: [
      { q: "Maximiza f(x,y,z) = xyz sujeto a x + y + z = 15. El sistema de Lagrange da x = y = z. ¿Cuál es el punto crítico?", options: ["(5, 5, 5)", "(15, 0, 0)", "(3, 6, 6)", "(7, 4, 4)"], correct: 0 },
      { q: "Siguiendo el ejercicio anterior, el valor máximo de f = xyz en (5,5,5) es:", options: ["125", "75", "15", "225"], correct: 0 },
      { q: "Minimiza f(x,y,z) = x² + y² + z² sujeto a 2x + y + 2z = 9. Resolviendo el sistema de Lagrange, el punto crítico es:", options: ["(2, 1, 2)", "(1, 2, 1)", "(3, 3, 0)", "(2, 2, 1)"], correct: 0 }
    ],
    9: [
      { q: "Maximiza f(x,y,z) = 2x + y + 2z sujeto a x² + y² + z² = 9. El punto crítico (proporcional a (2,1,2)) es:", options: ["(2, 1, 2)", "(1, 2, 2)", "(2, 2, 1)", "(3, 1.5, 3)"], correct: 0 },
      { q: "Siguiendo el ejercicio anterior, el valor máximo de f = 2x + y + 2z en (2,1,2) es:", options: ["9", "6", "12", "5"], correct: 0 },
      { q: "Minimiza f(x,y,z) = x² + y² + z² sujeto a x − y + 2z = 6. Resolviendo el sistema de Lagrange, el punto crítico es:", options: ["(1, −1, 2)", "(2, −2, 1)", "(1, 1, 2)", "(2, −1, 2)"], correct: 0 }
    ],
    10: [
      { q: "Maximiza f(x,y) = x²y sujeto a x + y = 9 (x,y > 0). El sistema de Lagrange da x = 2y. ¿Cuál es el punto crítico?", options: ["(6, 3)", "(3, 6)", "(4.5, 4.5)", "(9, 0)"], correct: 0 },
      { q: "Siguiendo el ejercicio anterior, el valor máximo de f = x²y en (6,3) es:", options: ["108", "54", "81", "216"], correct: 0 },
      { q: "Con dos restricciones simultáneas x+y+z=10 y x−z=2, al restar la segunda de la primera se obtiene:", options: ["y + 2z = 8", "y − 2z = 8", "2y + z = 8", "y + z = 8"], correct: 0 }
    ]
  };

  // ---------- Ingredients ----------
  const BUN_BOTTOM = { id: 'bunBottom', emo: '🍞', lbl: 'Pan base' };
  const BUN_TOP    = { id: 'bunTop',    emo: '🍞', lbl: 'Pan tapa' };
  const MIDDLES = [
    { id: 'lettuce', emo: '🥬', lbl: 'Lechuga' },
    { id: 'tomato',  emo: '🍅', lbl: 'Tomate' },
    { id: 'cheese',  emo: '🧀', lbl: 'Queso' },
    { id: 'patty',   emo: '🥩', lbl: 'Carne' },
    { id: 'bacon',   emo: '🥓', lbl: 'Tocino' },
    { id: 'onion',   emo: '🧅', lbl: 'Cebolla' },
    { id: 'pickle',  emo: '🥒', lbl: 'Pepinillo' }
  ];

  // ---------- Level config ----------
  const LEVEL_TIMES = [10, 9, 8, 7, 6.2, 5.5, 5, 4.5, 4, 3.5];
  const LEVELS = [1,2,3,4,5,6,7,8,9,10].map((n, i) => ({
    middleCount: Math.min(n + 1, MIDDLES.length),
    time: LEVEL_TIMES[i]
  }));
  const LIVES_PER_LEVEL = 3;

  // ---------- State ----------
  let level = 1;
  let lives = LIVES_PER_LEVEL;
  let recipe = [];
  let nextIndex = 0;
  let timeLeft = 0;
  let timeTotal = 0;
  let timerId = null;
  let running = false;
  let usedQuestionIdx = {};
  let pendingAfterQuestion = null;
  let coins = 0;

  // ---------- DOM ----------
  const stage = document.getElementById('stage');
  const ticketRow = document.getElementById('ticketRow');
  const buildZone = document.getElementById('buildZone');
  const tray = document.getElementById('tray');
  const startOverlay = document.getElementById('startOverlay');
  const questionOverlay = document.getElementById('questionOverlay');
  const winOverlay = document.getElementById('winOverlay');
  const startBtn = document.getElementById('startBtn');
  const restartBtn = document.getElementById('restartBtn');
  const continueBtn = document.getElementById('continueBtn');
  const questionTag = document.getElementById('questionTag');
  const questionText = document.getElementById('questionText');
  const optionsList = document.getElementById('optionsList');
  const feedback = document.getElementById('feedback');
  const levelNameEl = document.getElementById('levelName');
  const peakCountEl = document.getElementById('peakCount');
  const livesEl = document.getElementById('lives');
  const timerFill = document.getElementById('timerFill');
  const timerNum = document.getElementById('timerNum');
  const customerFace = document.getElementById('customerFace');
  const coinCountEl = document.getElementById('coinCount');

  function renderCoins(delta) {
    coinCountEl.textContent = '🪙 ' + coins;
    coinCountEl.classList.remove('pulse');
    void coinCountEl.offsetWidth;
    coinCountEl.classList.add('pulse');
  }
  const msgBanner = document.getElementById('msgBanner');

  function renderHud() {
    levelNameEl.textContent = "Nivel " + level;
    peakCountEl.textContent = "Nivel " + level + " / 10";
    livesEl.innerHTML = "";
    for (let i = 0; i < LIVES_PER_LEVEL; i++) {
      const dot = document.createElement('div');
      dot.className = 'life-dot' + (i < (LIVES_PER_LEVEL - lives) ? ' lost' : '');
      livesEl.appendChild(dot);
    }
  }

  function shuffle(arr) {
    const a = arr.slice();
    for (let i = a.length - 1; i > 0; i--) {
      const j = Math.floor(Math.random() * (i + 1));
      [a[i], a[j]] = [a[j], a[i]];
    }
    return a;
  }

  function buildRecipe(cfg) {
    const chosen = shuffle(MIDDLES).slice(0, cfg.middleCount);
    return [BUN_BOTTOM, ...chosen, BUN_TOP];
  }

  function renderTicket() {
    ticketRow.innerHTML = "";
    recipe.forEach((ing, i) => {
      const el = document.createElement('div');
      el.className = 'ticket-item' + (i < nextIndex ? ' done' : (i === nextIndex ? ' next' : ''));
      el.textContent = ing.emo;
      ticketRow.appendChild(el);
    });
  }

  function renderBuildZone() {
    buildZone.querySelectorAll('.stack-item').forEach(n => n.remove());
    const plate = buildZone.querySelector('.plate');
    for (let i = 0; i < nextIndex; i++) {
      const el = document.createElement('div');
      el.className = 'stack-item';
      el.textContent = recipe[i].emo;
      buildZone.insertBefore(el, plate.nextSibling);
    }
  }

  function renderTray() {
    tray.innerHTML = "";
    const order = shuffle(recipe);
    order.forEach(ing => {
      const btn = document.createElement('button');
      btn.className = 'ing-btn';
      btn.dataset.id = ing.id;
      btn.innerHTML = '<span class="emo">' + ing.emo + '</span><span class="lbl">' + ing.lbl + '</span>';
      btn.addEventListener('click', () => onIngredientTap(ing, btn));
      tray.appendChild(btn);
    });
  }

  function onIngredientTap(ing, btn) {
    if (!running) return;
    if (btn.disabled) return;
    if (recipe[nextIndex] && ing.id === recipe[nextIndex].id) {
      btn.disabled = true;
      nextIndex++;
      renderTicket();
      renderBuildZone();
      if (nextIndex >= recipe.length) {
        coins += 50;
        renderCoins();
        msgBanner.textContent = "¡Pedido listo! +50 monedas por la hamburguesa.";
        endAttempt(true);
      }
    } else {
      btn.classList.remove('wrong-flash');
      void btn.offsetWidth;
      btn.classList.add('wrong-flash');
      stage.classList.remove('shake');
      void stage.offsetWidth;
      stage.classList.add('shake');
      lives -= 1;
      renderHud();
      if (lives <= 0) {
        endAttempt(false, 'mistakes');
      }
    }
  }

  function startLevel(n) {
    level = n;
    lives = LIVES_PER_LEVEL;
    const cfg = LEVELS[n - 1];
    recipe = buildRecipe(cfg);
    nextIndex = 0;
    timeTotal = cfg.time;
    timeLeft = cfg.time;
    renderHud();
    renderTicket();
    renderBuildZone();
    renderTray();
    updateTimerBar();
    msgBanner.textContent = "Nivel " + n + " · " + cfg.time.toFixed(1) + " s para armar el pedido";
    running = true;
    if (timerId) clearInterval(timerId);
    timerId = setInterval(tick, 100);
  }

  function retryLevel() {
    startLevel(level);
    msgBanner.textContent = "Reintentando nivel " + level;
  }

  function tick() {
    if (!running) return;
    timeLeft -= 0.1;
    if (timeLeft <= 0) {
      timeLeft = 0;
      updateTimerBar();
      endAttempt(false, 'time');
      return;
    }
    updateTimerBar();
  }

  function updateTimerBar() {
    const ratio = Math.max(0, timeLeft / timeTotal);
    const pct = ratio * 100;
    timerFill.style.width = pct + '%';
    timerNum.textContent = timeLeft.toFixed(1) + ' s';
    if (pct < 25) {
      timerFill.style.background = 'linear-gradient(90deg, var(--tomato), #ff8a65)';
    } else if (pct < 55) {
      timerFill.style.background = 'linear-gradient(90deg, var(--cheese), #ffe08a)';
    } else {
      timerFill.style.background = 'linear-gradient(90deg, var(--lettuce), var(--cheese))';
    }
    updateCustomerFace(ratio);
  }

  function updateCustomerFace(ratio) {
    let emo;
    if (ratio > 0.55) emo = '😊';
    else if (ratio > 0.3) emo = '😐';
    else if (ratio > 0.12) emo = '😠';
    else emo = '😡';
    customerFace.textContent = emo;
    customerFace.style.transform = ratio < 0.3 ? 'scale(1.18)' : 'scale(1)';
  }

  function endAttempt(success, reason) {
    running = false;
    if (timerId) clearInterval(timerId);
    tray.querySelectorAll('.ing-btn').forEach(b => b.disabled = true);

    if (success) {
      msgBanner.textContent = "¡Pedido listo! A resolver el ejercicio.";
      if (level >= 10) {
        pendingAfterQuestion = null;
        openQuestion('pass', () => { showOverlay(winOverlay); });
      } else {
        const nextLevel = level + 1;
        pendingAfterQuestion = () => startLevel(nextLevel);
        openQuestion('pass');
      }
    } else {
      msgBanner.textContent = reason === 'time' ? "¡Se acabó el tiempo!" : "Demasiados ingredientes equivocados.";
      pendingAfterQuestion = () => retryLevel();
      openQuestion('fail');
    }
  }

  function pickQuestion(lvl) {
    const pool = QUESTIONS[lvl] || QUESTIONS[1];
    if (!usedQuestionIdx[lvl]) usedQuestionIdx[lvl] = [];
    let available = pool.map((_, i) => i).filter(i => !usedQuestionIdx[lvl].includes(i));
    if (available.length === 0) {
      usedQuestionIdx[lvl] = [];
      available = pool.map((_, i) => i);
    }
    const idx = available[Math.floor(Math.random() * available.length)];
    usedQuestionIdx[lvl].push(idx);
    return pool[idx];
  }

  function openQuestion(mode, overrideContinue) {
    const question = pickQuestion(level);
    questionTag.textContent = (mode === 'pass' ? "PEDIDO LISTO · NIVEL " + level : "COCINA EN APUROS · NIVEL " + level);
    questionText.textContent = question.q;
    optionsList.innerHTML = "";
    feedback.textContent = "";
    continueBtn.classList.add('hidden');
    continueBtn.textContent = (mode === 'pass')
      ? (level >= 10 ? "Ver resultado final" : "Ir al nivel " + (level + 1))
      : "Reintentar nivel " + level;

    const shuffled = question.options.map((text, i) => ({ text, isCorrect: i === question.correct }));
    for (let i = shuffled.length - 1; i > 0; i--) {
      const j = Math.floor(Math.random() * (i + 1));
      [shuffled[i], shuffled[j]] = [shuffled[j], shuffled[i]];
    }

    shuffled.forEach(opt => {
      const btn = document.createElement('button');
      btn.className = 'option-btn';
      btn.textContent = opt.text;
      btn.addEventListener('click', () => {
        const allBtns = optionsList.querySelectorAll('.option-btn');
        allBtns.forEach(b => b.disabled = true);
        if (opt.isCorrect) {
          btn.classList.add('correct');
          coins += 100;
          renderCoins();
          feedback.textContent = (mode === 'pass'
            ? "Correcto. Buen trabajo en la cocina y en el cálculo."
            : "Correcto. A reintentar el nivel " + level + ".") + " +100 monedas.";
        } else {
          btn.classList.add('wrong');
          coins = Math.max(0, coins - 50);
          renderCoins();
          allBtns.forEach(b => {
            if (b.textContent === question.options[question.correct]) b.classList.add('correct');
          });
          feedback.textContent = "No era esa. La respuesta correcta está marcada arriba. −50 monedas.";
        }
        continueBtn.classList.remove('hidden');
      });
      optionsList.appendChild(btn);
    });

    if (overrideContinue) pendingAfterQuestion = overrideContinue;
    showOverlay(questionOverlay);
  }

  function showOverlay(el) {
    [startOverlay, questionOverlay, winOverlay].forEach(o => o.classList.add('hidden'));
    el.classList.remove('hidden');
  }
  function hideOverlays() {
    [startOverlay, questionOverlay, winOverlay].forEach(o => o.classList.add('hidden'));
  }

  // ---------- Buttons ----------
  startBtn.addEventListener('click', () => { hideOverlays(); startLevel(1); });
  restartBtn.addEventListener('click', () => { hideOverlays(); coins = 0; renderCoins(); startLevel(1); });
  continueBtn.addEventListener('click', () => {
    hideOverlays();
    const fn = pendingAfterQuestion;
    pendingAfterQuestion = null;
    if (fn) fn();
  });

  renderHud();
})();
</script>
</body>
</html>