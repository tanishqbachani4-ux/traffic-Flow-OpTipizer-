
<html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Traffic Flow Optimizer | Tanishq Bachani</title>
<style>
*{box-sizing:border-box}
body{margin:0;font-family:Arial,sans-serif;background:#0b1220;color:#eef4ff}
header{text-align:center;padding:22px 12px;background:linear-gradient(135deg,#102b50,#126e75)}
header h1{margin:0;font-size:clamp(24px,5vw,38px)}
header p{margin:9px 0 0;color:#d4e8f8}
.container{max-width:1050px;margin:auto;padding:16px}
.panel{background:#151f32;border:1px solid #2c3c56;border-radius:15px;padding:16px;margin-bottom:16px}
h2{font-size:20px;margin:0 0 14px}
.controls{display:grid;grid-template-columns:repeat(auto-fit,minmax(220px,1fr));gap:14px}
label{display:block;font-weight:bold;margin-bottom:8px}
select,input[type=range]{width:100%}
select,button{padding:11px;border-radius:8px;border:1px solid #435572;background:#202e44;color:white}
input[type=range]{accent-color:#29d5c0}
button{cursor:pointer;font-weight:bold}
button:hover{filter:brightness(1.15)}
.buttons{display:flex;flex-wrap:wrap;gap:9px;margin-top:15px}
.primary{background:#087f8c;border-color:#12b7b5}
.reset{background:#34445d}
.stats{display:grid;grid-template-columns:repeat(2,1fr);gap:10px}
.stat{background:#202d42;border-radius:10px;padding:14px;text-align:center}
.stat strong{display:block;font-size:27px;color:#42e2cf;margin-top:5px}
.stat span{font-size:13px;color:#c3d1e4}
.road-wrap{overflow:hidden;border-radius:12px;background:#172a2a;padding:10px}
.intersection{height:320px;position:relative;overflow:hidden;background:#31534a;border-radius:8px}
.road-h,.road-v{position:absolute;background:#303946}
.road-h{height:100px;width:100%;top:110px}
.road-v{width:100px;height:100%;left:calc(50% - 50px)}
.line-h{position:absolute;left:0;right:0;top:159px;border-top:3px dashed #e6e9d8}
.line-v{position:absolute;top:0;bottom:0;left:calc(50% - 1px);border-left:3px dashed #e6e9d8}
.stop{position:absolute;background:#f8fafc}
.stop-top{width:65px;height:5px;top:105px;left:calc(50% - 82px)}
.stop-bottom{width:65px;height:5px;top:210px;left:calc(50% + 17px)}
.stop-left{height:65px;width:5px;left:calc(50% - 55px);top:30px}
.stop-right{height:65px;width:5px;left:calc(50% + 50px);top:225px}
.light{position:absolute;z-index:5;background:#101820;padding:5px;border-radius:8px;display:flex;gap:4px}
.light i{width:13px;height:13px;border-radius:50%;background:#38414b}
.light .red.on{background:#ff4c5b;box-shadow:0 0 8px #ff4c5b}
.light .yellow.on{background:#ffd166;box-shadow:0 0 8px #ffd166}
.light .green.on{background:#30e58b;box-shadow:0 0 8px #30e58b}
.lt{top:72px;left:calc(50% - 95px)}
.lb{bottom:72px;right:calc(50% - 95px)}
.car{position:absolute;width:19px;height:34px;border-radius:5px;background:#48b9ff;border:2px solid #d9f3ff;z-index:3;box-shadow:0 2px 5px #1118}
.car.hcar{width:34px;height:18px}
.car.c2{background:#ffb454}
.car.c3{background:#d58bff}
.car.c4{background:#f6e66c}
#cars{position:absolute;inset:0;pointer-events:none}
.legend{font-size:12px;color:#c3d1e4;margin-top:9px}
.status{padding:11px;border-radius:8px;background:#202d42;margin-top:13px}
.status b{color:#45e2ce}
footer{text-align:center;padding:15px;color:#9eacc2;font-size:12px}
@media(max-width:500px){.intersection{height:270px}.road-h{top:90px}.road-v{left:calc(50% - 42px);width:84px}.line-h{top:132px}.line-v{left:calc(50% - 1px)}.stop-top{top:85px}.stop-bottom{top:180px}.stop-left{left:calc(50% - 47px);top:20px}.stop-right{left:calc(50% + 42px);top:190px}.lt{top:55px;left:calc(50% - 88px)}.lb{bottom:55px;right:calc(50% - 88px)}}
</style>
</head>
<body>
<header>
  <h1>🚦 Traffic Flow Optimizer</h1>
  <p>HCL Jigsaw 7.0 · AI Revolution & Digital Futures</p>
  <p>Created by Tanishq Bachani and Team</p>
</header>

<main class="container">
<section class="panel">
<h2>⚙️ Simulation Controls</h2>
<div class="controls">
  <div>
    <label for="mode">Signal Control Mode</label>
    <select id="mode">
      <option value="ai">AI Adaptive Mode</option>
      <option value="fixed">Fixed Timing Mode</option>
    </select>
  </div>
  <div>
    <label for="ns">North–South Traffic: <span id="nsVal">60</span>%</label>
    <input id="ns" type="range" min="10" max="100" value="60">
  </div>
  <div>
    <label for="ew">East–West Traffic: <span id="ewVal">40</span>%</label>
    <input id="ew" type="range" min="10" max="100" value="40">
  </div>
</div>
<div class="buttons">
  <button class="primary" id="start">▶ Start Simulation</button>
  <button id="pause"> Pause</button>
  <button class="reset" id="reset">↻ Reset</button>
</div>
<div class="status" id="status">Status: <b>Ready to start</b></div>
</section>

<section class="panel">
<h2> Four-Way Intersection</h2>
<div class="road-wrap">
<div class="intersection" id="intersection">
  <div class="road-h"></div><div class="road-v"></div>
  <div class="line-h"></div><div class="line-v"></div>
  <div class="stop stop-top"></div><div class="stop stop-bottom"></div>
  <div class="stop stop-left"></div><div class="stop stop-right"></div>
  <div class="light lt" id="lightNS"><i class="red on"></i><i class="yellow"></i><i class="green"></i></div>
  <div class="light lb" id="lightEW"><i class="red on"></i><i class="yellow"></i><i class="green"></i></div>
  <div id="cars"></div>
</div>
<div class="legend">Illustrative intersection • Blue, orange, purple and yellow vehicles represent traffic.</div>
</div>
</section>

<section class="panel">
<h2> Live Simulation Statistics</h2>
<div class="stats">
  <div class="stat"><span>Vehicles Processed</span><strong id="processed">0</strong></div>
  <div class="stat"><span>Estimated Waiting Time</span><strong><span id="wait" style="display:inline;color:inherit;font-size:27px">0</span> s</strong></div>
  <div class="stat"><span>Traffic Density</span><strong id="density">50%</strong></div>
  <div class="stat"><span>Signal Changes</span><strong id="changes">0</strong></div>
</div>
<p class="legend">These are simulated estimates for a school project, not measurements from a real road or a trained AI system.</p>
</section>

<section class="panel">
<h2> How It Works</h2>
<p><b>Fixed Timing:</b> The traffic lights switch at a preset interval.</p>
<p><b>AI Adaptive Mode:</b> A simplified rule-based model gives more green time to the direction with heavier traffic.</p>
<p><b>Project Goal:</b> Explore how adaptive signal timing may reduce waiting and improve traffic flow at a four-way intersection.</p>
</section>
</main>

<footer>
Mithi Gobindram Public School, Bhopal<br>
Team: Tanishq Bachani · Jayesh Managlani · Lakshya Damani · Rishabh Adwani
</footer>

<script>
const $ = id => document.getElementById(id);
let running = false, timer = null, seconds = 0, processed = 0, changes = 0;
let phase = "NS", phaseTime = 0;

function setStatus(message) {
  $("status").innerHTML = "Status: <b>" + message + "</b>";
}
function setLight(id, colour) {
  const lights = $(id).querySelectorAll("i");
  lights.forEach(el => el.classList.remove("on"));
  const index = {red:0, yellow:1, green:2}[colour];
  lights[index].classList.add("on");
}
function updateLights() {
  if (phase === "NS") {
    setLight("lightNS","green"); setLight("lightEW","red");
  } else {
    setLight("lightNS","red"); setLight("lightEW","green");
  }
}
function addCars() {
  const holder = $("cars");
  holder.innerHTML = "";
  const ns = Number($("ns").value);
  const ew = Number($("ew").value);
  const countNS = Math.max(1, Math.round(ns / 18));
  const countEW = Math.max(1, Math.round(ew / 18));
  for (let i=0;i<countNS;i++) {
    const car = document.createElement("div");
    car.className = "car c" + (i%4+1);
    car.style.left = "calc(50% - 10px)";
    car.style.top = (8+i*25) + "px";
    holder.appendChild(car);
  }
  for (let i=0;i<countEW;i++) {
    const car = document.createElement("div");
    car.className = "car hcar c" + (i%4+1);
    car.style.top = "calc(50% - 9px)";
    car.style.left = (8+i*38) + "px";
    holder.appendChild(car);
  }
}
function updateStats() {
  const ns = Number($("ns").value);
  const ew = Number($("ew").value);
  $("processed").textContent = processed;
  $("wait").textContent = Math.max(0, Math.round((ns+ew)/5 - (phaseTime%5)));
  $("density").textContent = Math.round((ns+ew)/2) + "%";
  $("changes").textContent = changes;
}
function tick() {
  if (!running) return;
  seconds++;
  phaseTime++;
  const mode = $("mode").value;
  const ns = Number($("ns").value);
  const ew = Number($("ew").value);
  let duration = 8;
  if (mode === "ai") {
    duration = phase === "NS"
      ? Math.max(5, Math.min(15, Math.round(5 + ns/10)))
      : Math.max(5, Math.min(15, Math.round(5 + ew/10)));
  }
  if (phaseTime >= duration) {
    phase = phase === "NS" ? "EW" : "NS";
    phaseTime = 0;
    changes++;
    updateLights();
  }
  const active = phase === "NS" ? ns : ew;
  processed += Math.max(1, Math.round(active/30));
  updateStats();
}
function start() {
  if (running) return;
  running = true;
  setStatus($("mode").value === "ai" ? "Running — AI Adaptive Mode" : "Running — Fixed Timing Mode");
  timer = setInterval(tick, 1000);
}
function pause() {
  running = false;
  if (timer) clearInterval(timer);
  timer = null;
  setStatus("Paused");
}
function reset() {
  pause();
  seconds = 0; processed = 0; changes = 0; phaseTime = 0; phase = "NS";
  updateLights(); updateStats(); addCars();
  setStatus("Reset — ready to start");
}
$("start").addEventListener("click", start);
$("pause").addEventListener("click", pause);
$("reset").addEventListener("click", reset);
["ns","ew"].forEach(id => $(id).addEventListener("input", () => {
  $(id+"Val").textContent = $(id).value;
  addCars(); updateStats();
}));
$("mode").addEventListener("change", () => {
  if (running) {
    pause();
    start();
  } else {
    setStatus($("mode").value === "ai" ? "AI Adaptive Mode selected" : "Fixed Timing Mode selected");
  }
});
updateLights();
addCars();
updateStats();
</script>
</body>
</html>
