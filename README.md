# hellaludo<!DOCTYPE html>
<html lang="ur">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Hella Ludo - Yalla Style</title>
    <style>
        * { box-sizing: border-box; margin: 0; padding: 0; font-family: Arial, sans-serif; user-select: none; }
        body { background: #061814; color: #fff; height: 100vh; display: flex; flex-direction: column; overflow: hidden; }

        /* Yalla Header */
        header { background: linear-gradient(180deg, #0f3d33, #08211b); padding: 10px 15px; display: flex; justify-content: space-between; align-items: center; border-bottom: 2px solid #00ffcc; }
        .user-box { display: flex; align-items: center; gap: 8px; }
        .avatar { width: 40px; height: 40px; border-radius: 50%; border: 2px solid #ffcc00; background: #008866; display: flex; align-items: center; justify-content: center; font-weight: bold; }
        .wallet { display: flex; gap: 10px; }
        .chip { background: rgba(0,0,0,0.6); border: 1px solid #00ffcc; padding: 4px 10px; border-radius: 12px; font-size: 12px; color: #ffcc00; }

        /* Views */
        .view { display: none; flex: 1; flex-direction: column; align-items: center; justify-content: center; padding: 10px; }
        .view.active { display: flex; }

        /* Dashboard Items */
        .mode-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 15px; width: 100%; max-width: 400px; }
        .yalla-card { background: linear-gradient(145deg, #124d40, #0a2922); border: 2px solid #00ffcc; border-radius: 15px; padding: 25px 10px; text-align: center; box-shadow: 0 4px 15px rgba(0,255,204,0.2); }
        .yalla-card:active { transform: scale(0.95); }
        .yalla-card h3 { color: #fff; font-size: 16px; margin-top: 8px; }

        /* LUDO BOARD CANVAS */
        #ludo-container { display: flex; flex-direction: column; align-items: center; gap: 10px; }
        canvas { background: #fff; border: 4px solid #333; border-radius: 8px; box-shadow: 0 0 20px rgba(0,0,0,0.8); }

        /* Controls */
        .game-controls { display: flex; align-items: center; justify-content: space-between; width: 100%; max-width: 320px; background: #0a2922; padding: 10px; border-radius: 10px; border: 1px solid #00ffcc; }
        .dice-btn { background: #ffcc00; color: #000; font-weight: bold; padding: 10px 20px; border-radius: 8px; border: none; font-size: 16px; }
        .dice-val { font-size: 24px; font-weight: bold; color: #00ffcc; width: 40px; text-align: center; }

        /* Nav */
        nav { background: #04120f; display: flex; justify-content: space-around; padding: 12px 0; border-top: 1px solid #124d40; }
        .nav-btn { color: #888; text-decoration: none; font-size: 12px; display: flex; flex-direction: column; align-items: center; }
        .nav-btn.active { color: #00ffcc; font-weight: bold; }
    </style>
</head>
<body>

    <!-- Header -->
    <header>
        <div class="user-box">
            <div class="avatar">👑</div>
            <div>
                <div style="font-size:13px; font-weight:bold;">Yalla Player</div>
                <div style="font-size:10px; color:#00ffcc;">ID: 77890</div>
            </div>
        </div>
        <div class="wallet">
            <div class="chip">💎 1,000</div>
            <div class="chip">🪙 50K</div>
        </div>
    </header>

    <!-- DASHBOARD VIEW -->
    <div id="dashboard-view" class="view active">
        <h2 style="color:#00ffcc; margin-bottom:20px;">🟡 YALLA BATTLE ARENA 🔴</h2>
        <div class="mode-grid">
            <div class="yalla-card" onclick="startGame('2P')">
                <div style="font-size:35px;">🎲</div>
                <h3>2 Players</h3>
                <span style="font-size:10px; color:#00ffcc;">Quick Match</span>
            </div>
            <div class="yalla-card" onclick="startGame('4P')">
                <div style="font-size:35px;">👥</div>
                <h3>4 Players</h3>
                <span style="font-size:10px; color:#00ffcc;">Classic Ludo</span>
            </div>
        </div>
    </div>

    <!-- GAMEPLAY VIEW -->
    <div id="game-view" class="view">
        <div id="ludo-container">
            <div style="display:flex; justify-content:space-between; width:100%; max-width:320px;">
                <span id="turn-txt" style="color:#00ffcc; font-weight:bold;">Turn: Red</span>
                <button onclick="exitGame()" style="background:#ff3333; color:#fff; border:none; padding:4px 8px; border-radius:4px;">Quit</button>
            </div>
            <canvas id="board" width="320" height="320"></canvas>
            <div class="game-controls">
                <div>Player Control</div>
                <div id="dice-display" class="dice-val">🎲</div>
                <button class="dice-btn" onclick="rollDice()">ROLL</button>
            </div>
        </div>
    </div>

    <!-- Bottom Nav -->
    <nav>
        <div class="nav-btn active" onclick="exitGame()">🏠 Home</div>
        <div class="nav-btn">🏆 Rank</div>
        <div class="nav-btn">🛒 Shop</div>
    </nav>

    <script>
        const canvas = document.getElementById('board');
        const ctx = canvas.getContext('2d');
        let currentDice = 1;
        let turn = 'Red';

        // Draw Basic Ludo Board
        function drawBoard() {
            ctx.clearRect(0,0,320,320);
            
            // Base Grid
            let size = 320 / 15;
            
            // Homes
            ctx.fillStyle = "#ff3333"; ctx.fillRect(0, 0, size*6, size*6); // Red
            ctx.fillStyle = "#3388ff"; ctx.fillRect(size*9, 0, size*6, size*6); // Blue
            ctx.fillStyle = "#ffcc00"; ctx.fillRect(0, size*9, size*6, size*6); // Yellow
            ctx.fillStyle = "#33cc33"; ctx.fillRect(size*9, size*9, size*6, size*6); // Green

            // Center Home
            ctx.fillStyle = "#fff"; ctx.fillRect(size*6, size*6, size*3, size*3);
            
            // Inner Boxes
            ctx.fillStyle = "#fff";
            ctx.fillRect(size, size, size*4, size*4);
            ctx.fillRect(size*10, size, size*4, size*4);
            ctx.fillRect(size, size*10, size*4, size*4);
            ctx.fillRect(size*10, size*10, size*4, size*4);

            // Draw Tokens (Demo)
            ctx.fillStyle = "#ff3333";
            ctx.beginPath(); ctx.arc(size*2.5, size*2.5, size*0.8, 0, Math.PI*2); ctx.fill();
            
            ctx.fillStyle = "#3388ff";
            ctx.beginPath(); ctx.arc(size*12.5, size*2.5, size*0.8, 0, Math.PI*2); ctx.fill();
        }

        function startGame(mode) {
            document.getElementById('dashboard-view').classList.remove('active');
            document.getElementById('game-view').classList.add('active');
            drawBoard();
        }

        function exitGame() {
            document.getElementById('game-view').classList.remove('active');
            document.getElementById('dashboard-view').classList.add('active');
        }

        function rollDice() {
            currentDice = Math.floor(Math.random() * 6) + 1;
            document.getElementById('dice-display').innerText = currentDice;
            turn = turn === 'Red' ? 'Blue' : 'Red';
            document.getElementById('turn-txt').innerText = "Turn: " + turn;
        }
    </script>
</body>
</html>
