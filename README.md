# Sugawara-game
Juego matemático 
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Sugawara - Juego de Matemáticas</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Arial', sans-serif;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            min-height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
            overflow-x: auto;
        }

        .game-container {
            background: rgba(255, 255, 255, 0.95);
            border-radius: 20px;
            padding: 15px;
            box-shadow: 0 20px 40px rgba(0,0,0,0.3);
            max-width: 1400px;
            width: 95%;
            position: relative;
            margin: 10px;
        }

        @media (max-width: 768px) {
            .game-container {
                padding: 10px;
                width: 98%;
            }
            
            .game-board {
                grid-template-columns: repeat(7, 1fr);
            }
            
            .game-info {
                grid-template-columns: 1fr;
            }
        }

        .game-title {
            text-align: center;
            font-size: 2.5rem;
            background: linear-gradient(45deg, #FF6B6B, #4ECDC4, #45B7D1);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            margin-bottom: 20px;
            text-shadow: 2px 2px 4px rgba(0,0,0,0.3);
        }

        .game-board {
            display: grid;
            grid-template-columns: repeat(11, 1fr);
            gap: 2px;
            background: linear-gradient(45deg, #2E8B57, #228B22);
            padding: 8px;
            border-radius: 15px;
            border: 3px solid #FFD700;
            position: relative;
            max-width: 100%;
            overflow: hidden;
        }

        .cell {
            width: 100%;
            height: 0;
            padding-bottom: 100%; /* Mantiene aspecto cuadrado */
            border: 1px solid #333;
            border-radius: 6px;
            position: relative;
            cursor: pointer;
            transition: all 0.3s ease;
            background: #f8f9fa;
            min-width: 0; /* Permite que se comprima */
        }

        .cell-content {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            display: flex;
            align-items: center;
            justify-content: center;
            font-weight: bold;
            font-size: clamp(8px, 1.5vw, 12px);
            color: #333;
        }

        .cell:hover {
            transform: scale(1.05);
            z-index: 10;
        }

        .cell.start .cell-content { background: #28a745; color: white; border-radius: 6px; }
        .cell.finish .cell-content { background: #dc3545; color: white; border-radius: 6px; }
        .cell.special .cell-content { background: #ffc107; color: #333; border-radius: 6px; }
        .cell.trap .cell-content { background: #6f42c1; color: white; border-radius: 6px; }
        .cell.bonus .cell-content { background: #17a2b8; color: white; border-radius: 6px; }

        .player {
            width: 16px;
            height: 16px;
            border-radius: 50%;
            position: absolute;
            border: 2px solid white;
            z-index: 5;
            transition: all 0.5s ease;
        }

        .player1 { background: #FF4757; top: 2px; left: 2px; }
        .player2 { background: #3742fa; top: 2px; right: 2px; }
        .player3 { background: #2ed573; bottom: 2px; left: 2px; }
        .player4 { background: #ffa502; bottom: 2px; right: 2px; }

        .game-info {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 20px;
            margin-top: 20px;
        }

        .players-panel, .game-controls {
            background: rgba(255, 255, 255, 0.9);
            padding: 15px;
            border-radius: 10px;
            border: 2px solid #ddd;
        }

        .player-info {
            margin-bottom: 10px;
            padding: 10px;
            border-radius: 8px;
            border-left: 4px solid;
            background: rgba(255, 255, 255, 0.8);
        }

        .player-info.active {
            background: rgba(255, 215, 0, 0.3);
            transform: scale(1.02);
        }

        .player-info.player1 { border-left-color: #FF4757; }
        .player-info.player2 { border-left-color: #3742fa; }
        .player-info.player3 { border-left-color: #2ed573; }
        .player-info.player4 { border-left-color: #ffa502; }

        .dice-container {
            text-align: center;
            margin: 20px 0;
        }

        .dice {
            width: 80px;
            height: 80px;
            background: linear-gradient(45deg, #fff, #f0f0f0);
            border: 3px solid #333;
            border-radius: 15px;
            display: inline-flex;
            align-items: center;
            justify-content: center;
            font-size: 2rem;
            font-weight: bold;
            margin: 0 10px;
            box-shadow: 0 4px 8px rgba(0,0,0,0.3);
            transition: all 0.3s ease;
            cursor: pointer;
        }

        .dice:hover {
            transform: rotateX(15deg) rotateY(15deg);
        }

        .dice.rolling {
            animation: roll 1s ease-in-out;
        }

        @keyframes roll {
            0%, 100% { transform: rotate(0deg); }
            25% { transform: rotate(90deg); }
            50% { transform: rotate(180deg); }
            75% { transform: rotate(270deg); }
        }

        .btn {
            padding: 12px 24px;
            border: none;
            border-radius: 8px;
            font-size: 16px;
            font-weight: bold;
            cursor: pointer;
            transition: all 0.3s ease;
            margin: 5px;
        }

        .btn-primary {
            background: linear-gradient(45deg, #4CAF50, #45a049);
            color: white;
        }

        .btn-primary:hover {
            transform: translateY(-2px);
            box-shadow: 0 4px 8px rgba(0,0,0,0.3);
        }

        .btn:disabled {
            opacity: 0.6;
            cursor: not-allowed;
            transform: none !important;
        }

        .modal {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0,0,0,0.8);
            display: flex;
            align-items: center;
            justify-content: center;
            z-index: 1000;
        }

        .modal-content {
            background: white;
            padding: 30px;
            border-radius: 15px;
            max-width: 500px;
            width: 90%;
            text-align: center;
            box-shadow: 0 20px 40px rgba(0,0,0,0.3);
        }

        .modal h3 {
            color: #333;
            margin-bottom: 15px;
            font-size: 1.5rem;
        }

        .modal p {
            color: #666;
            margin-bottom: 20px;
            line-height: 1.6;
        }

        .answer-input {
            width: 100%;
            padding: 15px;
            border: 2px solid #ddd;
            border-radius: 8px;
            font-size: 18px;
            text-align: center;
            margin-bottom: 20px;
        }

        .hidden { display: none; }

        .role-badge {
            display: inline-block;
            padding: 4px 8px;
            border-radius: 12px;
            font-size: 12px;
            font-weight: bold;
            margin-left: 10px;
        }

        .role-none { background: #6c757d; color: white; }
        .role-jackpot { background: #ffd700; color: #333; }
        .role-protegido { background: #007bff; color: white; }
        .role-daniel { background: #28a745; color: white; }

        .ability-cooldown {
            font-size: 10px;
            color: #666;
            margin-top: 5px;
        }

        .game-log {
            height: 200px;
            overflow-y: auto;
            background: rgba(0,0,0,0.1);
            padding: 10px;
            border-radius: 8px;
            margin-top: 15px;
            font-size: 12px;
        }

        .log-entry {
            margin-bottom: 5px;
            padding: 2px 5px;
            border-radius: 3px;
            background: rgba(255,255,255,0.5);
        }
    </style>
</head>
<body>
    <div class="game-container">
        <h1 class="game-title">🎲 SUGAWARA 🎲</h1>
        
        <div class="game-board" id="gameBoard"></div>
        
        <div class="game-info">
            <div class="players-panel">
                <h3>🎮 Jugadores</h3>
                <div id="playersInfo"></div>
                <div class="game-log" id="gameLog"></div>
            </div>
            
            <div class="game-controls">
                <div class="dice-container">
                    <div class="dice" id="dice">🎲</div>
                    <div>
                        <button class="btn btn-primary" id="rollDice">🎲 Tirar Dado</button>
                    </div>
                </div>
                
                <div id="gameControls">
                    <button class="btn btn-primary" id="startGame">🚀 Iniciar Juego</button>
                    <button class="btn btn-primary" id="resetGame">🔄 Reiniciar</button>
                </div>

                <div id="specialActions" class="hidden">
                    <h4>Habilidades Especiales:</h4>
                    <button class="btn" id="skipQuestion" style="background: gold;">⚡ Saltar Pregunta</button>
                    <button class="btn" id="useGun" style="background: red; color: white;">🔫 Usar Pistola</button>
                    <select id="gunTarget" class="hidden">
                        <option value="">Seleccionar objetivo...</option>
                    </select>
                </div>
            </div>
        </div>
    </div>

    <!-- Modal para preguntas -->
    <div class="modal hidden" id="questionModal">
        <div class="modal-content">
            <h3 id="questionTitle">Pregunta Matemática</h3>
            <p id="questionText"></p>
            <input type="number" class="answer-input" id="answerInput" placeholder="Tu respuesta...">
            <div id="answerReveal" class="hidden" style="color: green; font-weight: bold; margin: 10px 0;"></div>
            <div>
                <button class="btn btn-primary" id="submitAnswer">✓ Responder</button>
                <button class="btn" id="cancelQuestion" style="background: #dc3545; color: white;">✗ Cancelar</button>
            </div>
        </div>
    </div>

    <!-- Modal para selección de objetivo -->
    <div class="modal hidden" id="targetModal">
        <div class="modal-content">
            <h3>🔫 Seleccionar Objetivo</h3>
            <p>Elige a qué jugador disparar:</p>
            <div id="targetButtons"></div>
        </div>
    </div>

    <script>
        class SugawaraGame {
            constructor() {
                this.players = [
                    { id: 1, name: 'Jugador 1', position: 0, role: 'none', color: '#FF4757', abilities: {} },
                    { id: 2, name: 'Jugador 2', position: 0, role: 'none', color: '#3742fa', abilities: {} },
                    { id: 3, name: 'Jugador 3', position: 0, role: 'none', color: '#2ed573', abilities: {} },
                    { id: 4, name: 'Jugador 4', position: 0, role: 'none', color: '#ffa502', abilities: {} }
                ];
                
                this.currentPlayer = 0;
                this.gameStarted = false;
                this.totalCells = 77;
                this.currentQuestion = null;
                this.gameLog = [];
                
                this.roles = ['none', 'jackpot', 'protegido', 'daniel'];
                this.roleWeights = [40, 10, 25, 25]; // Probabilidades
                
                this.questions = {
                    easy: [
                        { q: "¿Cuánto es 5 + 3?", a: 8 },
                        { q: "¿Cuánto es 12 - 7?", a: 5 },
                        { q: "¿Cuánto es 4 × 3?", a: 12 },
                        { q: "¿Cuánto es 15 ÷ 3?", a: 5 },
                        { q: "¿Cuánto es 8 + 6?", a: 14 }
                    ],
                    medium: [
                        { q: "¿Cuánto es 7 × 8?", a: 56 },
                        { q: "¿Cuánto es 144 ÷ 12?", a: 12 },
                        { q: "¿Cuánto es 25 + 37?", a: 62 },
                        { q: "¿Cuánto es 100 - 43?", a: 57 },
                        { q: "¿Cuánto es 9 × 6?", a: 54 }
                    ],
                    hard: [
                        { q: "¿Cuánto es 17 × 13?", a: 221 },
                        { q: "¿Cuánto es 256 ÷ 16?", a: 16 },
                        { q: "¿Cuánto es 89 + 156?", a: 245 },
                        { q: "¿Cuánto es 15² ?", a: 225 },
                        { q: "¿Cuánto es √196 ?", a: 14 }
                    ]
                };
                
                this.initializeGame();
            }
            
            initializeGame() {
                this.createBoard();
                this.setupEventListeners();
                this.assignRoles();
                this.updateDisplay();
                this.addLog("¡Bienvenido a Sugawara! 🎮");
            }
            
            createBoard() {
                const board = document.getElementById('gameBoard');
                board.innerHTML = '';
                
                for (let i = 0; i <= this.totalCells; i++) {
                    const cell = document.createElement('div');
                    cell.className = 'cell';
                    cell.id = `cell-${i}`;
                    
                    const cellContent = document.createElement('div');
                    cellContent.className = 'cell-content';
                    
                    if (i === 0) {
                        cell.classList.add('start');
                        cellContent.textContent = 'START';
                    } else if (i === this.totalCells) {
                        cell.classList.add('finish');
                        cellContent.textContent = 'META';
                    } else {
                        cellContent.textContent = i;
                        
                        // Asignar tipos especiales de casillas
                        const rand = Math.random();
                        if (rand < 0.15) {
                            cell.classList.add('special');
                            cell.title = 'Casilla Especial - Pregunta Matemática';
                        } else if (rand < 0.25) {
                            cell.classList.add('trap');
                            cell.title = 'Trampa - Pregunta Difícil';
                        } else if (rand < 0.35) {
                            cell.classList.add('bonus');
                            cell.title = 'Bonus - Pregunta Fácil';
                        }
                    }
                    
                    cell.appendChild(cellContent);
                    board.appendChild(cell);
                }
            }
            
            assignRoles() {
                this.players.forEach(player => {
                    const rand = Math.random() * 100;
                    let cumulative = 0;
                    
                    for (let i = 0; i < this.roles.length; i++) {
                        cumulative += this.roleWeights[i];
                        if (rand < cumulative) {
                            player.role = this.roles[i];
                            break;
                        }
                    }
                    
                    // Inicializar habilidades
                    player.abilities = {
                        skipCooldown: 0,
                        gunCooldown: 0,
                        danielCooldown: 0,
                        protectedTurns: 0,
                        frozenTurns: 0
                    };
                });
            }
            
            setupEventListeners() {
                document.getElementById('startGame').addEventListener('click', () => this.startGame());
                document.getElementById('resetGame').addEventListener('click', () => this.resetGame());
                document.getElementById('rollDice').addEventListener('click', () => this.rollDice());
                document.getElementById('submitAnswer').addEventListener('click', () => this.submitAnswer());
                document.getElementById('cancelQuestion').addEventListener('click', () => this.cancelQuestion());
                document.getElementById('skipQuestion').addEventListener('click', () => this.useSkip());
                document.getElementById('useGun').addEventListener('click', () => this.showGunTargets());
                
                // Enter key para responder
                document.getElementById('answerInput').addEventListener('keypress', (e) => {
                    if (e.key === 'Enter') this.submitAnswer();
                });
            }
            
            startGame() {
                this.gameStarted = true;
                this.addLog("¡El juego ha comenzado! 🚀");
                this.updateDisplay();
                this.playSound('start');
            }
            
            resetGame() {
                this.players.forEach(player => {
                    player.position = 0;
                    player.abilities = {
                        skipCooldown: 0,
                        gunCooldown: 0,
                        danielCooldown: 0,
                        protectedTurns: 0,
                        frozenTurns: 0
                    };
                });
                
                this.currentPlayer = 0;
                this.gameStarted = false;
                this.gameLog = [];
                this.assignRoles();
                this.updateDisplay();
                this.addLog("Juego reiniciado 🔄");
            }
            
            rollDice() {
                if (!this.gameStarted) return;
                
                const currentP = this.players[this.currentPlayer];
                
                // Verificar si está congelado
                if (currentP.abilities.frozenTurns > 0) {
                    currentP.abilities.frozenTurns--;
                    this.addLog(`${currentP.name} está congelado. Pierde el turno. ❄️`);
                    this.nextPlayer();
                    return;
                }
                
                const dice = document.getElementById('dice');
                dice.classList.add('rolling');
                
                setTimeout(() => {
                    const roll = Math.floor(Math.random() * 6) + 1;
                    dice.textContent = roll;
                    dice.classList.remove('rolling');
                    
                    this.movePlayer(this.currentPlayer, roll);
                    this.playSound('dice');
                }, 1000);
            }
            
            movePlayer(playerIndex, spaces) {
                const player = this.players[playerIndex];
                const oldPosition = player.position;
                player.position = Math.min(player.position + spaces, this.totalCells);
                
                this.addLog(`${player.name} avanza ${spaces} casillas (${oldPosition} → ${player.position})`);
                
                // Verificar si llegó a la meta
                if (player.position === this.totalCells) {
                    this.endGame(playerIndex);
                    return;
                }
                
                this.updateDisplay();
                
                // Verificar tipo de casilla
                setTimeout(() => {
                    this.checkCellType(playerIndex, player.position);
                }, 500);
            }
            
            checkCellType(playerIndex, position) {
                const cell = document.getElementById(`cell-${position}`);
                const player = this.players[playerIndex];
                
                if (cell.classList.contains('special')) {
                    this.addLog(`${player.name} cayó en una casilla especial! ⭐`);
                    this.askQuestion('medium', playerIndex);
                } else if (cell.classList.contains('trap')) {
                    this.addLog(`${player.name} cayó en una trampa! 🪤`);
                    this.askQuestion('hard', playerIndex);
                } else if (cell.classList.contains('bonus')) {
                    this.addLog(`${player.name} cayó en un bonus! 🎁`);
                    this.askQuestion('easy', playerIndex);
                } else {
                    this.nextPlayer();
                }
            }
            
            askQuestion(difficulty, playerIndex) {
                const questions = this.questions[difficulty];
                const question = questions[Math.floor(Math.random() * questions.length)];
                
                this.currentQuestion = {
                    question: question,
                    difficulty: difficulty,
                    playerIndex: playerIndex
                };
                
                document.getElementById('questionTitle').textContent = 
                    `Pregunta ${difficulty.charAt(0).toUpperCase() + difficulty.slice(1)} - ${this.players[playerIndex].name}`;
                document.getElementById('questionText').textContent = question.q;
                document.getElementById('answerInput').value = '';
                document.getElementById('answerReveal').classList.add('hidden');
                
                // Habilidad de Daniel Carreón
                const player = this.players[playerIndex];
                if (player.role === 'daniel' && player.abilities.danielCooldown === 0) {
                    player.abilities.danielCooldown = 4;
                    document.getElementById('answerReveal').textContent = `Respuesta: ${question.a}`;
                    document.getElementById('answerReveal').classList.remove('hidden');
                    
                    setTimeout(() => {
                        document.getElementById('answerReveal').classList.add('hidden');
                    }, 2500);
                    
                    this.addLog(`Daniel Carreón ayuda mostrando la respuesta! 🧙‍♂️`);
                }
                
                document.getElementById('questionModal').classList.remove('hidden');
            }
            
            submitAnswer() {
                const answer = parseInt(document.getElementById('answerInput').value);
                const correct = answer === this.currentQuestion.question.a;
                const player = this.players[this.currentQuestion.playerIndex];
                
                document.getElementById('questionModal').classList.add('hidden');
                
                if (correct) {
                    this.addLog(`${player.name} respondió correctamente! ✅`);
                    this.playSound('correct');
                } else {
                    this.addLog(`${player.name} respondió incorrectamente. ❌`);
                    this.playSound('wrong');
                    
                    // Calcular retroceso
                    let penalty = this.currentQuestion.difficulty === 'hard' ? 3 : 
                                 this.currentQuestion.difficulty === 'medium' ? 2 : 1;
                    
                    // Rol Jackpot multiplica por 3 el castigo
                    if (player.role === 'jackpot') {
                        penalty *= 3;
                        this.addLog(`¡Jackpot multiplica el castigo por 3! 💥`);
                    }
                    
                    // Rol Protegido puede usar escudo
                    if (player.role === 'protegido' && player.abilities.protectedTurns === 0) {
                        player.abilities.protectedTurns = 3;
                        this.addLog(`${player.name} usa su escudo protector! 🛡️`);
                    } else if (player.role === 'protegido' && player.abilities.protectedTurns > 0) {
                        penalty = 0;
                        this.addLog(`${player.name} está protegido! No retrocede. 🛡️`);
                    }
                    
                    player.position = Math.max(0, player.position - penalty);
                    if (penalty > 0) {
                        this.addLog(`${player.name} retrocede ${penalty} casillas.`);
                    }
                }
                
                this.updateDisplay();
                this.nextPlayer();
            }
            
            cancelQuestion() {
                document.getElementById('questionModal').classList.add('hidden');
                this.nextPlayer();
            }
            
            useSkip() {
                const player = this.players[this.currentPlayer];
                if (player.role !== 'jackpot' || player.abilities.skipCooldown > 0) return;
                
                player.abilities.skipCooldown = 2;
                this.addLog(`${player.name} usa su habilidad para saltar la pregunta! ⚡`);
                
                // Posibilidad de pregunta difícil
                if (Math.random() < 0.3) {
                    this.addLog(`¡Oh no! ${player.name} recibe una pregunta difícil por saltar! 😰`);
                    this.askQuestion('hard', this.currentPlayer);
                } else {
                    this.nextPlayer();
                }
                
                this.updateDisplay();
            }
            
            showGunTargets() {
                const player = this.players[this.currentPlayer];
                if (player.role !== 'jackpot' || player.abilities.gunCooldown > 0) return;
                
                const targetButtons = document.getElementById('targetButtons');
                targetButtons.innerHTML = '';
                
                this.players.forEach((p, index) => {
                    if (index !== this.currentPlayer) {
                        const btn = document.createElement('button');
                        btn.className = 'btn btn-primary';
                        btn.textContent = p.name;
                        btn.style.margin = '5px';
                        btn.onclick = () => this.fireGun(index);
                        targetButtons.appendChild(btn);
                    }
                });
                
                document.getElementById('targetModal').classList.remove('hidden');
            }
            
            fireGun(targetIndex) {
                document.getElementById('targetModal').classList.add('hidden');
                
                const shooter = this.players[this.currentPlayer];
                const target = this.players[targetIndex];
                
                shooter.abilities.gunCooldown = 5;
                
                const rand = Math.random();
                
                if (rand < 0.20) {
                    // Pistola explota
                    shooter.abilities.frozenTurns = 1;
                    this.addLog(`¡La pistola de ${shooter.name} explota! Pierde 1 turno. 💥`);
                    this.playSound('explosion');
                } else if (rand < 0.40) {
                    // Pistola no dispara
                    this.addLog(`La pistola de ${shooter.name} no disparó. 🔫💨`);
                } else {
                    // Disparo exitoso
                    target.position = Math.max(0, target.position - 3);
                    this.addLog(`${shooter.name} dispara a ${target.name}! Retrocede 3 casillas. 🔫💥`);
                    this.playSound('gunshot');
                }
                
                this.updateDisplay();
                this.nextPlayer();
            }
            
            nextPlayer() {
                // Reducir cooldowns
                this.players.forEach(player => {
                    if (player.abilities.skipCooldown > 0) player.abilities.skipCooldown--;
                    if (player.abilities.gunCooldown > 0) player.abilities.gunCooldown--;
                    if (player.abilities.danielCooldown > 0) player.abilities.danielCooldown--;
                    if (player.abilities.protectedTurns > 0) player.abilities.protectedTurns--;
                });
                
                this.currentPlayer = (this.currentPlayer + 1) % 4;
                this.updateDisplay();
            }
            
            updateDisplay() {
                // Actualizar posiciones de jugadores
                document.querySelectorAll('.player').forEach(p => p.remove());
                
                this.players.forEach((player, index) => {
                    const cell = document.getElementById(`cell-${player.position}`);
                    if (cell) {
                        const playerElement = document.createElement('div');
                        playerElement.className = `player player${index + 1}`;
                        cell.appendChild(playerElement);
                    }
                });
                
                // Actualizar panel de jugadores
                const playersInfo = document.getElementById('playersInfo');
                playersInfo.innerHTML = '';
                
                this.players.forEach((player, index) => {
                    const playerDiv = document.createElement('div');
                    playerDiv.className = `player-info player${index + 1} ${index === this.currentPlayer ? 'active' : ''}`;
                    
                    const roleText = {
                        none: 'Sin Rol',
                        jackpot: 'Jackpot',
                        protegido: 'Protegido',
                        daniel: 'Daniel Carreón'
                    }[player.role];
                    
                    playerDiv.innerHTML = `
                        <strong>${player.name}</strong>
                        <span class="role-badge role-${player.role}">${roleText}</span>
                        <br>Posición: ${player.position}/${this.totalCells}
                        <div class="ability-cooldown">
                            ${player.role === 'jackpot' ? `Saltar: ${player.abilities.skipCooldown}, Pistola: ${player.abilities.gunCooldown}` : ''}
                            ${player.role === 'daniel' ? `Ayuda: ${player.abilities.danielCooldown}` : ''}
                            ${player.role === 'protegido' ? `Escudo: ${player.abilities.protectedTurns}` : ''}
                            ${player.abilities.frozenTurns > 0 ? `❄️ Congelado: ${player.abilities.frozenTurns}` : ''}
                        </div>
                    `;
                    
                    playersInfo.appendChild(playerDiv);
                });
                
                // Actualizar controles
                const rollButton = document.getElementById('rollDice');
                rollButton.disabled = !this.gameStarted;
                
                // Actualizar habilidades especiales
                const currentP = this.players[this.currentPlayer];
                const specialActions = document.getElementById('specialActions');
                const skipButton = document.getElementById('skipQuestion');
                const gunButton = document.getElementById('useGun');
                
                if (currentP.role === 'jackpot') {
                    specialActions.classList.remove('hidden');
                    skipButton.disabled = currentP.abilities.skipCooldown > 0;
                    gunButton.disabled = currentP.abilities.gunCooldown > 0;
                } else {
                    specialActions.classList.add('hidden');
                }
            }
            
            endGame(winnerIndex) {
                const winner = this.players[winnerIndex];
                this.addLog(`🎉 ¡${winner.name} ha ganado el juego! 🏆`);
                this.gameStarted = false;
                this.playSound('victory');
                
                // Mostrar mensaje de victoria
                setTimeout(() => {
                    alert(`🎉 ¡Felicidades ${winner.name}! 🏆\n\nHas completado el juego Sugawara con el rol: ${winner.role}\n\n¡Excelente trabajo resolviendo los problemas matemáticos!`);
                }, 1000);
                
                this.updateDisplay();
            }
            
            addLog(message) {
                const timestamp = new Date().toLocaleTimeString();
                this.gameLog.unshift(`[${timestamp}] ${message}`);
                
                if (this.gameLog.length > 50) {
                    this.gameLog = this.gameLog.slice(0, 50);
                }
                
                const logElement = document.getElementById('gameLog');
                const entry = document.createElement('div');
                entry.className = 'log-entry';
                entry.textContent = `[${timestamp}] ${message}`;
                
                logElement.insertBefore(entry, logElement.firstChild);
                
                // Mantener máximo 50 entradas
                while (logElement.children.length > 50) {
                    logElement.removeChild(logElement.lastChild);
                }
            }
            
            playSound(type) {
                // Crear sonidos usando Web Audio API
                const audioContext = new (window.AudioContext || window.webkitAudioContext)();
                
                let frequency, duration;
                
                switch(type) {
                    case 'dice':
                        frequency = 440;
                        duration = 0.1;
                        break;
                    case 'correct':
                        frequency = 800;
                        duration = 0.3;
                        break;
                    case 'wrong':
                        frequency = 200;
                        duration = 0.5;
                        break;
                    case 'start':
                        frequency = 600;
                        duration = 0.8;
                        break;
                    case 'victory':
                        frequency = 1000;
                        duration = 1.0;
                        break;
                    case 'gunshot':
                        frequency = 150;
                        duration = 0.2;
                        break;
                    case 'explosion':
                        frequency = 100;
                        duration = 0.8;
                        break;
                    default:
                        return;
                }
                
                try {
                    const oscillator = audioContext.createOscillator();
                    const gainNode = audioContext.createGain();
                    
                    oscillator.connect(gainNode);
                    gainNode.connect(audioContext.destination);
                    
                    oscillator.frequency.setValueAtTime(frequency, audioContext.currentTime);
                    oscillator.type = type === 'explosion' ? 'sawtooth' : 'sine';
                    
                    gainNode.gain.setValueAtTime(0.1, audioContext.currentTime);
                    gainNode.gain.exponentialRampToValueAtTime(0.01, audioContext.currentTime + duration);
                    
                    oscillator.start(audioContext.currentTime);
                    oscillator.stop(audioContext.currentTime + duration);
                } catch (e) {
                    console.log('Audio no disponible');
                }
            }
        }
        
        // Inicializar el juego cuando se carga la página
        window.addEventListener('DOMContentLoaded', () => {
            const game = new SugawaraGame();
            
            // Agregar efectos visuales adicionales
            document.querySelectorAll('.cell').forEach(cell => {
                cell.addEventListener('mouseenter', () => {
                    cell.style.transform = 'scale(1.1)';
                    cell.style.zIndex = '10';
                });
                
                cell.addEventListener('mouseleave', () => {
                    cell.style.transform = 'scale(1)';
                    cell.style.zIndex = '1';
                });
            });
            
            // Efectos de partículas para celebraciones
            function createParticles() {
                for (let i = 0; i < 20; i++) {
                    const particle = document.createElement('div');
                    particle.style.position = 'fixed';
                    particle.style.width = '10px';
                    particle.style.height = '10px';
                    particle.style.background = ['#FF6B6B', '#4ECDC4', '#45B7D1', '#96CEB4', '#FFEAA7'][Math.floor(Math.random() * 5)];
                    particle.style.borderRadius = '50%';
                    particle.style.pointerEvents = 'none';
                    particle.style.zIndex = '9999';
                    particle.style.left = Math.random() * window.innerWidth + 'px';
                    particle.style.top = window.innerHeight + 'px';
                    
                    document.body.appendChild(particle);
                    
                    particle.animate([
                        { transform: 'translateY(0px) rotate(0deg)', opacity: 1 },
                        { transform: `translateY(-${window.innerHeight + 100}px) rotate(360deg)`, opacity: 0 }
                    ], {
                        duration: 3000 + Math.random() * 2000,
                        easing: 'ease-out'
                    }).onfinish = () => particle.remove();
                }
            }
            
            // Agregar partículas en eventos especiales
            const originalAddLog = game.addLog.bind(game);
            game.addLog = function(message) {
                originalAddLog(message);
                
                if (message.includes('ganado') || message.includes('🏆')) {
                    createParticles();
                }
            };
        });
    </script>
</body>
</html>
