
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Bhopal Smart Signals - Traffic Flow Optimizer</title>
    <style>
        :root {
            --bg-color: #0f172a;
            --panel-bg: #1e293b;
            --accent-blue: #38bdf8;
            --accent-green: #22c55e;
            --accent-red: #ef4444;
            --accent-yellow: #eab308;
            --text-main: #f8fafc;
            --text-muted: #94a3b8;
        }

        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }

        body {
            background-color: var(--bg-color);
            color: var(--text-main);
            display: flex;
            flex-direction: column;
            min-height: 100vh;
            padding: 15px;
        }

        header {
            background: var(--panel-bg);
            padding: 15px 20px;
            border-radius: 10px;
            margin-bottom: 15px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            border-left: 5px solid var(--accent-blue);
        }

        header h1 {
            font-size: 1.4rem;
            color: var(--text-main);
        }

        header p {
            font-size: 0.85rem;
            color: var(--text-muted);
        }

        .team-badge {
            background: rgba(56, 189, 248, 0.1);
            color: var(--accent-blue);
            padding: 5px 12px;
            border-radius: 20px;
            font-size: 0.8rem;
            font-weight: bold;
            border: 1px solid rgba(56, 189, 248, 0.3);
        }

        .main-container {
            display: grid;
            grid-template-columns: 1fr 340px;
            gap: 15px;
            flex: 1;
        }

        @media (max-width: 900px) {
            .main-container {
                grid-template-columns: 1fr;
            }
        }

        .simulation-panel {
            background: var(--panel-bg);
            border-radius: 10px;
            padding: 15px;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            position: relative;
        }

        canvas {
            background: #273549;
            border-radius: 8px;
            max-width: 100%;
            height: auto;
            box-shadow: 0 4px 20px rgba(0,0,0,0.4);
        }

        .controls-panel {
            background: var(--panel-bg);
            border-radius: 10px;
            padding: 15px;
            display: flex;
            flex-direction: column;
            gap: 15px;
        }

        .panel-section {
            background: rgba(15, 23, 42, 0.6);
            padding: 12px;
            border-radius: 8px;
            border: 1px solid rgba(255,255,255,0.05);
        }

        .panel-section h3 {
            font-size: 0.95rem;
            margin-bottom: 10px;
            color: var(--accent-blue);
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        .control-group {
            margin-bottom: 10px;
        }

        .control-group:last-child {
            margin-bottom: 0;
        }

        label {
            display: flex;
            justify-content: space-between;
            font-size: 0.8rem;
            color: var(--text-muted);
            margin-bottom: 4px;
        }

        input[type="range"] {
            width: 100%;
            accent-color: var(--accent-blue);
        }

        .btn-group {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 8px;
        }

        button {
            padding: 10px;
            border: none;
            border-radius: 6px;
            font-weight: bold;
            font-size: 0.85rem;
            cursor: pointer;
            transition: all 0.2s;
        }

        .btn-primary {
            background: var(--accent-blue);
            color: #0f172a;
        }

        .btn-primary:hover {
            background: #0284c7;
            color: white;
        }

        .btn-mode {
            background: rgba(255,255,255,0.1);
            color: var(--text-main);
        }

        .btn-mode.active-fixed {
            background: var(--accent-yellow);
            color: #0f172a;
        }

        .btn-mode.active-smart {
            background: var(--accent-green);
            color: #0f172a;
        }

        .metrics-grid {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 8px;
        }

        .metric-card {
            background: rgba(255,255,255,0.03);
            padding: 8px;
            border-radius: 6px;
            text-align: center;
        }

        .metric-value {
            font-size: 1.2rem;
            font-weight: bold;
            color: var(--text-main);
        }

        .metric-label {
            font-size: 0.7rem;
            color: var(--text-muted);
        }

        .comparison-box {
            font-size: 0.75rem;
            line-height: 1.4;
            color: var(--text-muted);
        }

        .status-pill {
            display: inline-block;
            padding: 2px 8px;
            border-radius: 10px;
            font-size: 0.75rem;
            font-weight: bold;
        }

        .status-ns {
            background: rgba(34, 197, 94, 0.2);
            color: var(--accent-green);
        }

        .status-ew {
            background: rgba(239, 68, 68, 0.2);
            color: var(--accent-red);
        }
    </style>
</head>
<body>

    <header>
        <div>
            <h1>Traffic Flow Optimizer</h1>
            <p>HCL JIGSAW Edition 7 · Grade 8 Model</p>
        </div>
        <div class="team-badge">Team: Bhopal Smart Signals</div>
    </header>

    <div class="main-container">
        <!-- Canvas Simulation Area -->
        <div class="simulation-panel">
            <canvas id="simCanvas" width="500" height="500"></canvas>
        </div>

        <!-- Right Side Control Dashboard -->
        <div class="controls-panel">
            
            <!-- Mode Selector -->
            <div class="panel-section">
                <h3>Signal Mode</h3>
                <div class="btn-group">
                    <button id="btnFixed" class="btn-mode active-fixed" onclick="setMode('fixed')">Fixed (10s)</button>
                    <button id="btnSmart" class="btn-mode" onclick="setMode('smart')">Smart (5-20s)</button>
                </div>
            </div>

            <!-- Inputs / Sliders -->
            <div class="panel-section">
                <h3>Traffic Arrival Rates</h3>
                <div class="control-group">
                    <label>North - South Traffic: <span id="nsValue">6 veh/min (90/15m)</span></label>
                    <input type="range" id="nsRate" min="2" max="20" value="6" oninput="updateSliders()">
                </div>
                <div class="control-group">
                    <label>East - West Traffic: <span id="ewValue">12 veh/min (180/15m)</span></label>
                    <input type="range" id="ewRate" min="2" max="20" value="12" oninput="updateSliders()">
                </div>
            </div>

            <!-- Signal Status -->
            <div class="panel-section">
                <h3>Active Signal Timing</h3>
                <div style="display: flex; justify-content: space-between; align-items: center; margin-bottom: 5px;">
                    <span style="font-size: 0.85rem;">Active Green Phase:</span>
                    <span id="activePhase" class="status-pill status-ns">North - South</span>
                </div>
                <div style="display: flex; justify-content: space-between; align-items: center;">
                    <span style="font-size: 0.85rem;">Phase Time Left:</span>
                    <span id="timerDisplay" style="font-weight: bold; color: var(--accent-blue);">10.0 s</span>
                </div>
            </div>

            <!-- Results Metrics -->
            <div class="panel-section">
                <h3>Live Simulation Metrics</h3>
                <div class="metrics-grid">
                    <div class="metric-card">
                        <div class="metric-value" id="passedCount">0</div>
                        <div class="metric-label">Vehicles Passed</div>
                    </div>
                    <div class="metric-card">
                        <div class="metric-value" id="avgWait">0.0s</div>
                        <div class="metric-label">Avg Wait Time</div>
                    </div>
                    <div class="metric-card">
                        <div class="metric-value" id="queueNS">0</div>
                        <div class="metric-label">N-S Queue</div>
                    </div>
                    <div class="metric-card">
                        <div class="metric-value" id="queueEW">0</div>
                        <div class="metric-label">E-W Queue</div>
                    </div>
                </div>
            </div>

            <!-- Simulation Controls -->
            <div class="btn-group">
                <button class="btn-primary" id="btnPause" onclick="togglePause()">Pause</button>
                <button style="background: rgba(255,255,255,0.1); color: white;" onclick="resetSim()">Reset Data</button>
            </div>

            <div class="comparison-box">
                <b>Rules:</b> Fixed mode alternates 10s green lights. Smart mode calculates queue differential to award 5s–20s green lights dynamically.
            </div>

        </div>
    </div>

    <script>
        const canvas = document.getElementById('simCanvas');
        const ctx = canvas.getContext('2d');

        // Simulation parameters
        let mode = 'fixed';
        let isPaused = false;
        
        let nsArrivalRate = 6;
        let ewArrivalRate = 12;

        // Signal Timer state
        let currentGreen = 'NS';
        let timerMax = 10;
        let timerRemaining = 10;
        let lastTime = performance.now();

        // Queue & Analytics Data
        let queues = { N: [], S: [], E: [], W: [] };
        let activeVehicles = [];
        let totalPassed = 0;
        let totalWaitTime = 0;

        function updateSliders() {
            nsArrivalRate = parseInt(document.getElementById('nsRate').value);
            ewArrivalRate = parseInt(document.getElementById('ewRate').value);

            document.getElementById('nsValue').innerText = `${nsArrivalRate} veh/min (${nsArrivalRate * 15}/15m)`;
            document.getElementById('ewValue').innerText = `${ewArrivalRate} veh/min (${ewArrivalRate * 15}/15m)`;
        }

        function setMode(newMode) {
            mode = newMode;
            document.getElementById('btnFixed').className = mode === 'fixed' ? 'btn-mode active-fixed' : 'btn-mode';
            document.getElementById('btnSmart').className = mode === 'smart' ? 'btn-mode active-smart' : 'btn-mode';
            resetSim();
        }

        function resetSim() {
            queues = { N: [], S: [], E: [], W: [] };
            activeVehicles = [];
            totalPassed = 0;
            totalWaitTime = 0;
            currentGreen = 'NS';
            timerRemaining = calculateNextGreenDuration('NS');
            timerMax = timerRemaining;
            lastTime = performance.now();
        }

        function togglePause() {
            isPaused = !isPaused;
            document.getElementById('btnPause').innerText = isPaused ? 'Resume' : 'Pause';
        }

        function calculateNextGreenDuration(direction) {
            if (mode === 'fixed') {
                return 10;
            } else {
                let nsQueueLen = queues.N.length + queues.S.length;
                let ewQueueLen = queues.E.length + queues.W.length;

                let queueDiff = direction === 'NS' ? (nsQueueLen - ewQueueLen) : (ewQueueLen - nsQueueLen);

                let duration = 10 + Math.floor(queueDiff * 1.5);
                return Math.max(5, Math.min(20, duration));
            }
        }

        function spawnVehicles(dt) {
            let nsProb = (nsArrivalRate / 60) * dt;
            if (Math.random() < nsProb / 2) spawnVehicle('N');
            if (Math.random() < nsProb / 2) spawnVehicle('S');

            let ewProb = (ewArrivalRate / 60) * dt;
            if (Math.random() < ewProb / 2) spawnVehicle('E');
            if (Math.random() < ewProb / 2) spawnVehicle('W');
        }

        function spawnVehicle(dir) {
            let car = {
                dir: dir,
                created: performance.now(),
                passed: false
            };
            queues[dir].push(car);
            activeVehicles.push(car);
        }

        function updateSimulation(dt) {
            if (isPaused) return;

            timerRemaining -= dt;
            if (timerRemaining <= 0) {
                currentGreen = currentGreen === 'NS' ? 'EW' : 'NS';
                timerRemaining = calculateNextGreenDuration(currentGreen);
                timerMax = timerRemaining;
            }

            spawnVehicles(dt);

            let clearanceRate = dt / 1.2; 

            if (currentGreen === 'NS') {
                clearQueue('N', clearanceRate);
                clearQueue('S', clearanceRate);
            } else {
                clearQueue('E', clearanceRate);
                clearQueue('W', clearanceRate);
            }

            let now = performance.now();
            ['N', 'S', 'E', 'W'].forEach(d => {
                queues[d].forEach(car => {
                    totalWaitTime += dt;
                });
            });

            document.getElementById('timerDisplay').innerText = `${timerRemaining.toFixed(1)} s`;
            let phaseDisplay = document.getElementById('activePhase');
            phaseDisplay.innerText = currentGreen === 'NS' ? 'North - South' : 'East - West';
            phaseDisplay.className = currentGreen === 'NS' ? 'status-pill status-ns' : 'status-pill status-ew';

            document.getElementById('passedCount').innerText = totalPassed;
            document.getElementById('avgWait').innerText = totalPassed > 0 ? `${(totalWaitTime / totalPassed).toFixed(1)}s` : '0.0s';
            document.getElementById('queueNS').innerText = queues.N.length + queues.S.length;
            document.getElementById('queueEW').innerText = queues.E.length + queues.W.length;
        }

        function clearQueue(dir, rate) {
            if (queues[dir].length > 0 && Math.random() < rate) {
                let car = queues[dir].shift();
                car.passed = true;
                totalPassed++;
            }
        }

        function draw() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);

            const roadWidth = 100;
            const cx = canvas.width / 2;
            const cy = canvas.height / 2;

            ctx.fillStyle = '#1e293b';
            ctx.fillRect(cx - roadWidth/2, 0, roadWidth, canvas.height);
            ctx.fillRect(0, cy - roadWidth/2, canvas.width, roadWidth);

            ctx.strokeStyle = '#cbd5e1';
            ctx.setLineDash([10, 10]);
            ctx.lineWidth = 2;

            ctx.beginPath();
            ctx.moveTo(cx, 0); ctx.lineTo(cx, cy - roadWidth/2);
            ctx.moveTo(cx, cy + roadWidth/2); ctx.lineTo(cx, canvas.height);
            ctx.moveTo(0, cy); ctx.lineTo(cx - roadWidth/2, cy);
            ctx.moveTo(cx + roadWidth/2, cy); ctx.lineTo(canvas.width, cy);
            ctx.stroke();
            ctx.setLineDash([]);

            ctx.fillStyle = currentGreen === 'NS' ? '#22c55e' : '#ef4444';
            ctx.beginPath();
            ctx.arc(cx - 60, cy - 60, 10, 0, Math.PI * 2);
            ctx.arc(cx + 60, cy + 60, 10, 0, Math.PI * 2);
            ctx.fill();

            ctx.fillStyle = currentGreen === 'EW' ? '#22c55e' : '#ef4444';
            ctx.beginPath();
            ctx.arc(cx + 60, cy - 60, 10, 0, Math.PI * 2);
            ctx.arc(cx - 60, cy + 60, 10, 0, Math.PI * 2);
            ctx.fill();

            drawQueueCars('N', cx - 25, cy - roadWidth/2 - 15, 0, -18);
            drawQueueCars('S', cx + 25, cy + roadWidth/2 + 15, 0, 18);
            drawQueueCars('W', cx - roadWidth/2 - 15, cy + 25, -18, 0);
            drawQueueCars('E', cx + roadWidth/2 + 15, cy - 25, 18, 0);
        }

        function drawQueueCars(dir, startX, startY, stepX, stepY) {
            let count = queues[dir].length;
            ctx.fillStyle = (dir === 'N' || dir === 'S') ? '#38bdf8' : '#eab308';
            for (let i = 0; i < Math.min(count, 12); i++) {
                ctx.beginPath();
                ctx.arc(startX + i * stepX, startY + i * stepY, 6, 0, Math.PI * 2);
                ctx.fill();
            }
            if (count > 12) {
                ctx.fillStyle = '#ffffff';
                ctx.font = '10px sans-serif';
                ctx.fillText(`+${count - 12}`, startX + 12 * stepX - 5, startY + 12 * stepY + 3);
            }
        }

        function gameLoop() {
            let now = performance.now();
            let dt = (now - lastTime) / 1000;
            lastTime = now;

            updateSimulation(dt);
            draw();

            requestAnimationFrame(gameLoop);
        }

        updateSliders();
        resetSim();
        gameLoop();
    </script>
</body>
</html>
```