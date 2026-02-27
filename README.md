<div id="game-container">
  <h1>Score: <span id="score">0</span></h1>
  <button id="main-clicker">Click Me!</button>

  <div id="shop">
    <h3>Upgrades</h3>
    <!-- Upgrades are hidden initially -->
    <div id="upgrade-list"></div>
  </div>
</div>
let score = 0;
let autoClickers = 0;

// Upgrade data: name, base cost, and income per second
const upgrades = [
  { id: 'u1', name: 'Auto-Clicker', cost: 15, income: 1, count: 0, unlocked: false },
  { id: 'u2', name: 'Mega-Drill', cost: 100, income: 10, count: 0, unlocked: false },
];

function updateDisplay() {
  document.getElementById('score').innerText = Math.floor(score);
  
  upgrades.forEach(upg => {
    // Reveal upgrade if score reaches 80% of cost
    if (!upg.unlocked && score >= upg.cost * 0.8) {
      upg.unlocked = true;
      renderUpgrade(upg);
    }
    
    // Update button text and disable if too expensive
    const btn = document.getElementById(upg.id);
    if (btn) {
      btn.innerText = `${upg.name} (Cost: ${Math.floor(upg.cost)})`;
      btn.disabled = score < upg.cost;
    }
  });
}

function renderUpgrade(upg) {
  const btn = document.createElement('button');
  btn.id = upg.id;
  btn.className = 'upgrade-btn';
  btn.onclick = () => buyUpgrade(upg);
  document.getElementById('upgrade-list').appendChild(btn);
}

function buyUpgrade(upg) {
  if (score >= upg.cost) {
    score -= upg.cost;
    upg.count++;
    // Exponential cost scaling: cost = baseCost * (1.15 ^ amountOwned)
    upg.cost = Math.floor(upg.cost * 1.15); 
    autoClickers += upg.income;
    updateDisplay();
  }
}

// Main Click Logic
document.getElementById('main-clicker').onclick = () => {
  score++;
  updateDisplay();
};

// Passive Income Loop (runs every 100ms for smoothness)
setInterval(() => {
  score += autoClickers / 10;
  updateDisplay();
}, 100);
.upgrade-btn {
  display: block;
  margin: 10px 0;
  padding: 10px;
  animation: popIn 0.3s cubic-bezier(0.175, 0.885, 0.32, 1.275);
}

@keyframes popIn {
  0% { transform: scale(0.5); opacity: 0; }
  100% { transform: scale(1); opacity: 1; }
}
