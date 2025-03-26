<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>XO Game</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #222;
            color: white;
            text-align: center;
            padding-top: 50px;
        }

        .game-board {
            display: grid;
            grid-template-columns: repeat(3, 120px);
            gap: 15px;
            margin: 50px auto;
            width: fit-content;
            background: #333;
            padding: 20px;
            border-radius: 10px;
            box-shadow: 0 0 20px rgba(0, 0, 0, 0.5);
        }

        .cell {
            width: 120px;
            height: 120px;
            background: #444;
            border: 3px solid #ff0;
            cursor: pointer;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 2em;
            color: white;
            transition: transform 0.3s, background 0.3s;
        }

        .cell:hover {
            transform: scale(1.05);
            background: #555;
        }

        .winner-line {
            animation: glow 1.5s infinite;
            background: #0f0 !important;
            border-color: #0f0 !important;
        }

        .game-over {
            pointer-events: none;
            opacity: 0.9;
        }

        @keyframes glow {
            0% { background-color: #0f0; }
            50% { background-color: #0a0; }
            100% { background-color: #0f0; }
        }

        button {
            margin-top: 20px;
            padding: 10px 20px;
            font-size: 16px;
            background-color: #28a745;
            color: white;
            border: none;
            border-radius: 5px;
            cursor: pointer;
        }

        button:hover {
            background-color: #218838;
        }

        .status {
            margin-top: 20px;
            font-size: 18px;
        }
    </style>
</head>
<body>
    <h1>XO Game</h1>
    <div class="game-board" id="gameBoard"></div>
    <button onclick="restartGame()">Restart Game</button>
    <div class="status">
        <p>Turn: <span id="turn">Player 1 (❌)</span></p>
        <p>Winner: <span id="winner">In Progress</span></p>
    </div>

    <script>
        const board = Array(9).fill(null);
        let currentPlayer = 'X';
        let gameOver = false;
        const winConditions = [
            [0, 1, 2], // الصف الأول
            [3, 4, 5], // الصف الثاني
            [6, 7, 8], // الصف الثالث
            [0, 3, 6], // العمود الأول
            [1, 4, 7], // العمود الثاني
            [2, 5, 8], // العمود الثالث
            [0, 4, 8], // القطر الرئيسي
            [2, 4, 6]  // القطر الثانوي
        ];

        function renderBoard() {
            const gameBoard = document.getElementById('gameBoard');
            gameBoard.innerHTML = '';
            board.forEach((cell, index) => {
                const cellDiv = document.createElement('div');
                cellDiv.classList.add('cell');
                if (cell) {
                    cellDiv.textContent = cell === 'X' ? '❌' : '⭕'; // استخدام رموز Unicode
                } else {
                    cellDiv.textContent = '?';
                }
                if (gameOver && isWinningCell(index)) {
                    cellDiv.classList.add('winner-line');
                }
                if (gameOver) {
                    cellDiv.classList.add('game-over');
                }
                cellDiv.addEventListener('click', () => handleCellClick(index));
                gameBoard.appendChild(cellDiv);
            });
        }

        function handleCellClick(index) {
            if (gameOver || board[index]) return;

            board[index] = currentPlayer;
            renderBoard();
            checkWinner();
            togglePlayer();
        }

        function togglePlayer() {
            currentPlayer = currentPlayer === 'X' ? 'O' : 'X';
            document.getElementById('turn').textContent = `Player ${currentPlayer === 'X' ? '1 (❌)' : '2 (⭕)'}`;
        }

        function checkWinner() {
            for (const condition of winConditions) {
                const [a, b, c] = condition;
                if (board[a] && board[a] === board[b] && board[a] === board[c]) {
                    document.getElementById('winner').textContent = `Player ${currentPlayer === 'X' ? '1' : '2'} Wins!`;
                    gameOver = true;
                    return;
                }
            }

            if (board.every(cell => cell)) {
                document.getElementById('winner').textContent = 'Draw!';
                gameOver = true;
            }
        }

        function isWinningCell(index) {
            if (!gameOver) return false;

            for (const condition of winConditions) {
                const [a, b, c] = condition;
                if (board[a] && board[a] === board[b] && board[a] === board[c]) {
                    return condition.includes(index);
                }
            }
            return false;
        }

        function restartGame() {
            board.fill(null);
            currentPlayer = 'X';
            gameOver = false;
            document.getElementById('turn').textContent = 'Player 1 (❌)';
            document.getElementById('winner').textContent = 'In Progress';
            renderBoard();
        }

        // بدء اللعبة
        renderBoard();
    </script>
</body>
</html>
