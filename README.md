# poule-clicker1
Jeux ou une poule picore Quand on click
<!DOCTYPE html>
<html>
<head>
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Chicken Clicker Empire</title>
<style>
body {
  font-family: Arial;
  text-align: center;
  background: #fff6d6;
}

button {
  padding: 10px;
  margin: 5px;
  font-size: 16px;
}

.card {
  background: white;
  margin: 10px;
  padding: 10px;
  border-radius: 10px;
  box-shadow: 0 0 10px #00000020;
}

#chicken {
  font-size: 80px;
  cursor: pointer;
  user-select: none;
}

.small {
  font-size: 12px;
}
</style>
</head>

<body>

<h1>🐔 Chicken Clicker Empire</h1>

<div class="card">
  💰 Argent : <span id="money">0</span> €
  <br>
  ✖ Multiplicateur renaissance : <span id="mult">1</span>x
  <br>
  🔁 Renaissance : <span id="rebirth">0</span>
</div>

<div id="chicken">🐔</div>
<p class="small">Clique la poule (max 15 clics/sec)</p>

<div class="card">
  <h3>🏪 Boutique</h3>
  <button onclick="buyChicken()">Acheter poule (50€)</button>
  <br>
  🐔 Poules : <span id="chickens">1</span>
</div>

<div class="card">
  <h3>🔁 Renaissance</h3>
  <button onclick="rebirth()">Faire renaissance</button>
  <p class="small">Reset tout MAIS +0.5x multiplicateur</p>
</div>

<div class="card">
  <h3>🏆 Classement local</h3>
  <button onclick="saveScore()">Sauvegarder score</button>
  <div id="leaderboard"></div>
</div>

<script>

let money = 0;
let chickens = 1;
let mult = 1;
let rebirthCount = 0;

let clicks = [];
let lastClickTime = 0;

function update() {
  document.getElementById("money").innerText = Math.floor(money);
  document.getElementById("chickens").innerText = chickens;
  document.getElementById("mult").innerText = mult.toFixed(1);
  document.getElementById("rebirth").innerText = rebirthCount;
}

function canClick() {
  let now = Date.now();
  clicks = clicks.filter(t => now - t < 1000);
  return clicks.length < 15;
}

document.getElementById("chicken").addEventListener("click", () => {
  if (!canClick()) return;

  clicks.push(Date.now());

  money += chickens * mult;
  update();
});

function buyChicken() {
  let cost = 50 * chickens;
  if (money >= cost) {
    money -= cost;
    chickens++;
    update();
  }
}

function rebirth() {
  if (money < 500) return;

  rebirthCount++;
  mult += 0.5;

  money = 0;
  chickens = 1;

  update();
}

function saveScore() {
  let name = prompt("Ton pseudo ?");
  if (!name) return;

  let scores = JSON.parse(localStorage.getItem("scores") || "[]");

  scores.push({
    name: name,
    money: money,
    rebirth: rebirthCount
  });

  scores.sort((a,b) => b.money - a.money);

  localStorage.setItem("scores", JSON.stringify(scores.slice(0,5)));

  showScores();
}

function showScores() {
  let scores = JSON.parse(localStorage.getItem("scores") || "[]");
  let html = "";

  scores.forEach((s,i) => {
    html += (i+1) + ". " + s.name + " - 💰 " + Math.floor(s.money) + "€ 🔁 " + s.rebirth + "<br>";
  });

  document.getElementById("leaderboard").innerHTML = html;
}

update();
showScores();

</script>

</body>
</html>
