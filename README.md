# Fruit-ninja
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Fruit Ninja</title>
    <style>
        body {
            margin: 0;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            background-color: #87CEEB; /* Sky blue */
            overflow: hidden;
        }

        #game-container {
            position: relative;
            width: 800px;
            height: 600px;
            background-color: #7CFC00; /* Green background */
            border: 2px solid #000;
            overflow: hidden;
        }

        #start-screen {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            text-align: center;
            font-family: Arial, sans-serif;
            color: #FFF;
            text-shadow: 1px 1px 2px #000;
        }

        .fruit {
            position: absolute;
            width: 50px;
            height: 50px;
            background-color: #FF4500; /* Orange for fruits */
            border-radius: 50%;
            cursor: pointer;
        }

        .bomb {
            position: absolute;
            width: 50px;
            height: 50px;
            background-color: #333; /* Dark gray for bombs */
            border-radius: 50%;
            cursor: pointer;
        }

        #score {
            position: absolute;
            top: 10px;
            left: 10px;
            font-family: Arial, sans-serif;
            font-size: 20px;
            color: #FFF;
            text-shadow: 1px 1px 2px #000;
        }

        #lives {
            position: absolute;
            top: 10px;
            right: 10px;
            font-family: Arial, sans-serif;
            font-size: 20px;
            color: #FFF;
            text-shadow: 1px 1px 2px #000;
        }
    </style>
</head>
<body>
    <div id="game-container">
        <div id="start-screen">
            <h1>Fruit Ninja</h1>
            <p>Click to Start! (Slice fruits, avoid bombs)</p>
        </div>
        <div id="score">Score: 0</div>
        <div id="lives">Lives: 3</div>
    </div>

    <script>
        const gameContainer = document.getElementById('game-container');
        const startScreen = document.getElementById('start-screen');
        const scoreDisplay = document.getElementById('score');
        const livesDisplay = document.getElementById('lives');

        let isGameRunning = false;
        let score = 0;
        let lives = 3;
        let objects = [];
        const gravity = 1.0; // Speed objects fall
        const spawnInterval = 500; // Spawn every 1 second

        // Start game on click
        startScreen.addEventListener('click', () => {
            if (!isGameRunning) {
                isGameRunning = true;
                startScreen.style.display = 'none';
                gameLoop();
                spawnObjects();
            }
        });

        // Spawn fruits and bombs
        function spawnObjects() {
            if (!isGameRunning) return;

            const isBomb = Math.random() < 0.3; // 30% chance for a bomb
            const object = document.createElement('div');
            object.classList.add(isBomb ? 'bomb' : 'fruit');
            object.style.left = Math.random() * (800 - 50) + 'px'; // Random x-position
            object.style.top = '-50px'; // Start above screen
            object.addEventListener('mousedown', () => sliceObject(object, isBomb));
            gameContainer.appendChild(object);
            objects.push({ element: object, y: -50 });
            
            setTimeout(spawnObjects, spawnInterval);
        }

        // Slice fruit or bomb
        function sliceObject(object, isBomb) {
            if (isBomb) {
                lives -= 1;
                livesDisplay.textContent = `Lives: ${lives}`;
            } else {
                score += 10;
                scoreDisplay.textContent = `Score: ${score}`;
            }
            object.remove();
            objects = objects.filter(obj => obj.element !== object);
            if (lives <= 0) endGame();
        }

        // Game loop
        function gameLoop() {
            if (!isGameRunning) return;

            objects.forEach((obj, index) => {
                obj.y += gravity;
                obj.element.style.top = obj.y + 'px';

                // Remove if off-screen
                if (obj.y > 600) {
                    if (!obj.element.classList.contains('bomb')) {
                        lives -= 1; // Lose life for missing fruit
                        livesDisplay.textContent = `Lives: ${lives}`;
                    }
                    obj.element.remove();
                    objects.splice(index, 1);
                    if (lives <= 0) endGame();
                }
            });

            requestAnimationFrame(gameLoop);
        }

        // End game
        function endGame() {
            isGameRunning = false;
            alert(`Game Over! Score: ${score} - Refresh to play again.`);
            resetGame();
        }

        // Reset game
        function resetGame() {
            score = 0;
            lives = 3;
            scoreDisplay.textContent = `Score: ${score}`;
            livesDisplay.textContent = `Lives: ${lives}`;
            objects.forEach(obj => obj.element.remove());
            objects = [];
            startScreen.style.display = 'block';
        }
    </script>
</body>
</html>
