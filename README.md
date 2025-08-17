<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Tic-Tac-Toe Web Application</title>
    <link href="https://cdn.jsdelivr.net/npm/tailwindcss@2.2.19/dist/tailwind.min.css" rel="stylesheet">
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/@fortawesome/fontawesome-free@6.4.0/css/all.min.css">
    <style>
        body {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            min-height: 100vh;
            font-family: 'Arial', sans-serif;
        }
        
        .game-cell {
            transition: all 0.3s ease;
            background: rgba(255, 255, 255, 0.1);
            backdrop-filter: blur(10px);
            border: 2px solid rgba(255, 255, 255, 0.2);
        }
        
        .game-cell:hover {
            background: rgba(255, 255, 255, 0.2);
            transform: scale(1.05);
            border-color: rgba(255, 255, 255, 0.4);
        }
        
        .game-cell.occupied:hover {
            transform: none;
        }
        
        .player-x {
            color: #ff6b6b;
            text-shadow: 0 0 20px rgba(255, 107, 107, 0.5);
        }
        
        .player-o {
            color: #4ecdc4;
            text-shadow: 0 0 20px rgba(78, 205, 196, 0.5);
        }
        
        .winning-cell {
            background: rgba(255, 215, 0, 0.3) !important;
            animation: pulse 1s infinite;
        }
        
        @keyframes pulse {
            0%, 100% { opacity: 1; }
            50% { opacity: 0.7; }
        }
        
        @keyframes celebration {
            0%, 100% { transform: scale(1); }
            25% { transform: scale(1.1) rotate(5deg); }
            75% { transform: scale(1.1) rotate(-5deg); }
        }
        
        .celebrate {
            animation: celebration 0.6s ease-in-out;
        }
        
        .glass-effect {
            background: rgba(255, 255, 255, 0.1);
            backdrop-filter: blur(10px);
            border: 1px solid rgba(255, 255, 255, 0.2);
        }
        
        .mode-button.active {
            background: rgba(255, 255, 255, 0.3);
            border-color: rgba(255, 255, 255, 0.5);
        }
        
        .game-board {
            background: rgba(255, 255, 255, 0.05);
            backdrop-filter: blur(20px);
            border-radius: 20px;
            box-shadow: 0 20px 40px rgba(0, 0, 0, 0.1);
        }
    </style>
</head>
<body class="flex items-center justify-center p-4">
    <div class="max-w-4xl w-full">
        <!-- Header -->
        <div class="text-center mb-8">
            <h1 class="text-5xl font-bold text-white mb-2">
                <i class="fas fa-gamepad mr-4"></i>Tic-Tac-Toe
            </h1>
            <p class="text-white text-lg opacity-80">Challenge a friend or test your skills against AI!</p>
        </div>

        <!-- Game Container -->
        <div class="game-board p-8">
            <!-- Game Mode Selection -->
            <div class="flex justify-center mb-6">
                <div class="glass-effect rounded-lg p-1">
                    <button id="pvp-mode" class="mode-button active px-6 py-3 rounded-lg text-white font-semibold transition-all duration-300">
                        <i class="fas fa-users mr-2"></i>Player vs Player
                    </button>
                    <button id="ai-mode" class="mode-button px-6 py-3 rounded-lg text-white font-semibold transition-all duration-300">
                        <i class="fas fa-robot mr-2"></i>Player vs AI
                    </button>
                </div>
            </div>

            <!-- AI Difficulty Selection (hidden by default) -->
            <div id="difficulty-selector" class="hidden justify-center mb-6">
                <div class="glass-effect rounded-lg p-1">
                    <button class="difficulty-btn active" data-difficulty="easy">Easy</button>
                    <button class="difficulty-btn" data-difficulty="medium">Medium</button>
                    <button class="difficulty-btn" data-difficulty="hard">Hard</button>
                </div>
            </div>

            <div class="grid grid-cols-1 lg:grid-cols-3 gap-8">
                <!-- Game Status and Controls -->
                <div class="lg:col-span-1 order-2 lg:order-1">
                    <!-- Current Game Status -->
                    <div class="glass-effect rounded-lg p-6 mb-6">
                        <h3 class="text-white text-xl font-semibold mb-4">
                            <i class="fas fa-info-circle mr-2"></i>Game Status
                        </h3>
                        <div id="game-status" class="text-white text-lg mb-4">
                            Player X's turn
                        </div>
                        <div id="turn-indicator" class="flex items-center justify-center space-x-4">
                            <div class="player-indicator active" data-player="X">
                                <span class="player-x text-3xl font-bold">X</span>
                            </div>
                            <div class="text-white">VS</div>
                            <div class="player-indicator" data-player="O">
                                <span class="player-o text-3xl font-bold">O</span>
                            </div>
                        </div>
                    </div>

                    <!-- Score Board -->
                    <div class="glass-effect rounded-lg p-6 mb-6">
                        <h3 class="text-white text-xl font-semibold mb-4">
                            <i class="fas fa-trophy mr-2"></i>Score Board
                        </h3>
                        <div class="space-y-3">
                            <div class="flex justify-between items-center">
                                <span class="text-white">Player X Wins:</span>
                                <span id="x-wins" class="player-x font-bold text-xl">0</span>
                            </div>
                            <div class="flex justify-between items-center">
                                <span class="text-white">Player O Wins:</span>
                                <span id="o-wins" class="player-o font-bold text-xl">0</span>
                            </div>
                            <div class="flex justify-between items-center">
                                <span class="text-white">Draws:</span>
                                <span id="draws" class="text-yellow-400 font-bold text-xl">0</span>
                            </div>
                        </div>
                    </div>

                    <!-- Game Controls -->
                    <div class="space-y-3">
                        <button id="reset-btn" class="w-full glass-effect rounded-lg px-6 py-3 text-white font-semibold hover:bg-white hover:bg-opacity-20 transition-all duration-300">
                            <i class="fas fa-redo mr-2"></i>Reset Game
                        </button>
                        <button id="new-game-btn" class="w-full glass-effect rounded-lg px-6 py-3 text-white font-semibold hover:bg-white hover:bg-opacity-20 transition-all duration-300">
                            <i class="fas fa-plus mr-2"></i>New Game
                        </button>
                        <button id="reset-score-btn" class="w-full glass-effect rounded-lg px-6 py-3 text-white font-semibold hover:bg-white hover:bg-opacity-20 transition-all duration-300">
                            <i class="fas fa-eraser mr-2"></i>Reset Score
                        </button>
                    </div>
                </div>

                <!-- Game Board -->
                <div class="lg:col-span-2 order-1 lg:order-2">
                    <div class="flex justify-center">
                        <div id="game-board" class="grid grid-cols-3 gap-4 w-96 h-96">
                            <!-- Game cells will be generated by JavaScript -->
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <!-- Game Result Modal -->
        <div id="result-modal" class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50 hidden">
            <div class="glass-effect rounded-lg p-8 max-w-md w-full mx-4 text-center">
                <div id="result-icon" class="text-6xl mb-4"></div>
                <h2 id="result-title" class="text-3xl font-bold text-white mb-4"></h2>
                <p id="result-message" class="text-white text-lg mb-6"></p>
                <div class="space-y-3">
                    <button id="play-again-btn" class="w-full bg-blue-600 hover:bg-blue-700 text-white font-bold py-3 px-6 rounded-lg transition-all duration-300">
                        <i class="fas fa-play mr-2"></i>Play Again
                    </button>
                    <button id="close-modal-btn" class="w-full glass-effect text-white font-bold py-3 px-6 rounded-lg hover:bg-white hover:bg-opacity-20 transition-all duration-300">
                        <i class="fas fa-times mr-2"></i>Close
                    </button>
                </div>
            </div>
        </div>
    </div>

    <script>
        class TicTacToeGame {
            constructor() {
                this.board = Array(9).fill('');
                this.currentPlayer = 'X';
                this.gameMode = 'pvp'; // 'pvp' or 'ai'
                this.aiDifficulty = 'easy';
                this.gameActive = true;
                this.scores = { X: 0, O: 0, draws: 0 };
                this.winningCombinations = [
                    [0, 1, 2], [3, 4, 5], [6, 7, 8], // rows
                    [0, 3, 6], [1, 4, 7], [2, 5, 8], // columns
                    [0, 4, 8], [2, 4, 6] // diagonals
                ];
                this.init();
            }

            init() {
                this.createBoard();
                this.bindEvents();
                this.updateStatus();
                this.loadScores();
            }

            createBoard() {
                const gameBoard = document.getElementById('game-board');
                gameBoard.innerHTML = '';
                
                for (let i = 0; i < 9; i++) {
                    const cell = document.createElement('div');
                    cell.className = 'game-cell flex items-center justify-center text-6xl font-bold cursor-pointer rounded-lg';
                    cell.dataset.index = i;
                    cell.addEventListener('click', () => this.handleCellClick(i));
                    gameBoard.appendChild(cell);
                }
            }

            bindEvents() {
                // Mode selection
                document.getElementById('pvp-mode').addEventListener('click', () => this.setGameMode('pvp'));
                document.getElementById('ai-mode').addEventListener('click', () => this.setGameMode('ai'));

                // Difficulty selection
                document.querySelectorAll('.difficulty-btn').forEach(btn => {
                    btn.addEventListener('click', (e) => this.setDifficulty(e.target.dataset.difficulty));
                });

                // Control buttons
                document.getElementById('reset-btn').addEventListener('click', () => this.resetGame());
                document.getElementById('new-game-btn').addEventListener('click', () => this.newGame());
                document.getElementById('reset-score-btn').addEventListener('click', () => this.resetScores());

                // Modal buttons
                document.getElementById('play-again-btn').addEventListener('click', () => this.playAgain());
                document.getElementById('close-modal-btn').addEventListener('click', () => this.closeModal());

                // Keyboard support
                document.addEventListener('keydown', (e) => this.handleKeyPress(e));
            }

            setGameMode(mode) {
                this.gameMode = mode;
                
                // Update UI
                document.querySelectorAll('.mode-button').forEach(btn => btn.classList.remove('active'));
                document.getElementById(`${mode === 'pvp' ? 'pvp' : 'ai'}-mode`).classList.add('active');
                
                // Show/hide difficulty selector
                const difficultySelector = document.getElementById('difficulty-selector');
                if (mode === 'ai') {
                    difficultySelector.classList.remove('hidden');
                    difficultySelector.classList.add('flex');
                } else {
                    difficultySelector.classList.add('hidden');
                    difficultySelector.classList.remove('flex');
                }
                
                this.resetGame();
            }

            setDifficulty(difficulty) {
                this.aiDifficulty = difficulty;
                
                // Update UI
                document.querySelectorAll('.difficulty-btn').forEach(btn => {
                    btn.classList.remove('active');
                    if (btn.dataset.difficulty === difficulty) {
                        btn.classList.add('active');
                    }
                });
                
                this.resetGame();
            }

            handleCellClick(index) {
                if (!this.gameActive || this.board[index] !== '') return;
                
                this.makeMove(index, this.currentPlayer);
                
                if (this.gameMode === 'ai' && this.gameActive && this.currentPlayer === 'O') {
                    setTimeout(() => this.makeAIMove(), 500);
                }
            }

            makeMove(index, player) {
                this.board[index] = player;
                this.updateBoard();
                
                const winner = this.checkWinner();
                if (winner) {
                    this.gameActive = false;
                    this.highlightWinningCells();
                    this.scores[winner]++;
                    this.updateScores();
                    this.saveScores();
                    setTimeout(() => this.showResult(winner), 500);
                } else if (this.board.every(cell => cell !== '')) {
                    this.gameActive = false;
                    this.scores.draws++;
                    this.updateScores();
                    this.saveScores();
                    setTimeout(() => this.showResult('draw'), 500);
                } else {
                    this.currentPlayer = this.currentPlayer === 'X' ? 'O' : 'X';
                    this.updateStatus();
                }
            }

            makeAIMove() {
                const bestMove = this.getBestMove();
                if (bestMove !== -1) {
                    this.makeMove(bestMove, 'O');
                }
            }

            getBestMove() {
                const emptyCells = this.board.map((cell, index) => cell === '' ? index : null).filter(val => val !== null);
                
                if (emptyCells.length === 0) return -1;
                
                switch (this.aiDifficulty) {
                    case 'easy':
                        return this.getRandomMove(emptyCells);
                    case 'medium':
                        return Math.random() < 0.7 ? this.getOptimalMove() : this.getRandomMove(emptyCells);
                    case 'hard':
                        return this.getOptimalMove();
                    default:
                        return this.getRandomMove(emptyCells);
                }
            }

            getRandomMove(emptyCells) {
                return emptyCells[Math.floor(Math.random() * emptyCells.length)];
            }

            getOptimalMove() {
                let bestScore = -Infinity;
                let bestMove = -1;
                
                for (let i = 0; i < 9; i++) {
                    if (this.board[i] === '') {
                        this.board[i] = 'O';
                        const score = this.minimax(this.board, 0, false);
                        this.board[i] = '';
                        
                        if (score > bestScore) {
                            bestScore = score;
                            bestMove = i;
                        }
                    }
                }
                
                return bestMove;
            }

            minimax(board, depth, isMaximizing) {
                const winner = this.checkWinner();
                
                if (winner === 'O') return 10 - depth;
                if (winner === 'X') return depth - 10;
                if (board.every(cell => cell !== '')) return 0;
                
                if (isMaximizing) {
                    let bestScore = -Infinity;
                    for (let i = 0; i < 9; i++) {
                        if (board[i] === '') {
                            board[i] = 'O';
                            const score = this.minimax(board, depth + 1, false);
                            board[i] = '';
                            bestScore = Math.max(score, bestScore);
                        }
                    }
                    return bestScore;
                } else {
                    let bestScore = Infinity;
                    for (let i = 0; i < 9; i++) {
                        if (board[i] === '') {
                            board[i] = 'X';
                            const score = this.minimax(board, depth + 1, true);
                            board[i] = '';
                            bestScore = Math.min(score, bestScore);
                        }
                    }
                    return bestScore;
                }
            }

            checkWinner() {
                for (let combination of this.winningCombinations) {
                    const [a, b, c] = combination;
                    if (this.board[a] && this.board[a] === this.board[b] && this.board[a] === this.board[c]) {
                        this.winningCombination = combination;
                        return this.board[a];
                    }
                }
                return null;
            }

            highlightWinningCells() {
                if (this.winningCombination) {
                    this.winningCombination.forEach(index => {
                        const cell = document.querySelector(`[data-index="${index}"]`);
                        cell.classList.add('winning-cell');
                    });
                }
            }

            updateBoard() {
                const cells = document.querySelectorAll('.game-cell');
                cells.forEach((cell, index) => {
                    const player = this.board[index];
                    if (player) {
                        cell.textContent = player;
                        cell.classList.add(`player-${player.toLowerCase()}`);
                        cell.classList.add('occupied');
                    } else {
                        cell.textContent = '';
                        cell.classList.remove('player-x', 'player-o', 'occupied');
                    }
                });
            }

            updateStatus() {
                const status = document.getElementById('game-status');
                if (!this.gameActive) return;
                
                if (this.gameMode === 'pvp') {
                    status.textContent = `Player ${this.currentPlayer}'s turn`;
                } else {
                    status.textContent = this.currentPlayer === 'X' ? "Your turn" : "AI's turn";
                }
                
                // Update turn indicator
                document.querySelectorAll('.player-indicator').forEach(indicator => {
                    indicator.classList.remove('active');
                    if (indicator.dataset.player === this.currentPlayer) {
                        indicator.classList.add('active');
                    }
                });
            }

            updateScores() {
                document.getElementById('x-wins').textContent = this.scores.X;
                document.getElementById('o-wins').textContent = this.scores.O;
                document.getElementById('draws').textContent = this.scores.draws;
            }

            showResult(result) {
                const modal = document.getElementById('result-modal');
                const icon = document.getElementById('result-icon');
                const title = document.getElementById('result-title');
                const message = document.getElementById('result-message');
                
                if (result === 'draw') {
                    icon.innerHTML = '<i class="fas fa-handshake text-yellow-400"></i>';
                    title.textContent = "It's a Draw!";
                    message.textContent = "Great game! Nobody wins this round.";
                } else {
                    icon.innerHTML = '<i class="fas fa-trophy text-yellow-400"></i>';
                    if (this.gameMode === 'pvp') {
                        title.textContent = `Player ${result} Wins!`;
                        message.textContent = `Congratulations! Player ${result} is the winner.`;
                    } else {
                        if (result === 'X') {
                            title.textContent = "You Win!";
                            message.textContent = "Congratulations! You defeated the AI.";
                        } else {
                            title.textContent = "AI Wins!";
                            message.textContent = "The AI has outsmarted you this time.";
                        }
                    }
                }
                
                modal.classList.remove('hidden');
                document.querySelector('.glass-effect').classList.add('celebrate');
            }

            closeModal() {
                document.getElementById('result-modal').classList.add('hidden');
                document.querySelector('.glass-effect').classList.remove('celebrate');
            }

            playAgain() {
                this.closeModal();
                this.newGame();
            }

            resetGame() {
                this.board = Array(9).fill('');
                this.currentPlayer = 'X';
                this.gameActive = true;
                this.winningCombination = null;
                this.updateBoard();
                this.updateStatus();
                
                // Remove winning highlights
                document.querySelectorAll('.game-cell').forEach(cell => {
                    cell.classList.remove('winning-cell');
                });
            }

            newGame() {
                this.resetGame();
            }

            resetScores() {
                this.scores = { X: 0, O: 0, draws: 0 };
                this.updateScores();
                this.saveScores();
            }

            saveScores() {
                localStorage.setItem('tic-tac-toe-scores', JSON.stringify(this.scores));
            }

            loadScores() {
                const savedScores = localStorage.getItem('tic-tac-toe-scores');
                if (savedScores) {
                    this.scores = JSON.parse(savedScores);
                    this.updateScores();
                }
            }

            handleKeyPress(e) {
                // Keyboard controls for accessibility
                const keyMap = {
                    '1': 0, '2': 1, '3': 2,
                    '4': 3, '5': 4, '6': 5,
                    '7': 6, '8': 7, '9': 8
                };
                
                if (keyMap.hasOwnProperty(e.key)) {
                    this.handleCellClick(keyMap[e.key]);
                } else if (e.key === 'r' || e.key === 'R') {
                    this.resetGame();
                } else if (e.key === 'n' || e.key === 'N') {
                    this.newGame();
                }
            }
        }

        // Add styles for difficulty buttons
        const style = document.createElement('style');
        style.textContent = `
            .difficulty-btn {
                padding: 0.5rem 1rem;
                margin: 0 2px;
                border-radius: 0.5rem;
                background: transparent;
                color: white;
                font-weight: 600;
                transition: all 0.3s ease;
                border: 1px solid rgba(255, 255, 255, 0.2);
            }
            .difficulty-btn:hover {
                background: rgba(255, 255, 255, 0.1);
            }
            .difficulty-btn.active {
                background: rgba(255, 255, 255, 0.3);
                border-color: rgba(255, 255, 255, 0.5);
            }
            .player-indicator {
                opacity: 0.5;
                transition: all 0.3s ease;
                padding: 0.5rem;
                border-radius: 0.5rem;
            }
            .player-indicator.active {
                opacity: 1;
                background: rgba(255, 255, 255, 0.1);
                transform: scale(1.1);
            }
        `;
        document.head.appendChild(style);

        // Initialize the game when the page loads
        document.addEventListener('DOMContentLoaded', () => {
            new TicTacToeGame();
        });
    </script>
</body>
</html># PRODIGY_Wb_03
