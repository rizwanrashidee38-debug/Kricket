# Kricket<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Cricket Division Simulator</title>
    <style>
        body { font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; background: #f0f2f5; display: flex; flex-direction: column; align-items: center; }
        #game-container { background: white; padding: 20px; border-radius: 12px; box-shadow: 0 4px 15px rgba(0,0,0,0.1); width: 90%; max-width: 600px; margin-top: 20px; }
        .hidden { display: none; }
        .tile-grid { display: grid; grid-template-columns: repeat(2, 1fr); gap: 15px; margin-top: 20px; }
        .tile { height: 100px; background: #007bff; color: white; display: flex; align-items: center; justify-content: center; font-size: 24px; cursor: pointer; border-radius: 8px; transition: transform 0.2s; }
        .tile:hover { transform: scale(1.05); background: #0056b3; }
        .stats-bar { display: flex; justify-content: space-between; background: #333; color: white; padding: 10px; border-radius: 5px; margin-bottom: 10px; }
        .log { height: 100px; overflow-y: auto; border: 1px solid #ddd; padding: 5px; font-size: 14px; margin-top: 10px; }
    </style>
</head>
<body>

<div id="game-container">
    <!-- Setup Screen -->
    <div id="setup-screen">
        <h2>Cricket Career Creator</h2>
        <input type="text" id="player-name" placeholder="Enter Name"><br><br>
        <select id="player-role">
            <option value="Batsman">Batsman</option>
            <option value="All-Rounder">All-Rounder</option>
            <option value="Bowler">Bowler</option>
        </select>
        <button onclick="startGame()">Start Career</button>
    </div>

    <!-- Match Screen -->
    <div id="match-screen" class="hidden">
        <div class="stats-bar">
            <div id="display-name"></div>
            <div id="display-division">Division: 3</div>
        </div>
        <div id="match-info"></div>
        <div id="score-display" style="font-size: 20px; font-weight: bold; margin: 10px 0;"></div>
        
        <div id="tile-area" class="tile-grid">
            <div class="tile" onclick="handlePlay()">?</div>
            <div class="tile" onclick="handlePlay()">?</div>
            <div class="tile" onclick="handlePlay()">?</div>
            <div class="tile" onclick="handlePlay()">?</div>
        </div>
        
        <div class="log" id="game-log">Welcome to the crease!</div>
    </div>
</div>

<script>
    let player = {
        name: "", role: "", div: 3, 
        totalRuns: 0, totalWickets: 0, matchesPlayed: 0
    };

    let currentMatch = {
        overs: 0, balls: 0, score: 0, wickets: 0, isPlayerBatting: false
    };

    function startGame() {
        player.name = document.getElementById('player-name').value || "Rookie";
        player.role = document.getElementById('player-role').value;
        document.getElementById('setup-screen').classList.add('hidden');
        document.getElementById('match-screen').classList.remove('hidden');
        document.getElementById('display-name').innerText = player.name;
        startNewMatch();
    }

    function startNewMatch() {
        currentMatch = { overs: 0, balls: 0, score: 0, wickets: 0, isPlayerBatting: true };
        updateUI("Toss won! You are batting first.");
    }

    function handlePlay() {
        // 20% Probability of Dismissal
        let outcome = Math.random();
        let logMsg = "";
        
        if (outcome < 0.20) {
            const dismissals = ["Bowled!", "Caught!", "LBW!"];
            logMsg = dismissals[Math.floor(Math.random() * dismissals.length)];
            endInnings();
        } else {
            const runsOptions = [1, 2, 3, 4, 6];
            let runs = runsOptions[Math.floor(Math.random() * runsOptions.length)];
            currentMatch.score += runs;
            player.totalRuns += runs;
            currentMatch.balls++;
            logMsg = `You scored ${runs} runs.`;
        }

        if (currentMatch.balls >= 120) endInnings(); // 20 Overs max
        updateUI(logMsg);
    }

    function endInnings() {
        player.matchesPlayed++;
        alert(`Match Ended! Total Runs this season: ${player.totalRuns}`);
        checkPromotion();
        startNewMatch();
    }

    function checkPromotion() {
        if (player.matchesPlayed >= 10) {
            let promoted = false;
            if (player.role === "Batsman" && player.totalRuns >= 300) promoted = true;
            if (player.role === "All-Rounder" && player.totalRuns >= 200 && player.totalWickets >= 5) promoted = true;
            if (player.role === "Bowler" && player.totalWickets >= 10) promoted = true;

            if (promoted && player.div > 1) {
                player.div--;
                alert(`CONGRATULATIONS! Promoted to Division ${player.div}`);
            } else {
                alert("Season over. Stayed in current division.");
            }
            // Reset season stats
            player.matchesPlayed = 0; player.totalRuns = 0; player.totalWickets = 0;
        }
    }

    function updateUI(msg) {
        document.getElementById('score-display').innerText = `Score: ${currentMatch.score} | Balls: ${currentMatch.balls}`;
        document.getElementById('display-division').innerText = `Division: ${player.div}`;
        let log = document.getElementById('game-log');
        log.innerHTML += `<div>${msg}</div>`;
        log.scrollTop = log.scrollHeight;
    }
</script>
</body>
</html>
