<!DOCTYPE html>
<html lang="pt">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Química Interativa — Lopes Sebastião Neto</title>
<link href="https://fonts.googleapis.com/css2?family=Playfair+Display:ital,wght@0,400;0,700;1,400&family=DM+Mono:wght@300;400;500&family=Cormorant+Garamond:ital,wght@0,300;0,600;1,300&display=swap" rel="stylesheet">
<style>
  :root {
    --bg: #0a0d12;
    --surface: #111520;
    --card: #161b28;
    --border: #1e2740;
    --gold: #c9a84c;
    --gold-light: #e8cc85;
    --aqua: #3dd6c8;
    --aqua-dim: #1a6b65;
    --red: #e05c5c;
    --purple: #a06cdd;
    --text: #dde3f0;
    --muted: #6b7899;
    --glow: rgba(61,214,200,0.15);
  }

  *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

  body {
    background: var(--bg);
    color: var(--text);
    font-family: 'DM Mono', monospace;
    min-height: 100vh;
    overflow-x: hidden;
  }

  /* ── BACKGROUND GRID ── */
  body::before {
    content: '';
    position: fixed;
    inset: 0;
    background-image:
      linear-gradient(rgba(61,214,200,0.03) 1px, transparent 1px),
      linear-gradient(90deg, rgba(61,214,200,0.03) 1px, transparent 1px);
    background-size: 40px 40px;
    pointer-events: none;
    z-index: 0;
  }

  /* ── HEADER ── */
  header {
    position: relative;
    z-index: 10;
    padding: 64px 48px 48px;
    border-bottom: 1px solid var(--border);
    display: flex;
    align-items: flex-end;
    justify-content: space-between;
    flex-wrap: wrap;
    gap: 24px;
  }

  .header-left h1 {
    font-family: 'Playfair Display', serif;
    font-size: clamp(2rem, 5vw, 3.5rem);
    font-weight: 700;
    color: var(--gold-light);
    line-height: 1.1;
    letter-spacing: -0.02em;
  }

  .header-left h1 span {
    font-style: italic;
    color: var(--aqua);
  }

  .header-left p {
    margin-top: 10px;
    color: var(--muted);
    font-size: 0.78rem;
    letter-spacing: 0.12em;
    text-transform: uppercase;
  }

  .header-right {
    display: flex;
    align-items: center;
    gap: 16px;
  }

  .badge {
    padding: 6px 14px;
    border: 1px solid var(--border);
    border-radius: 2px;
    font-size: 0.7rem;
    letter-spacing: 0.1em;
    text-transform: uppercase;
    color: var(--muted);
  }

  .badge.active {
    border-color: var(--aqua-dim);
    color: var(--aqua);
    background: rgba(61,214,200,0.05);
  }

  /* ── TABELA PERIÓDICA MINI ── */
  .periodic-strip {
    position: relative;
    z-index: 10;
    display: flex;
    gap: 6px;
    padding: 20px 48px;
    overflow-x: auto;
    border-bottom: 1px solid var(--border);
    background: linear-gradient(to right, var(--surface), transparent 30%, transparent 70%, var(--surface));
  }

  .periodic-strip::-webkit-scrollbar { height: 3px; }
  .periodic-strip::-webkit-scrollbar-thumb { background: var(--border); }

  .elem {
    min-width: 52px;
    height: 60px;
    border: 1px solid var(--border);
    border-radius: 3px;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    cursor: pointer;
    transition: all 0.2s;
    flex-shrink: 0;
    background: var(--card);
    position: relative;
    overflow: hidden;
  }

  .elem::after {
    content: '';
    position: absolute;
    inset: 0;
    background: var(--elem-color, var(--aqua));
    opacity: 0;
    transition: opacity 0.2s;
  }

  .elem:hover::after { opacity: 0.12; }
  .elem:hover { border-color: var(--elem-color, var(--aqua)); transform: translateY(-3px); }

  .elem .num { font-size: 0.58rem; color: var(--muted); }
  .elem .sym { font-size: 1.1rem; font-weight: 500; color: var(--text); position: relative; z-index: 1; }
  .elem .name { font-size: 0.5rem; color: var(--muted); position: relative; z-index: 1; }

  /* ── MAIN SECTIONS ── */
  .main {
    position: relative;
    z-index: 10;
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 1px;
    background: var(--border);
    border-bottom: 1px solid var(--border);
  }

  @media (max-width: 768px) {
    .main { grid-template-columns: 1fr; }
    header { padding: 40px 24px 32px; }
    .periodic-strip { padding: 16px 24px; }
    .section { padding: 32px 24px; }
  }

  .section {
    background: var(--bg);
    padding: 48px;
  }

  .section-title {
    font-family: 'Cormorant Garamond', serif;
    font-size: 0.75rem;
    letter-spacing: 0.18em;
    text-transform: uppercase;
    color: var(--aqua);
    margin-bottom: 28px;
    display: flex;
    align-items: center;
    gap: 10px;
  }

  .section-title::after {
    content: '';
    flex: 1;
    height: 1px;
    background: var(--border);
  }

  /* ── CALCULADORA MOLAR ── */
  .calc-input-group {
    display: flex;
    gap: 8px;
    margin-bottom: 16px;
  }

  .calc-input {
    flex: 1;
    background: var(--card);
    border: 1px solid var(--border);
    color: var(--text);
    padding: 12px 16px;
    font-family: 'DM Mono', monospace;
    font-size: 0.85rem;
    border-radius: 2px;
    outline: none;
    transition: border-color 0.2s;
  }

  .calc-input:focus { border-color: var(--aqua); }
  .calc-input::placeholder { color: var(--muted); }

  .btn {
    background: transparent;
    border: 1px solid var(--aqua-dim);
    color: var(--aqua);
    padding: 12px 22px;
    font-family: 'DM Mono', monospace;
    font-size: 0.8rem;
    letter-spacing: 0.08em;
    cursor: pointer;
    border-radius: 2px;
    transition: all 0.2s;
  }

  .btn:hover {
    background: rgba(61,214,200,0.1);
    border-color: var(--aqua);
  }

  .result-box {
    background: var(--card);
    border: 1px solid var(--border);
    border-radius: 2px;
    padding: 20px;
    min-height: 80px;
    font-size: 0.82rem;
    line-height: 1.8;
  }

  .result-value {
    font-family: 'Playfair Display', serif;
    font-size: 1.8rem;
    color: var(--gold-light);
    display: block;
    margin-bottom: 4px;
  }

  .result-label { color: var(--muted); font-size: 0.72rem; }

  /* ── EQUILÍBRIO QUÍMICO ── */
  .reaction-display {
    background: var(--card);
    border: 1px solid var(--border);
    border-radius: 2px;
    padding: 24px;
    margin-bottom: 16px;
    font-family: 'Playfair Display', serif;
    font-size: 1.1rem;
    text-align: center;
    letter-spacing: 0.05em;
    color: var(--text);
  }

  .reaction-display sup { font-size: 0.6em; }

  .eq-inputs {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 8px;
    margin-bottom: 12px;
  }

  .eq-label {
    font-size: 0.7rem;
    color: var(--muted);
    margin-bottom: 4px;
    letter-spacing: 0.06em;
  }

  /* ── TABELA SOLUBILIDADE ── */
  .solubility-grid {
    display: grid;
    grid-template-columns: repeat(6, 1fr);
    gap: 4px;
    font-size: 0.65rem;
  }

  .sol-header {
    background: var(--surface);
    padding: 6px 4px;
    text-align: center;
    color: var(--muted);
    border-radius: 2px;
    font-weight: 500;
  }

  .sol-cell {
    padding: 8px 4px;
    text-align: center;
    border-radius: 2px;
    font-weight: 500;
    cursor: default;
    transition: transform 0.15s;
  }

  .sol-cell:hover { transform: scale(1.1); }
  .sol-cell.S { background: rgba(61,214,200,0.15); color: var(--aqua); }
  .sol-cell.I { background: rgba(224,92,92,0.15); color: var(--red); }
  .sol-cell.P { background: rgba(201,168,76,0.15); color: var(--gold); }
  .sol-cell.D { background: rgba(160,108,221,0.15); color: var(--purple); }

  /* ── CONVERSOR pH ── */
  .ph-slider-wrap {
    margin-bottom: 20px;
  }

  .ph-slider {
    width: 100%;
    -webkit-appearance: none;
    height: 6px;
    border-radius: 3px;
    background: linear-gradient(to right,
      #e05c5c 0%,
      #e09c5c 14%,
      #e0d45c 28%,
      #9ce05c 42%,
      #3dd6c8 57%,
      #3d9dd6 71%,
      #6c3dd6 85%,
      #a06cdd 100%
    );
    outline: none;
    cursor: pointer;
    margin-bottom: 12px;
  }

  .ph-slider::-webkit-slider-thumb {
    -webkit-appearance: none;
    width: 20px;
    height: 20px;
    border-radius: 50%;
    background: var(--text);
    border: 3px solid var(--bg);
    box-shadow: 0 0 0 2px var(--aqua);
    cursor: pointer;
  }

  .ph-display {
    display: flex;
    align-items: center;
    gap: 20px;
    background: var(--card);
    border: 1px solid var(--border);
    border-radius: 2px;
    padding: 20px 24px;
  }

  .ph-num {
    font-family: 'Playfair Display', serif;
    font-size: 3rem;
    font-weight: 700;
    line-height: 1;
    min-width: 80px;
  }

  .ph-info { flex: 1; }
  .ph-type { font-size: 0.85rem; margin-bottom: 4px; }
  .ph-conc { font-size: 0.72rem; color: var(--muted); }

  /* ── QUIZ ── */
  .quiz-question {
    font-family: 'Cormorant Garamond', serif;
    font-size: 1.15rem;
    font-style: italic;
    color: var(--text);
    margin-bottom: 20px;
    line-height: 1.6;
    min-height: 60px;
  }

  .quiz-options {
    display: grid;
    gap: 8px;
    margin-bottom: 16px;
  }

  .quiz-opt {
    background: var(--card);
    border: 1px solid var(--border);
    color: var(--text);
    padding: 12px 16px;
    text-align: left;
    font-family: 'DM Mono', monospace;
    font-size: 0.78rem;
    cursor: pointer;
    border-radius: 2px;
    transition: all 0.2s;
    letter-spacing: 0.02em;
  }

  .quiz-opt:hover { border-color: var(--gold); color: var(--gold-light); }
  .quiz-opt.correct { border-color: var(--aqua); background: rgba(61,214,200,0.08); color: var(--aqua); }
  .quiz-opt.wrong { border-color: var(--red); background: rgba(224,92,92,0.08); color: var(--red); }
  .quiz-opt:disabled { cursor: default; }

  .quiz-feedback {
    font-size: 0.78rem;
    color: var(--muted);
    min-height: 24px;
    margin-bottom: 12px;
  }

  .quiz-score {
    display: flex;
    align-items: center;
    gap: 12px;
    font-size: 0.72rem;
    color: var(--muted);
  }

  .score-bar-wrap {
    flex: 1;
    height: 3px;
    background: var(--border);
    border-radius: 2px;
  }

  .score-bar {
    height: 100%;
    background: var(--aqua);
    border-radius: 2px;
    transition: width 0.4s ease;
  }

  /* ── ELEMENTO MODAL ── */
  #elem-modal {
    display: none;
    position: fixed;
    inset: 0;
    background: rgba(10,13,18,0.85);
    z-index: 100;
    align-items: center;
    justify-content: center;
    backdrop-filter: blur(4px);
  }

  #elem-modal.open { display: flex; }

  .modal-card {
    background: var(--card);
    border: 1px solid var(--border);
    border-radius: 4px;
    width: 340px;
    padding: 40px;
    position: relative;
    animation: popIn 0.2s ease;
  }

  @keyframes popIn {
    from { opacity: 0; transform: scale(0.93) translateY(8px); }
    to { opacity: 1; transform: scale(1) translateY(0); }
  }

  .modal-close {
    position: absolute;
    top: 16px;
    right: 16px;
    background: none;
    border: none;
    color: var(--muted);
    font-size: 1.2rem;
    cursor: pointer;
    line-height: 1;
    padding: 4px;
  }

  .modal-sym {
    font-family: 'Playfair Display', serif;
    font-size: 4rem;
    font-weight: 700;
    color: var(--aqua);
    line-height: 1;
  }

  .modal-name {
    font-family: 'Cormorant Garamond', serif;
    font-size: 1.4rem;
    color: var(--gold-light);
    margin: 8px 0 4px;
  }

  .modal-num { font-size: 0.72rem; color: var(--muted); margin-bottom: 20px; }

  .modal-props {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 12px;
  }

  .prop-item { }
  .prop-label { font-size: 0.6rem; color: var(--muted); letter-spacing: 0.1em; text-transform: uppercase; }
  .prop-val { font-size: 0.85rem; color: var(--text); margin-top: 2px; }

  /* ── FOOTER ── */
  footer {
    position: relative;
    z-index: 10;
    padding: 32px 48px;
    display: flex;
    align-items: center;
    justify-content: space-between;
    flex-wrap: wrap;
    gap: 12px;
    border-top: 1px solid var(--border);
  }

  footer p {
    font-size: 0.72rem;
    color: var(--muted);
    letter-spacing: 0.06em;
  }

  footer strong { color: var(--gold); }

  /* ── CANVAS ATOM ── */
  #atom-canvas {
    display: block;
    margin: 0 auto 16px;
  }

  /* ── ANIMS ── */
  @keyframes fadeUp {
    from { opacity: 0; transform: translateY(16px); }
    to { opacity: 1; transform: translateY(0); }
  }

  .section { animation: fadeUp 0.5s ease both; }
  .section:nth-child(1) { animation-delay: 0.1s; }
  .section:nth-child(2) { animation-delay: 0.2s; }
  .section:nth-child(3) { animation-delay: 0.3s; }
  .section:nth-child(4) { animation-delay: 0.4s; }

  .tooltip {
    font-size: 0.7rem;
    color: var(--muted);
    margin-top: 8px;
  }
</style>
</head>
<body>

<!-- HEADER -->
<header>
  <div class="header-left">
    <h1>Química <span>Interativa</span></h1>
    <p>Lopes Sebastião Neto · Professor de Informática &amp; Química · Cacuaco, Luanda</p>
  </div>
  <div class="header-right">
    <span class="badge active">&#9679; Online</span>
    <span class="badge">Angola</span>
  </div>
</header>

<!-- PERIODIC STRIP -->
<div class="periodic-strip" id="strip"></div>

<!-- MAIN GRID -->
<div class="main">

  <!-- SECÇÃO 1: MASSA MOLAR -->
  <div class="section">
    <div class="section-title">Calculadora de Massa Molar</div>
    <div class="calc-input-group">
      <input class="calc-input" id="formula-input" placeholder="Ex: H2O, NaCl, C6H12O6" type="text">
      <button class="btn" onclick="calcMolar()">Calcular</button>
    </div>
    <div class="result-box" id="molar-result">
      <span style="color:var(--muted);font-size:0.78rem;">Introduza uma fórmula química acima.</span>
    </div>
    <p class="tooltip">Suporta fórmulas como H₂O, NaCl, C₆H₁₂O₆, CaCO₃, H₂SO₄…</p>
  </div>

  <!-- SECÇÃO 2: pH -->
  <div class="section">
    <div class="section-title">Conversor de pH</div>
    <div class="ph-slider-wrap">
      <input type="range" class="ph-slider" id="ph-slider" min="0" max="14" step="0.1" value="7" oninput="updatePH()">
    </div>
    <div class="ph-display">
      <div class="ph-num" id="ph-num" style="color:var(--aqua)">7.0</div>
      <div class="ph-info">
        <div class="ph-type" id="ph-type">Neutro</div>
        <div class="ph-conc" id="ph-conc">[H⁺] = 1.0 × 10⁻⁷ mol/L</div>
        <div class="ph-conc" id="ph-oh">[OH⁻] = 1.0 × 10⁻⁷ mol/L</div>
      </div>
    </div>
  </div>

  <!-- SECÇÃO 3: ÁTOMO ANIMADO -->
  <div class="section">
    <div class="section-title">Modelo Atómico</div>
    <canvas id="atom-canvas" width="320" height="220"></canvas>
    <div class="calc-input-group">
      <input class="calc-input" id="proton-input" type="number" min="1" max="118" value="6" placeholder="Número atómico (Z)">
      <button class="btn" onclick="drawAtomFromInput()">Ver</button>
    </div>
    <div id="atom-info" style="font-size:0.78rem;color:var(--muted);margin-top:8px;"></div>
  </div>

  <!-- SECÇÃO 4: QUIZ -->
  <div class="section">
    <div class="section-title">Quiz de Química</div>
    <div class="quiz-question" id="q-text">A carregar pergunta…</div>
    <div class="quiz-options" id="q-opts"></div>
    <div class="quiz-feedback" id="q-feedback"></div>
    <div class="quiz-score">
      <span id="q-score-txt">0/0</span>
      <div class="score-bar-wrap"><div class="score-bar" id="q-bar" style="width:0%"></div></div>
      <button class="btn" onclick="nextQuestion()" style="padding:6px 14px;font-size:0.7rem;">Próxima →</button>
    </div>
  </div>

  <!-- SECÇÃO 5: SOLUBILIDADE -->
  <div class="section">
    <div class="section-title">Tabela de Solubilidade</div>
    <div class="solubility-grid" id="sol-grid"></div>
    <div style="display:flex;gap:16px;margin-top:16px;flex-wrap:wrap;">
      <span class="sol-cell S" style="font-size:0.7rem;padding:4px 10px;">S = Solúvel</span>
      <span class="sol-cell I" style="font-size:0.7rem;padding:4px 10px;">I = Insolúvel</span>
      <span class="sol-cell P" style="font-size:0.7rem;padding:4px 10px;">P = Pouco sol.</span>
      <span class="sol-cell D" style="font-size:0.7rem;padding:4px 10px;">D = Decompõe</span>
    </div>
  </div>

  <!-- SECÇÃO 6: BALANCEAMENTO RÁPIDO -->
  <div class="section">
    <div class="section-title">Reacções Notáveis</div>
    <div id="reactions-list"></div>
  </div>

</div>

<!-- ELEMENTO MODAL -->
<div id="elem-modal">
  <div class="modal-card">
    <button class="modal-close" onclick="closeModal()">✕</button>
    <div class="modal-sym" id="m-sym"></div>
    <div class="modal-name" id="m-name"></div>
    <div class="modal-num" id="m-num"></div>
    <div class="modal-props" id="m-props"></div>
  </div>
</div>

<!-- FOOTER -->
<footer>
  <p>© 2025 <strong>Lopes Sebastião Neto</strong> — Professor de Química &amp; Informática</p>
  <p>Instituto Médio Técnico Privado de Saúde Arco-Íris · ESPE-Bengo</p>
</footer>

<script>
// ══════════════════════════════════════════════════════════
// DADOS
// ══════════════════════════════════════════════════════════

const ELEMENTS = [
  { sym:'H',  name:'Hidrogénio',  num:1,  mass:1.008,   cat:'nonmetal', color:'#3dd6c8', config:'1s¹', group:'1A', period:1 },
  { sym:'He', name:'Hélio',       num:2,  mass:4.003,   cat:'noble',    color:'#a06cdd', config:'1s²', group:'0',  period:1 },
  { sym:'Li', name:'Lítio',       num:3,  mass:6.941,   cat:'alkali',   color:'#e05c5c', config:'[He]2s¹', group:'1A', period:2 },
  { sym:'C',  name:'Carbono',     num:6,  mass:12.011,  cat:'nonmetal', color:'#3dd6c8', config:'[He]2s²2p²', group:'4A', period:2 },
  { sym:'N',  name:'Nitrogénio',  num:7,  mass:14.007,  cat:'nonmetal', color:'#3dd6c8', config:'[He]2s²2p³', group:'5A', period:2 },
  { sym:'O',  name:'Oxigénio',    num:8,  mass:15.999,  cat:'nonmetal', color:'#3dd6c8', config:'[He]2s²2p⁴', group:'6A', period:2 },
  { sym:'F',  name:'Flúor',       num:9,  mass:18.998,  cat:'halogen',  color:'#c9a84c', config:'[He]2s²2p⁵', group:'7A', period:2 },
  { sym:'Na', name:'Sódio',       num:11, mass:22.990,  cat:'alkali',   color:'#e05c5c', config:'[Ne]3s¹', group:'1A', period:3 },
  { sym:'Mg', name:'Magnésio',    num:12, mass:24.305,  cat:'alkaline', color:'#e09c5c', config:'[Ne]3s²', group:'2A', period:3 },
  { sym:'Al', name:'Alumínio',    num:13, mass:26.982,  cat:'metal',    color:'#6b7899', config:'[Ne]3s²3p¹', group:'3A', period:3 },
  { sym:'Si', name:'Silício',     num:14, mass:28.086,  cat:'metalloid',color:'#6b7899', config:'[Ne]3s²3p²', group:'4A', period:3 },
  { sym:'P',  name:'Fósforo',     num:15, mass:30.974,  cat:'nonmetal', color:'#3dd6c8', config:'[Ne]3s²3p³', group:'5A', period:3 },
  { sym:'S',  name:'Enxofre',     num:16, mass:32.06,   cat:'nonmetal', color:'#3dd6c8', config:'[Ne]3s²3p⁴', group:'6A', period:3 },
  { sym:'Cl', name:'Cloro',       num:17, mass:35.45,   cat:'halogen',  color:'#c9a84c', config:'[Ne]3s²3p⁵', group:'7A', period:3 },
  { sym:'K',  name:'Potássio',    num:19, mass:39.098,  cat:'alkali',   color:'#e05c5c', config:'[Ar]4s¹', group:'1A', period:4 },
  { sym:'Ca', name:'Cálcio',      num:20, mass:40.078,  cat:'alkaline', color:'#e09c5c', config:'[Ar]4s²', group:'2A', period:4 },
  { sym:'Fe', name:'Ferro',       num:26, mass:55.845,  cat:'metal',    color:'#6b7899', config:'[Ar]3d⁶4s²', group:'8B', period:4 },
  { sym:'Cu', name:'Cobre',       num:29, mass:63.546,  cat:'metal',    color:'#e09c5c', config:'[Ar]3d¹⁰4s¹', group:'1B', period:4 },
  { sym:'Zn', name:'Zinco',       num:30, mass:65.38,   cat:'metal',    color:'#6b7899', config:'[Ar]3d¹⁰4s²', group:'2B', period:4 },
  { sym:'Br', name:'Bromo',       num:35, mass:79.904,  cat:'halogen',  color:'#c9a84c', config:'[Ar]3d¹⁰4s²4p⁵', group:'7A', period:4 },
  { sym:'Ag', name:'Prata',       num:47, mass:107.868, cat:'metal',    color:'#a0b0c8', config:'[Kr]4d¹⁰5s¹', group:'1B', period:5 },
  { sym:'I',  name:'Iodo',        num:53, mass:126.904, cat:'halogen',  color:'#c9a84c', config:'[Kr]4d¹⁰5s²5p⁵', group:'7A', period:5 },
  { sym:'Ba', name:'Bário',       num:56, mass:137.327, cat:'alkaline', color:'#e09c5c', config:'[Xe]6s²', group:'2A', period:6 },
{ sym:'Au', name:'Ouro',        num:79, mass:196.967, cat:'metal',    color:'#c9a84c', config:'[Xe]4f¹⁴5d¹⁰6s¹', group:'1B', period:6 },
  { sym:'Hg', name:'Mercúrio',    num:80, mass:200.59,  cat:'metal',    color:'#6b7899', config:'[Xe]4f¹⁴5d¹⁰6s²', group:'2B', period:6 },
  { sym:'Pb', name:'Chumbo',      num:82, mass:207.2,   cat:'metal',    color:'#6b7899', config:'[Xe]4f¹⁴5d¹⁰6s²6p²', group:'4A', period:6 },
];

const MOLAR_MASSES = {
  H:1.008,He:4.003,Li:6.941,Be:9.012,B:10.811,C:12.011,N:14.007,O:15.999,
  F:18.998,Ne:20.180,Na:22.990,Mg:24.305,Al:26.982,Si:28.086,P:30.974,
  S:32.065,Cl:35.453,Ar:39.948,K:39.098,Ca:40.078,Sc:44.956,Ti:47.867,
  V:50.942,Cr:51.996,Mn:54.938,Fe:55.845,Co:58.933,Ni:58.693,Cu:63.546,
  Zn:65.38,Ga:69.723,Ge:72.63,As:74.922,Se:78.96,Br:79.904,Kr:83.798,
  Rb:85.468,Sr:87.62,Ag:107.868,I:126.904,Ba:137.327,Au:196.967,Hg:200.59,Pb:207.2
};

// ══════════════════════════════════════════════════════════
// STRIP PERIÓDICA
// ══════════════════════════════════════════════════════════
const strip = document.getElementById('strip');
ELEMENTS.forEach(e => {
  const el = document.createElement('div');
  el.className = 'elem';
  el.style.setProperty('--elem-color', e.color);
  el.innerHTML = `<span class="num">${e.num}</span><span class="sym">${e.sym}</span><span class="name">${e.name.substring(0,4)}</span>`;
  el.onclick = () => showModal(e);
  strip.appendChild(el);
});

// ══════════════════════════════════════════════════════════
// MODAL
// ══════════════════════════════════════════════════════════
function showModal(e) {
  document.getElementById('m-sym').textContent = e.sym;
  document.getElementById('m-name').textContent = e.name;
  document.getElementById('m-num').textContent = `Z = ${e.num}  ·  Nº Atómico`;
  document.getElementById('m-props').innerHTML = `
    <div class="prop-item"><div class="prop-label">Massa Atómica</div><div class="prop-val">${e.mass} u</div></div>
    <div class="prop-item"><div class="prop-label">Grupo</div><div class="prop-val">${e.group}</div></div>
    <div class="prop-item"><div class="prop-label">Período</div><div class="prop-val">${e.period}</div></div>
    <div class="prop-item"><div class="prop-label">Config. Elec.</div><div class="prop-val" style="font-size:0.75rem">${e.config}</div></div>
  `;
  document.getElementById('m-sym').style.color = e.color;
  document.getElementById('elem-modal').classList.add('open');
}

function closeModal() {
  document.getElementById('elem-modal').classList.remove('open');
}

document.getElementById('elem-modal').addEventListener('click', function(ev) {
  if (ev.target === this) closeModal();
});

// ══════════════════════════════════════════════════════════
// CALCULADORA MOLAR
// ══════════════════════════════════════════════════════════
function parseMolar(formula) {
  let total = 0;
  const re = /([A-Z][a-z]?)(\d*)/g;
  let match;
  let breakdown = [];
  while ((match = re.exec(formula)) !== null) {
    const sym = match[1];
    const qty = parseInt(match[2] || '1');
    const mass = MOLAR_MASSES[sym];
    if (!mass) return null;
    total += mass * qty;
    breakdown.push(`${sym}<sub>${qty > 1 ? qty : ''}</sub>: ${(mass*qty).toFixed(3)} g/mol`);
  }
  return { total: total.toFixed(3), breakdown };
}

function calcMolar() {
  const raw = document.getElementById('formula-input').value.trim();
  const box = document.getElementById('molar-result');
  if (!raw) { box.innerHTML = '<span style="color:var(--muted)">Introduza uma fórmula.</span>'; return; }
  const res = parseMolar(raw);
  if (!res) {
    box.innerHTML = '<span style="color:var(--red)">Fórmula inválida ou elemento desconhecido.</span>';
    return;
  }
  box.innerHTML = `
    <span class="result-value">${res.total} <span style="font-size:1rem;color:var(--muted)">g/mol</span></span>
    <span class="result-label">${res.breakdown.join('  ·  ')}</span>
  `;
}

document.getElementById('formula-input').addEventListener('keydown', e => { if (e.key === 'Enter') calcMolar(); });

// ══════════════════════════════════════════════════════════
// pH
// ══════════════════════════════════════════════════════════
function updatePH() {
  const ph = parseFloat(document.getElementById('ph-slider').value);
  const hConc = Math.pow(10, -ph);
  const ohConc = Math.pow(10, -(14 - ph));

  const phEl = document.getElementById('ph-num');
  phEl.textContent = ph.toFixed(1);

  let color, type;
  if (ph < 3)       { color = '#e05c5c'; type = '🔴 Ácido forte'; }
  else if (ph < 7)  { color = '#e0c45c'; type = '🟡 Ácido'; }
  else if (ph === 7){ color = '#3dd6c8'; type = '🟢 Neutro'; }
  else if (ph < 11) { color = '#5c9de0'; type = '🔵 Base'; }
  else              { color = '#a06cdd'; type = '🟣 Base forte'; }

  phEl.style.color = color;
  document.getElementById('ph-type').textContent = type;
  document.getElementById('ph-conc').innerHTML = `[H⁺] = ${hConc.toExponential(2)} mol/L`;
  document.getElementById('ph-oh').innerHTML = `[OH⁻] = ${ohConc.toExponential(2)} mol/L`;
}
updatePH();

// ══════════════════════════════════════════════════════════
// ÁTOMO CANVAS
// ══════════════════════════════════════════════════════════
const canvas = document.getElementById('atom-canvas');
const ctx = canvas.getContext('2d');
let atomZ = 6;
let animFrame;
let angle = 0;

function getShells(z) {
  const shells = [2, 8, 18, 32, 18, 8, 2];
  let rem = z;
  return shells.map(max => {
    const n = Math.min(rem, max);
    rem -= n;
    return n;
  }).filter((n, i) => i < 4 && n > 0);
}

function drawAtom(z) {
  cancelAnimationFrame(animFrame);
  atomZ = z;
  const shells = getShells(z);
  const cx = canvas.width / 2, cy = canvas.height / 2;

  function frame(t) {
    ctx.clearRect(0, 0, canvas.width, canvas.height);

    // nucleus
    const ng = ctx.createRadialGradient(cx, cy, 2, cx, cy, 18);
    ng.addColorStop(0, '#e8cc85');
    ng.addColorStop(1, '#c9a84c');
    ctx.beginPath();
    ctx.arc(cx, cy, 14 + Math.sin(t * 0.003) * 2, 0, Math.PI * 2);
    ctx.fillStyle = ng;
    ctx.fill();
    ctx.fillStyle = '#0a0d12';
    ctx.font = '10px DM Mono';
    ctx.textAlign = 'center';
    ctx.textBaseline = 'middle';
    ctx.fillText(z, cx, cy);

    shells.forEach((electrons, si) => {
      const r = 30 + si * 38;

      // orbit
      ctx.beginPath();
      ctx.arc(cx, cy, r, 0, Math.PI * 2);
      ctx.strokeStyle = 'rgba(30,39,64,0.8)';
      ctx.lineWidth = 1;
      ctx.stroke();

      // electrons
      for (let i = 0; i < electrons; i++) {
        const a = (2 * Math.PI * i / electrons) + (t * 0.001 * (si % 2 === 0 ? 1 : -1)) * (1 / (si + 1));
        const ex = cx + r * Math.cos(a);
        const ey = cy + r * Math.sin(a);

        // glow
        const eg = ctx.createRadialGradient(ex, ey, 0, ex, ey, 8);
        eg.addColorStop(0, 'rgba(61,214,200,0.6)');
        eg.addColorStop(1, 'rgba(61,214,200,0)');
        ctx.beginPath();
        ctx.arc(ex, ey, 8, 0, Math.PI * 2);
        ctx.fillStyle = eg;
        ctx.fill();

        ctx.beginPath();
        ctx.arc(ex, ey, 3.5, 0, Math.PI * 2);
        ctx.fillStyle = '#3dd6c8';
        ctx.fill();
      }
    });

    animFrame = requestAnimationFrame(frame);
  }

  animFrame = requestAnimationFrame(frame);

  const el = ELEMENTS.find(e => e.num === z);
  const info = el ? `${el.sym} — ${el.name} · Config: ${el.config}` : `Z = ${z}`;
  document.getElementById('atom-info').textContent = info;
}

function drawAtomFromInput() {
  const z = parseInt(document.getElementById('proton-input').value);
  if (z >= 1 && z <= 118) drawAtom(z);
}

drawAtom(6);

// ══════════════════════════════════════════════════════════
// QUIZ
// ══════════════════════════════════════════════════════════
const QUESTIONS = [
  { q: 'Qual é a fórmula química da água?', opts: ['H₂O', 'H₂O₂', 'HO', 'OH₂'], ans: 0 },
  { q: 'Quantos electrões tem o carbono no estado fundamental?', opts: ['4', '6', '8', '12'], ans: 1 },
  { q: 'O pH de uma solução neutra a 25°C é:', opts: ['0', '7', '14', '1'], ans: 1 },
  { q: 'Qual a massa molar aproximada do NaCl?', opts: ['23 g/mol', '35,5 g/mol', '58,5 g/mol', '71 g/mol'], ans: 2 },
  { q: 'A reacção de neutralização produz:', opts: ['Ácido e Base', 'Sal e Água', 'Óxido e Água', 'Metal e Gás'], ans: 1 },
  { q: 'Qual destes é um gás nobre?', opts: ['Nitrogénio', 'Oxigénio', 'Árgon', 'Cloro'], ans: 2 },
  { q: 'O número de Avogadro é aproximadamente:', opts: ['6,02×10²³', '6,02×10¹⁰', '9,18×10³¹', '3,14×10⁸'], ans: 0 },
  { q: 'Qual é o símbolo químico do ouro?', opts: ['Or', 'Go', 'Au', 'Ag'], ans: 2 },
  { q: 'O metano tem fórmula:', opts: ['C₂H₆', 'CH₄', 'C₃H₈', 'C₂H₄'], ans: 1 },
  { q: 'Uma ligação covalente partilha:', opts: ['Electrões', 'Protões', 'Neutrões', 'Iões'], ans: 0 },
];

let qIdx = 0, correct = 0, total = 0;

function loadQuestion() {
  const q = QUESTIONS[qIdx % QUESTIONS.length];
  document.getElementById('q-text').textContent = q.q;
  document.getElementById('q-feedback').textContent = '';
  const opts = document.getElementById('q-opts');
  opts.innerHTML = '';
  q.opts.forEach((o, i) => {
    const b = document.createElement('button');
    b.className = 'quiz-opt';
    b.textContent = o;
    b.onclick = () => checkAnswer(i, q.ans, b);
    opts.appendChild(b);
  });
}

function checkAnswer(chosen, correct_idx, btn) {
  const btns = document.querySelectorAll('.quiz-opt');
  btns.forEach(b => b.disabled = true);
  total++;
  if (chosen === correct_idx) {
    btn.classList.add('correct');
    correct++;
    document.getElementById('q-feedback').textContent = '✓ Correcto!';
    document.getElementById('q-feedback').style.color = 'var(--aqua)';
  } else {
    btn.classList.add('wrong');
    btns[correct_idx].classList.add('correct');
    document.getElementById('q-feedback').textContent = '✗ Errado. A resposta correcta está a verde.';
    document.getElementById('q-feedback').style.color = 'var(--red)';
  }
  const pct = total > 0 ? (correct / total * 100) : 0;
  document.getElementById('q-score-txt').textContent = `${correct}/${total}`;
  document.getElementById('q-bar').style.width = pct + '%';
}

function nextQuestion() {
  qIdx = (qIdx + 1) % QUESTIONS.length;
  loadQuestion();
}

loadQuestion();

// ══════════════════════════════════════════════════════════
// SOLUBILIDADE
// ══════════════════════════════════════════════════════════
const SOL_CATIONS = ['Na⁺', 'K⁺', 'Ca²⁺', 'Ba²⁺', 'Fe³⁺'];
const SOL_ANIONS  = ['Cl⁻', 'SO₄²⁻', 'CO₃²⁻', 'OH⁻', 'NO₃⁻'];
const SOL_DATA = [
//           Cl⁻  SO₄²⁻ CO₃²⁻ OH⁻  NO₃⁻
/* Na⁺ */  ['S',  'S',  'S',  'S', 'S'],
/* K⁺  */  ['S',  'S',  'S',  'S', 'S'],
/* Ca²⁺*/  ['S',  'P',  'I',  'P', 'S'],
/* Ba²⁺*/  ['S',  'I',  'I',  'S', 'S'],
/* Fe³⁺*/  ['S',  'S',  'I',  'I', 'S'],
];

const grid = document.getElementById('sol-grid');
grid.innerHTML = '<div class="sol-header"></div>' +
  SOL_ANIONS.map(a => `<div class="sol-header">${a}</div>`).join('');

SOL_DATA.forEach((row, ri) => {
  grid.innerHTML += `<div class="sol-header">${SOL_CATIONS[ri]}</div>`;
  row.forEach(cell => {
    grid.innerHTML += `<div class="sol-cell ${cell}" title="${cell==='S'?'Solúvel':cell==='I'?'Insolúvel':cell==='P'?'Pouco solúvel':'Decompõe'}">${cell}</div>`;
  });
});


// ══════════════════════════════════════════════════════════
// REACÇÕES NOTÁVEIS
// ══════════════════════════════════════════════════════════
const REACTIONS = [
  { name: 'Síntese da Água',        eq: '2H₂ + O₂ → 2H₂O',         tipo: 'Síntese', desc: 'Reacção exotérmica de combinação' },
  { name: 'Combustão do Metano',    eq: 'CH₄ + 2O₂ → CO₂ + 2H₂O',  tipo: 'Combustão', desc: 'Principal reacção do gás natural' },
  { name: 'Fotossíntese',           eq: '6CO₂ + 6H₂O → C₆H₁₂O₆ + 6O₂', tipo: 'Bioquímica', desc: 'Conversão de energia solar em glicose' },
  { name: 'Neutralização HCl/NaOH', eq: 'HCl + NaOH → NaCl + H₂O', tipo: 'Neutralização', desc: 'Reacção ácido-base clássica' },
  { name: 'Decomp. do CaCO₃',       eq: 'CaCO₃ → CaO + CO₂',       tipo: 'Decomposição', desc: 'Base da indústria do cimento' },
];

const rl = document.getElementById('reactions-list');
REACTIONS.forEach(r => {
  const tipColors = { Síntese:'var(--aqua)', Combustão:'var(--red)', Bioquímica:'var(--gold)', Neutralização:'var(--purple)', Decomposição:'#e09c5c' };
  rl.innerHTML += `
    <div style="border:1px solid var(--border);border-radius:2px;padding:16px 20px;margin-bottom:8px;background:var(--card);">
      <div style="display:flex;align-items:center;gap:10px;margin-bottom:6px;">
        <span style="font-size:0.65rem;padding:2px 8px;background:rgba(0,0,0,0.3);border-radius:2px;color:${tipColors[r.tipo]||'var(--muted)'};">${r.tipo}</span>
        <span style="font-size:0.8rem;color:var(--text)">${r.name}</span>
      </div>
      <div style="font-family:'Playfair Display',serif;font-size:1rem;color:var(--gold-light);margin-bottom:4px;">${r.eq}</div>
      <div style="font-size:0.7rem;color:var(--muted)">${r.desc}</div>
    </div>
  `
});
</script>
</body>
</html>
