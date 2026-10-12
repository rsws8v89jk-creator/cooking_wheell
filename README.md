<!DOCTYPE html>  
<html lang="ar" dir="rtl">  
<head>  
<meta charset="UTF-8">  
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">  
<title>🎡 عجلة الطبخ</title>  
<style>  
  * { margin:0; padding:0; box-sizing:border-box; -webkit-tap-highlight-color:transparent; }  
  body {  
    font-family: 'Arial', sans-serif;  
    background: linear-gradient(180deg,#87CEEB 0%,#B0E0E6 40%,#7CB342 100%);  
    min-height:100vh;  
    display:flex; flex-direction:column; align-items:center;  
    padding:10px; overflow-x:hidden;  
  }  
  .header {  
    width:100%; max-width:420px;  
    background:linear-gradient(135deg,#FFD700,#FFA500);  
    border-radius:15px; padding:10px 15px;  
    display:flex; justify-content:space-between; align-items:center;  
    box-shadow:0 4px 10px rgba(0,0,0,.2); margin-bottom:10px;  
  }  
  .header .balance {  
    font-size:20px; font-weight:bold; color:#5D4037;  
  }  
  .header .level {  
    background:#5D4037; color:#FFD700;  
    padding:5px 12px; border-radius:20px;  
    font-weight:bold; font-size:14px;  
  }  
  
  .wheel-container {  
    position:relative;  
    width:min(92vw,400px); height:min(92vw,400px);  
    margin:5px auto;  
  }  
  canvas {  
    width:100%; height:100%;  
    border-radius:50%;  
    box-shadow:0 0 0 8px #8D6E63, 0 0 0 14px #5D4037, 0 10px 25px rgba(0,0,0,.4);  
  }  
  .pointer {  
    position:absolute; top:-8px; left:50%;  
    transform:translateX(-50%);  
    width:0; height:0;  
    border-left:18px solid transparent;  
    border-right:18px solid transparent;  
    border-top:32px solid #E53935;  
    filter:drop-shadow(0 3px 4px rgba(0,0,0,.4));  
    z-index:10;  
  }  
  .spin-btn {  
    position:absolute; top:50%; left:50%;  
    transform:translate(-50%,-50%);  
    width:80px; height:80px; border-radius:50%;  
    background:radial-gradient(circle at 30% 30%,#FFD54F,#F57C00);  
    border:4px solid #fff;  
    color:#fff; font-weight:bold; font-size:16px;  
    cursor:pointer; z-index:5;  
    box-shadow:0 4px 12px rgba(0,0,0,.4);  
    display:flex; align-items:center; justify-content:center;  
    font-family:inherit;  
  }  
  .spin-btn:disabled { opacity:.6; }  
  
  .controls {  
    width:100%; max-width:420px;  
    background:rgba(255,255,255,.9);  
    border-radius:15px; padding:12px;  
    margin-top:12px;  
    box-shadow:0 4px 10px rgba(0,0,0,.2);  
  }  
  .label { font-size:14px; color:#333; margin-bottom:6px; font-weight:bold; }  
  .choices {  
    display:grid; grid-template-columns:repeat(4,1fr);  
    gap:6px; margin-bottom:10px;  
  }  
  .choice {  
    background:#fff; border:2px solid #ddd;  
    border-radius:10px; padding:8px 4px;  
    text-align:center; cursor:pointer;  
    font-size:20px; transition:.2s;  
  }  
  .choice.selected {  
    border-color:#E53935; background:#FFEBEE;  
    transform:scale(1.05);  
  }  
  .choice .mult { display:block; font-size:11px; color:#666; font-weight:bold; }  
  
  .bet-row { display:flex; gap:6px; margin-bottom:10px; }  
  .bet-btn {  
    flex:1; padding:8px; border:2px solid #FFA000;  
    background:#FFF8E1; border-radius:10px;  
    font-size:13px; font-weight:bold; color:#E65100;  
    cursor:pointer;  
  }  
  .bet-btn.active { background:#FFA000; color:#fff; }  
  
  .result {  
    text-align:center; font-size:16px;  
    font-weight:bold; min-height:24px;  
    padding:8px; border-radius:10px;  
  }  
  .result.win { background:#C8E6C9; color:#1B5E20; }  
  .result.lose { background:#FFCDD2; color:#B71C1C; }  
  .result.info { background:#E3F2FD; color:#0D47A1; }  
  
  .level-bar {  
    width:100%; height:10px; background:#ddd;  
    border-radius:5px; overflow:hidden; margin-top:6px;  
  }  
  .level-fill {  
    height:100%; background:linear-gradient(90deg,#4CAF50,#8BC34A);  
    width:0%; transition:.5s;  
  }  
  .level-text { font-size:12px; color:#555; text-align:center; margin-top:4px; }  
  
  .reset {  
    margin-top:10px; padding:8px 20px;  
    background:#607D8B; color:#fff; border:none;  
    border-radius:10px; font-size:13px; cursor:pointer;  
  }  
</style>  
</head>  
<body>  
  
<div class="header">  
  <div class="balance">💰 <span id="balance">100</span></div>  
  <div class="level">المستوى <span id="levelNum">1</span></div>  
</div>  
  
<div class="wheel-container">  
  <div class="pointer"></div>  
  <canvas id="wheel" width="600" height="600"></canvas>  
  <button class="spin-btn" id="spinBtn">دور!</button>  
</div>  
  
<div class="controls">  
  <div class="label">اختر قطاعك (راهن):</div>  
  <div class="choices" id="choices"></div>  
  
  <div class="label">مبلغ الرهان:</div>  
  <div class="bet-row" id="betRow">  
    <button class="bet-btn" data-bet="10">10</button>  
    <button class="bet-btn active" data-bet="25">25</button>  
    <button class="bet-btn" data-bet="50">50</button>  
    <button class="bet-btn" data-bet="100">100</button>  
  </div>  
  
  <div class="result info" id="result">اختر قطاع ثم اضغط "دور" 🎡</div>  
  
  <div class="level-bar"><div class="level-fill" id="levelFill"></div></div>  
  <div class="level-text" id="levelText">الرصيد المطلوب للمستوى القادم: 200</div>  
  
  <div style="text-align:center">  
    <button class="reset" onclick="resetGame()">🔄 إعادة اللعبة</button>  
  </div>  
</div>  
  
<script>  
// ====== إعدادات القطاعات ======  
const SECTORS = [  
  { name:'🍗', mult:45, color:'#FFB74D' },  
  { name:'🐟', mult:25, color:'#4FC3F7' },  
  { name:'🥩', mult:15, color:'#E57373' },  
  { name:'🦐', mult:10, color:'#FF8A65' },  
  { name:'🥦', mult:5,  color:'#81C784' },  
  { name:'🌽', mult:5,  color:'#FFD54F' },  
  { name:'🥕', mult:5,  color:'#FF8A65' },  
  { name:'🍅', mult:5,  color:'#EF5350' }  
];  
  
// ====== حالة اللعبة ======  
let state = {  
  balance: 100,  
  level: 1,  
  bet: 25,  
  selected: -1,  
  spinning: false,  
  rotation: 0  
};  
  
const LEVELS = [  
  { threshold: 200,   bonus: 1.0 },  
  { threshold: 500,   bonus: 1.2 },  
  { threshold: 1000,  bonus: 1.5 },  
  { threshold: 2000,  bonus: 2.0 },  
  { threshold: 5000,  bonus: 3.0 }  
];  
  
// ====== Canvas ======  
const canvas = document.getElementById('wheel');  
const ctx = canvas.getContext('2d');  
const CX = 300, CY = 300, R = 290;  
const SEG = (Math.PI*2) / SECTORS.length;  
  
function drawWheel(rotation) {  
  ctx.clearRect(0,0,600,600);  
  SECTORS.forEach((s,i) => {  
    const start = i*SEG + rotation - Math.PI/2 - SEG/2;  
    const end = start + SEG;  
    // القطاع  
    ctx.beginPath();  
    ctx.moveTo(CX,CY);  
    ctx.arc(CX,CY,R,start,end);  
    ctx.closePath();  
    ctx.fillStyle = s.color;  
    ctx.fill();  
    ctx.strokeStyle = '#fff';  
    ctx.lineWidth = 3;  
    ctx.stroke();  
  
    // النص  
    ctx.save();  
    ctx.translate(CX,CY);  
    ctx.rotate(start + SEG/2);  
    ctx.textAlign = 'center';  
    ctx.fillStyle = '#fff';  
    ctx.font = 'bold 50px Arial';  
    ctx.fillText(s.name, R*0.65, 15);  
    ctx.font = 'bold 28px Arial';  
    ctx.fillStyle = '#000';  
    ctx.fillText('x'+s.mult, R*0.65, 50);  
    ctx.restore();  
  });  
  // دائرة مركزية  
  ctx.beginPath();  
  ctx.arc(CX,CY,50,0,Math.PI*2);  
  ctx.fillStyle = '#5D4037';  
  ctx.fill();  
}  
  
// ====== واجهة ======  
function renderChoices() {  
  const box = document.getElementById('choices');  
  box.innerHTML = '';  
  SECTORS.forEach((s,i) => {  
    const d = document.createElement('div');  
    d.className = 'choice' + (state.selected===i?' selected':'');  
    d.innerHTML = `${s.name}<span class="mult">x${s.mult}</span>`;  
    d.onclick = () => {  
      if (state.spinning) return;  
      state.selected = i;  
      renderChoices();  
    };  
    box.appendChild(d);  
  });  
}  
  
function renderBet() {  
  document.querySelectorAll('.bet-btn').forEach(b => {  
    b.classList.toggle('active', +b.dataset.bet === state.bet);  
  });  
}  
document.querySelectorAll('.bet-btn').forEach(b => {  
  b.onclick = () => {  
    if (state.spinning) return;  
    state.bet = +b.dataset.bet;  
    renderBet();  
  };  
});  
  
function updateUI() {  
  document.getElementById('balance').textContent = state.balance;  
  document.getElementById('levelNum').textContent = state.level;  
  const next = LEVELS[state.level-1];  
  if (next) {  
    const prev = state.level>1 ? LEVELS[state.level-2].threshold : 0;  
    const pct = Math.min(100, ((state.balance - prev)/(next.threshold - prev))*100);  
    document.getElementById('levelFill').style.width = pct + '%';  
    document.getElementById('levelText').textContent =  
      `المستوى القادم عند ${next.threshold} 💰 (مكافأة x${next.bonus})`;  
  } else {  
    document.getElementById('levelFill').style.width = '100%';  
    document.getElementById('levelText').textContent = '🏆 وصلت أعلى مستوى!';  
  }  
}  
  
function setResult(text, type) {  
  const r = document.getElementById('result');  
  r.textContent = text;  
  r.className = 'result ' + type;  
}  
  
// ====== دوران ======  
function spin() {  
  if (state.spinning) return;  
  if (state.selected === -1) { setResult('⚠️ اختر قطاع أول!', 'lose'); return; }  
  if (state.balance < state.bet) { setResult('❌ رصيدك ما يكفي!', 'lose'); return; }  
  
  state.spinning = true;  
  state.balance -= state.bet;  
  updateUI();  
  document.getElementById('spinBtn').disabled = true;  
  setResult('🎡 العجلة تدور...', 'info');  
  
  // الفائز عشوائي  
  const winnerIndex = Math.floor(Math.random() * SECTORS.length);  
  
  // حساب زاوية التوقف  
  const targetAngle = winnerIndex * SEG + SEG/2;  
  const extraTurns = 5 + Math.floor(Math.random()*3);  
  const finalRotation = Math.PI*2*extraTurns + (Math.PI/2 - targetAngle);  
  
  const startRot = state.rotation;  
  const delta = finalRotation - startRot;  
  const duration = 4000;  
  const startTime = performance.now();  
  
  function animate(now) {  
    const t = Math.min(1, (now - startTime)/duration);  
    const ease = 1 - Math.pow(1-t, 3); // easeOutCubic  
    state.rotation = startRot + delta*ease;  
    drawWheel(state.rotation);  
    if (t < 1) {  
      requestAnimationFrame(animate);  
    } else {  
      finishSpin(winnerIndex);  
    }  
  }  
  requestAnimationFrame(animate);  
}  
  
function finishSpin(winner) {  
  state.spinning = false;  
  document.getElementById('spinBtn').disabled = false;  
  
  const s = SECTORS[winner];  
  if (winner === state.selected) {  
    const bonus = LEVELS[state.level-1] ? LEVELS[state.level-1].bonus : 1;  
    const win = Math.round(state.bet * s.mult * bonus);  
    state.balance += win;  
    setResult(`🎉 فزت! ${s.name} x${s.mult} = +${win} 💰`, 'win');  
  } else {  
    setResult(`😢 للأسف، طلع ${s.name} x${s.mult}. خسرت ${state.bet} 💰`, 'lose');  
  }  
  checkLevel();  
  updateUI();  
}  
  
function checkLevel() {  
  while (state.level <= LEVELS.length && state.balance >= LEVELS[state.level-1].threshold) {  
    state.level++;  
    setTimeout(() => alert(`🎊 مبروك! وصلت المستوى ${state.level}`), 300);  
  }  
}  
  
// ====== إعادة ======  
function resetGame() {  
  if (!confirm('تبي تبدأ من جديد؟')) return;  
  state = { balance:100, level:1, bet:25, selected:-1, spinning:false, rotation:0 };  
  renderChoices();  
  renderBet();  
  updateUI();  
  setResult('اختر قطاع ثم اضغط "دور" 🎡', 'info');  
  drawWheel(0);  
}  
  
// ====== تشغيل ======  
document.getElementById('spinBtn').onclick = spin;  
renderChoices();  
renderBet();  
updateUI();  
drawWheel(0);  
</script>  
</body>  
</html>  
