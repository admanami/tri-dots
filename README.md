<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Tri-Dots</title>
<meta name="theme-color" content="#0b0c14">
<meta name="apple-mobile-web-app-capable" content="yes">
<meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
<meta name="apple-mobile-web-app-title" content="Tri-Dots">
<link rel="manifest" href="manifest.json">
<link href="https://fonts.googleapis.com/css2?family=DM+Mono:wght@400;500&family=Syne:wght@700;800&display=swap" rel="stylesheet">
<script>
  if ('serviceWorker' in navigator) {
    window.addEventListener('load', () => {
      navigator.serviceWorker.register('sw.js');
    });
  }
</script>
<style>
:root {
  --bg: #0b0c14;
  --surface: #13141f;
  --border: #1e2035;
  --c1: #ff7c5c;
  --c2: #47d9bf;
  --c1f: rgba(255,124,92,0.18);
  --c2f: rgba(71,217,191,0.18);
  --c1b: rgba(255,124,92,0.08);
  --c2b: rgba(71,217,191,0.08);
  --dot: #9098c0;
  --edge-ghost: rgba(120,130,180,0.12);
  --edge-drawn: rgba(200,210,255,0.88);
  --text: #d8ddf5;
  --muted: #5a6080;
}
* { box-sizing: border-box; margin: 0; padding: 0; }
body {
  font-family: 'Syne', sans-serif;
  background: var(--bg);
  color: var(--text);
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 18px 12px 32px;
  gap: 16px;
  overflow-x: auto;
  /* subtle grid pattern */
  background-image:
    linear-gradient(rgba(255,255,255,0.012) 1px, transparent 1px),
    linear-gradient(90deg, rgba(255,255,255,0.012) 1px, transparent 1px);
  background-size: 32px 32px;
}

/* ── Header ────────────────────────────────── */
.header { text-align: center; }
h1 {
  font-size: clamp(1.8rem, 6vw, 2.6rem);
  font-weight: 800;
  letter-spacing: 6px;
  text-transform: uppercase;
  background: linear-gradient(110deg, var(--c1) 0%, #e0a0ff 50%, var(--c2) 100%);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
}
.tagline {
  font-family: 'DM Mono', monospace;
  font-size: 1.4rem;
  color: var(--muted);
  letter-spacing: 2px;
  text-transform: uppercase;
  margin-top: 2px;
}

/* ── Controls ──────────────────────────────── */
.controls {
  display: flex;
  align-items: center;
  gap: 8px;
  flex-wrap: wrap;
  justify-content: center;
}
.ctrl-label {
  font-family: 'DM Mono', monospace;
  font-size: 1.44rem;
  color: var(--muted);
  letter-spacing: 1px;
}
select {
  font-family: 'DM Mono', monospace;
  font-size: 1.56rem;
  background: var(--surface);
  border: 1px solid var(--border);
  color: var(--text);
  border-radius: 8px;
  padding: 5px 10px;
  cursor: pointer;
  outline: none;
  transition: border-color 0.2s;
  appearance: none;
}
select:hover, select:focus { border-color: var(--c2); }

.seg {
  display: flex;
  background: var(--surface);
  border: 1px solid var(--border);
  border-radius: 10px;
  padding: 3px;
  gap: 3px;
}
.seg-btn {
  font-family: 'DM Mono', monospace;
  font-size: 1.44rem;
  background: transparent;
  border: none;
  color: var(--muted);
  border-radius: 7px;
  padding: 4px 12px;
  cursor: pointer;
  transition: all 0.18s;
  letter-spacing: 0.5px;
}
.seg-btn.on {
  background: var(--c2);
  color: #0b0c14;
  font-weight: 700;
}
.new-btn {
  font-family: 'Syne', sans-serif;
  font-size: 1.2rem;
  font-weight: 700;
  letter-spacing: 1px;
  text-transform: uppercase;
  background: linear-gradient(110deg, var(--c1), var(--c2));
  border: none;
  color: #0b0c14;
  border-radius: 10px;
  padding: 7px 18px;
  cursor: pointer;
  transition: opacity 0.15s, transform 0.1s;
}
.new-btn:hover { opacity: 0.88; }
.new-btn:active { transform: scale(0.96); }

/* ── Scoreboard ────────────────────────────── */
.scoreboard {
  display: flex;
  align-items: center;
  gap: 18px;
}
.pcard {
  min-width: 100px;
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 10px 22px;
  border-radius: 14px;
  background: var(--surface);
  border: 1.5px solid var(--border);
  transition: border-color 0.3s, box-shadow 0.3s;
}
.pcard.p1 { --pc: var(--c1); --pbg: var(--c1b); }
.pcard.p2 { --pc: var(--c2); --pbg: var(--c2b); }
.pcard.alive {
  border-color: var(--pc);
  box-shadow: 0 0 24px -8px var(--pc);
  background: var(--pbg);
}
.pname {
  font-family: 'DM Mono', monospace;
  font-size: 1.3rem;
  letter-spacing: 2px;
  text-transform: uppercase;
  color: var(--pc);
  opacity: 0.8;
}
.pscore {
  font-size: 2.4rem;
  font-weight: 800;
  color: var(--pc);
  line-height: 1;
  font-variant-numeric: tabular-nums;
}
.vs {
  font-family: 'DM Mono', monospace;
  font-size: 1.5rem;
  color: var(--muted);
  letter-spacing: 2px;
}

/* ── Status ────────────────────────────────── */
#status {
  font-family: 'DM Mono', monospace;
  font-size: 1.56rem;
  color: var(--muted);
  letter-spacing: 1px;
  min-height: 1.4em;
  text-align: center;
  transition: color 0.3s;
}
#status.win {
  font-size: 2rem;
  font-weight: 500;
  color: var(--text);
  letter-spacing: 2px;
}

/* ── Canvas ────────────────────────────────── */
#canvas {
  border-radius: 16px;
  display: block;
  cursor: crosshair;
}
</style>
</head>
<body>

<div class="header">
  <h1>TRI-DOTS</h1>
  <p class="tagline">Connect dots &middot; Claim triangles &middot; Outplay everyone</p>
</div>

<div class="controls">
  <span class="ctrl-label">Grid:</span>
  <select id="szSel">
    <option value="3">3 × 3</option>
    <option value="4" selected>4 × 4</option>
    <option value="5">5 × 5</option>
    <option value="6">6 × 6</option>
  </select>
  <div class="seg">
    <button class="seg-btn on" id="btnPvP">2 Players</button>
    <button class="seg-btn" id="btnPvC">vs CPU</button>
  </div>
  <button class="new-btn" id="btnNew">↺ New Game</button>
</div>

<div class="scoreboard">
  <div class="pcard p1 alive" id="card0">
    <span class="pname">Player 1</span>
    <span class="pscore" id="sc0">0</span>
  </div>
  <span class="vs">VS</span>
  <div class="pcard p2" id="card1">
    <span class="pname" id="p2name">Player 2</span>
    <span class="pscore" id="sc1">0</span>
  </div>
</div>

<div id="status">Player 1's turn</div>
<canvas id="canvas"></canvas>

<script>
// ═══════════════════════════════════════════════════
//  Constants & refs
// ═══════════════════════════════════════════════════
const canvas = document.getElementById('canvas');
const ctx    = canvas.getContext('2d');

const C1  = '#ff7c5c', C2 = '#47d9bf';
const C1F = 'rgba(255,124,92,0.20)', C2F = 'rgba(71,217,191,0.20)';
const C1S = 'rgba(255,124,92,0.45)', C2S = 'rgba(71,217,191,0.45)';
const COLORS  = [C1, C2];
const CFILL   = [C1F, C2F];
const CSTROKE = [C1S, C2S];
const EDGE_GHOST  = 'rgba(100,110,160,0.14)';
const EDGE_DRAWN  = 'rgba(200,210,255,0.85)';
const DOT_COL     = '#808ab8';

// ═══════════════════════════════════════════════════
//  State
// ═══════════════════════════════════════════════════
let ROWS, COLS, CELL, MARGIN;
let hEdges, rEdges, lEdges;   // null | 0 | 1
let upOwner, downOwner;        // null | 0 | 1
let curPlayer, scores, gameOver, vsComputer, hover, thinking;

// ═══════════════════════════════════════════════════
//  Init
// ═══════════════════════════════════════════════════
function mkMat(r, c, v) {
  return Array.from({length: r}, () => new Array(c).fill(v));
}

function initGame() {
  const sz = parseInt(document.getElementById('szSel').value);
  ROWS = sz; COLS = sz;

  const dpr = window.devicePixelRatio || 1;
  CELL = 76;
  MARGIN = Math.round(CELL * 0.65);

  const Hpx = CELL * Math.sqrt(3) / 2;
  const cssW = Math.ceil(COLS * CELL + ROWS * CELL / 2 + MARGIN * 2);
  const cssH = Math.ceil(ROWS * Hpx + MARGIN * 2);

  canvas.style.width  = cssW + 'px';
  canvas.style.height = cssH + 'px';
  canvas.width  = cssW * dpr;
  canvas.height = cssH * dpr;
  ctx.setTransform(dpr, 0, 0, dpr, 0, 0);

  hEdges   = mkMat(ROWS + 1, COLS, null);
  rEdges   = mkMat(ROWS, COLS + 1, null);
  lEdges   = mkMat(ROWS, COLS, null);
  upOwner  = mkMat(ROWS, COLS, null);
  downOwner= mkMat(ROWS, COLS, null);

  curPlayer = 0; scores = [0, 0];
  gameOver  = false; hover = null; thinking = false;

  refreshUI(0);
  draw();
}

// ═══════════════════════════════════════════════════
//  Geometry helpers
// ═══════════════════════════════════════════════════
function dotXY(r, c) {
  return {
    x: MARGIN + c * CELL + r * CELL / 2,
    y: MARGIN + r * CELL * Math.sqrt(3) / 2
  };
}

function edgePts(e) {
  let p1, p2;
  const {type: t, r, c} = e;
  if (t === 'H') { p1 = dotXY(r, c);   p2 = dotXY(r, c+1); }
  else if (t === 'R') { p1 = dotXY(r, c); p2 = dotXY(r+1, c); }
  else            { p1 = dotXY(r, c+1); p2 = dotXY(r+1, c); }  // L
  return { p1, p2 };
}

function triCentroid(pts) {
  return {
    x: (pts[0].x + pts[1].x + pts[2].x) / 3,
    y: (pts[0].y + pts[1].y + pts[2].y) / 3
  };
}

function distSeg(px, py, ax, ay, bx, by) {
  const dx = bx-ax, dy = by-ay, l2 = dx*dx+dy*dy;
  if (!l2) return Math.hypot(px-ax, py-ay);
  const t = Math.max(0, Math.min(1, ((px-ax)*dx + (py-ay)*dy) / l2));
  return Math.hypot(px-(ax+t*dx), py-(ay+t*dy));
}

// ═══════════════════════════════════════════════════
//  Edge accessors
// ═══════════════════════════════════════════════════
function getE(e) {
  if (e.type==='H') return hEdges[e.r][e.c];
  if (e.type==='R') return rEdges[e.r][e.c];
  return lEdges[e.r][e.c];
}
function setE(e, v) {
  if (e.type==='H') hEdges[e.r][e.c] = v;
  else if (e.type==='R') rEdges[e.r][e.c] = v;
  else lEdges[e.r][e.c] = v;
}
function eqE(a, b) {
  return a && b && a.type===b.type && a.r===b.r && a.c===b.c;
}

function allFreeEdges() {
  const out = [];
  for (let r=0;r<=ROWS;r++) for (let c=0;c<COLS;c++)   if (hEdges[r][c]===null) out.push({type:'H',r,c});
  for (let r=0;r<ROWS;r++)  for (let c=0;c<=COLS;c++)  if (rEdges[r][c]===null) out.push({type:'R',r,c});
  for (let r=0;r<ROWS;r++)  for (let c=0;c<COLS;c++)   if (lEdges[r][c]===null) out.push({type:'L',r,c});
  return out;
}
function allEdges() {
  const out = [];
  for (let r=0;r<=ROWS;r++) for (let c=0;c<COLS;c++)  out.push({type:'H',r,c});
  for (let r=0;r<ROWS;r++)  for (let c=0;c<=COLS;c++) out.push({type:'R',r,c});
  for (let r=0;r<ROWS;r++)  for (let c=0;c<COLS;c++)  out.push({type:'L',r,c});
  return out;
}

// ═══════════════════════════════════════════════════
//  Triangle completion checks
// ═══════════════════════════════════════════════════
// Up-triangle (r,c): vertices (r,c) (r,c+1) (r+1,c)
//   edges: H[r][c], R[r][c], L[r][c]
// Down-triangle (r,c): vertices (r,c+1) (r+1,c) (r+1,c+1)
//   edges: L[r][c], H[r+1][c], R[r][c+1]

function upOk(r,c) {
  return r>=0&&r<ROWS&&c>=0&&c<COLS&&upOwner[r][c]===null
    && hEdges[r][c]!==null && rEdges[r][c]!==null && lEdges[r][c]!==null;
}
function downOk(r,c) {
  return r>=0&&r<ROWS&&c>=0&&c<COLS&&downOwner[r][c]===null
    && lEdges[r][c]!==null && hEdges[r+1][c]!==null && rEdges[r][c+1]!==null;
}

function upEdgeCount(r,c) {
  if (r<0||r>=ROWS||c<0||c>=COLS||upOwner[r][c]!==null) return -1;
  return (hEdges[r][c]!==null?1:0)+(rEdges[r][c]!==null?1:0)+(lEdges[r][c]!==null?1:0);
}
function downEdgeCount(r,c) {
  if (r<0||r>=ROWS||c<0||c>=COLS||downOwner[r][c]!==null) return -1;
  return (lEdges[r][c]!==null?1:0)+(hEdges[r+1][c]!==null?1:0)+(rEdges[r][c+1]!==null?1:0);
}

// Triangles that contain edge e
function trisOf(e) {
  const {type:t, r, c} = e;
  if (t==='H') return [{up:true,r,c},{up:false,r:r-1,c}];
  if (t==='R') return [{up:true,r,c},{up:false,r,c:c-1}];
  return [{up:true,r,c},{up:false,r,c}]; // L
}
function validTri({up,r,c}) {
  return r>=0&&r<ROWS&&c>=0&&c<COLS;
}

// ═══════════════════════════════════════════════════
//  Core move
// ═══════════════════════════════════════════════════
function makeMove(edge) {
  if (getE(edge)!==null || gameOver || thinking) return false;
  setE(edge, curPlayer);

  let claimed = 0;
  for (let r=0;r<ROWS;r++) for (let c=0;c<COLS;c++) {
    if (upOk(r,c))  { upOwner[r][c]   = curPlayer; claimed++; }
    if (downOk(r,c)){ downOwner[r][c] = curPlayer; claimed++; }
  }
  scores[curPlayer] += claimed;

  const total = ROWS * COLS * 2;
  gameOver = (scores[0] + scores[1] === total);

  if (!gameOver && claimed === 0) curPlayer = 1 - curPlayer;

  draw();
  refreshUI(claimed);

  if (!gameOver && vsComputer && curPlayer === 1) {
    thinking = true;
    setTimeout(() => {
      thinking = false;
      const mv = bestMove();
      if (mv) makeMove(mv);
    }, 480);
  }
  return true;
}

// ═══════════════════════════════════════════════════
//  CPU AI
// ═══════════════════════════════════════════════════
function bestMove() {
  const free = allFreeEdges();
  if (!free.length) return null;

  // 1. Complete a triangle now
  for (const e of free) {
    setE(e, 1);
    const wins = trisOf(e).filter(validTri).some(({up,r,c}) => up ? upOk(r,c) : downOk(r,c));
    setE(e, null);
    if (wins) return e;
  }

  // 2. Safe: don't leave any triangle at 2 edges
  const safe = free.filter(e => {
    setE(e, 1);
    const danger = trisOf(e).filter(validTri).some(({up,r,c}) =>
      (up ? upEdgeCount(r,c) : downEdgeCount(r,c)) === 2
    );
    setE(e, null);
    return !danger;
  });
  if (safe.length) return safe[Math.floor(Math.random() * safe.length)];

  // 3. Sacrifice minimum: pick edge opening fewest 2-edge triangles
  free.sort((a, b) => {
    const cnt = e => {
      setE(e, 1);
      const n = trisOf(e).filter(validTri).filter(({up,r,c}) =>
        (up ? upEdgeCount(r,c) : downEdgeCount(r,c)) === 2
      ).length;
      setE(e, null);
      return n;
    };
    return cnt(a) - cnt(b);
  });
  return free[0];
}

// ═══════════════════════════════════════════════════
//  Nearest edge to click/touch
// ═══════════════════════════════════════════════════
function nearestEdge(mx, my) {
  let best = null, bestD = CELL * 0.44;
  for (const e of allEdges()) {
    if (getE(e) !== null) continue;
    const {p1, p2} = edgePts(e);
    const d = distSeg(mx, my, p1.x, p1.y, p2.x, p2.y);
    if (d < bestD) { bestD = d; best = e; }
  }
  return best;
}

// ═══════════════════════════════════════════════════
//  Draw
// ═══════════════════════════════════════════════════
function draw() {
  const cssW = parseFloat(canvas.style.width);
  const cssH = parseFloat(canvas.style.height);
  ctx.clearRect(0, 0, cssW, cssH);

  // Background
  ctx.fillStyle = '#0b0c14';
  ctx.fillRect(0, 0, cssW, cssH);

  // ── Claimed triangles ────────────────────────
  for (let r=0;r<ROWS;r++) for (let c=0;c<COLS;c++) {
    if (upOwner[r][c] !== null) {
      drawTri(
        [dotXY(r,c), dotXY(r,c+1), dotXY(r+1,c)],
        CFILL[upOwner[r][c]],
        CSTROKE[upOwner[r][c]]
      );
    }
    if (downOwner[r][c] !== null) {
      drawTri(
        [dotXY(r,c+1), dotXY(r+1,c), dotXY(r+1,c+1)],
        CFILL[downOwner[r][c]],
        CSTROKE[downOwner[r][c]]
      );
    }
  }

  // ── Player number in claimed triangle ──────
  ctx.textAlign = 'center';
  ctx.textBaseline = 'middle';
  const tFontSize = Math.max(9, Math.round(CELL * 0.18));
  ctx.font = `700 ${tFontSize}px 'DM Mono', monospace`;
  for (let r=0;r<ROWS;r++) for (let c=0;c<COLS;c++) {
    if (upOwner[r][c] !== null) {
      const cen = triCentroid([dotXY(r,c), dotXY(r,c+1), dotXY(r+1,c)]);
      ctx.fillStyle = COLORS[upOwner[r][c]];
      ctx.globalAlpha = 0.5;
      ctx.fillText(vsComputer && upOwner[r][c]===1 ? 'C' : (upOwner[r][c]+1)+'', cen.x, cen.y);
    }
    if (downOwner[r][c] !== null) {
      const cen = triCentroid([dotXY(r,c+1), dotXY(r+1,c), dotXY(r+1,c+1)]);
      ctx.fillStyle = COLORS[downOwner[r][c]];
      ctx.globalAlpha = 0.5;
      ctx.fillText(vsComputer && downOwner[r][c]===1 ? 'C' : (downOwner[r][c]+1)+'', cen.x, cen.y);
    }
  }
  ctx.globalAlpha = 1;

  // ── Edges ───────────────────────────────────
  ctx.lineCap = 'round';
  for (const e of allEdges()) {
    const state = getE(e);
    const isHov = eqE(e, hover);
    const {p1, p2} = edgePts(e);
    ctx.beginPath();
    ctx.moveTo(p1.x, p1.y);
    ctx.lineTo(p2.x, p2.y);

    if (state !== null) {
      ctx.strokeStyle = EDGE_DRAWN;
      ctx.lineWidth   = CELL * 0.09;
      ctx.globalAlpha = 0.88;
      ctx.setLineDash([]);
    } else if (isHov) {
      ctx.strokeStyle = COLORS[curPlayer];
      ctx.lineWidth   = CELL * 0.12;
      ctx.globalAlpha = 0.9;
      ctx.setLineDash([]);
      // glow
      ctx.shadowColor = COLORS[curPlayer];
      ctx.shadowBlur  = CELL * 0.5;
    } else {
      ctx.strokeStyle = EDGE_GHOST;
      ctx.lineWidth   = CELL * 0.03;
      ctx.globalAlpha = 1;
      ctx.setLineDash([CELL*0.055, CELL*0.085]);
    }
    ctx.stroke();
    ctx.setLineDash([]);
    ctx.shadowBlur  = 0;
    ctx.globalAlpha = 1;
  }

  // ── Dots ────────────────────────────────────
  const dotR = CELL * 0.072;
  for (let r=0;r<=ROWS;r++) for (let c=0;c<=COLS;c++) {
    const p = dotXY(r, c);
    ctx.beginPath();
    ctx.arc(p.x, p.y, dotR, 0, Math.PI*2);
    ctx.fillStyle = DOT_COL;
    ctx.shadowColor = 'rgba(140,155,210,0.5)';
    ctx.shadowBlur  = dotR * 2;
    ctx.fill();
    ctx.shadowBlur  = 0;
  }
}

function drawTri(pts, fill, stroke) {
  ctx.beginPath();
  ctx.moveTo(pts[0].x, pts[0].y);
  ctx.lineTo(pts[1].x, pts[1].y);
  ctx.lineTo(pts[2].x, pts[2].y);
  ctx.closePath();
  ctx.fillStyle   = fill;
  ctx.fill();
  ctx.strokeStyle = stroke;
  ctx.lineWidth   = 0.5;
  ctx.globalAlpha = 0.35;
  ctx.stroke();
  ctx.globalAlpha = 1;
}

// ═══════════════════════════════════════════════════
//  UI helpers
// ═══════════════════════════════════════════════════
function refreshUI(claimed) {
  document.getElementById('sc0').textContent = scores[0];
  document.getElementById('sc1').textContent = scores[1];

  document.getElementById('card0').classList.toggle('alive', curPlayer===0 && !gameOver);
  document.getElementById('card1').classList.toggle('alive', curPlayer===1 && !gameOver);

  const statusEl = document.getElementById('status');
  if (gameOver) {
    statusEl.className = 'win';
    if (scores[0] > scores[1])      statusEl.textContent = '🎉  PLAYER 1 WINS!';
    else if (scores[1] > scores[0]) statusEl.textContent = vsComputer ? '🤖  CPU WINS!' : '🎉  PLAYER 2 WINS!';
    else                            statusEl.textContent = '🤝  IT\'S A TIE!';
  } else {
    statusEl.className = '';
    const who = vsComputer && curPlayer===1 ? 'CPU' : `Player ${curPlayer+1}`;
    if (claimed > 0) {
      statusEl.textContent = `${who} claimed ${claimed} triangle${claimed>1?'s':''}!  Go again →`;
    } else {
      statusEl.textContent = `${who}'s turn`;
    }
  }
}

// ═══════════════════════════════════════════════════
//  Coordinate helper for events
// ═══════════════════════════════════════════════════
function cssCoords(clientX, clientY) {
  const r = canvas.getBoundingClientRect();
  const sw = parseFloat(canvas.style.width)  || r.width;
  const sh = parseFloat(canvas.style.height) || r.height;
  return {
    x: (clientX - r.left) * (sw / r.width),
    y: (clientY - r.top)  * (sh / r.height)
  };
}

// ═══════════════════════════════════════════════════
//  Events
// ═══════════════════════════════════════════════════
function playerCanAct() {
  return !gameOver && !thinking && !(vsComputer && curPlayer===1);
}

canvas.addEventListener('mousemove', e => {
  if (!playerCanAct()) { if (hover) { hover=null; draw(); } return; }
  const {x,y} = cssCoords(e.clientX, e.clientY);
  const ne = nearestEdge(x, y);
  if (!eqE(ne, hover)) { hover = ne; draw(); }
});

canvas.addEventListener('mouseleave', () => { hover = null; draw(); });

canvas.addEventListener('click', e => {
  if (!playerCanAct()) return;
  const {x,y} = cssCoords(e.clientX, e.clientY);
  const ne = nearestEdge(x, y);
  if (!ne) return;
  hover = null;
  makeMove(ne);
});

canvas.addEventListener('touchend', e => {
  if (!playerCanAct()) return;
  e.preventDefault();
  const t = e.changedTouches[0];
  const {x,y} = cssCoords(t.clientX, t.clientY);
  const ne = nearestEdge(x, y);
  if (!ne) return;
  hover = null;
  makeMove(ne);
}, {passive: false});

// Mode buttons
document.getElementById('btnPvP').addEventListener('click', () => {
  vsComputer = false;
  document.getElementById('btnPvP').classList.add('on');
  document.getElementById('btnPvC').classList.remove('on');
  document.getElementById('p2name').textContent = 'Player 2';
  initGame();
});
document.getElementById('btnPvC').addEventListener('click', () => {
  vsComputer = true;
  document.getElementById('btnPvC').classList.add('on');
  document.getElementById('btnPvP').classList.remove('on');
  document.getElementById('p2name').textContent = 'CPU';
  initGame();
});
document.getElementById('btnNew').addEventListener('click', initGame);
document.getElementById('szSel').addEventListener('change', initGame);

// ─── Boot ──────────────────────────────────────────
vsComputer = false;
initGame();
</script>
</body>
</html>
