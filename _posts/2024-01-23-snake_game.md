---
toc: false
comments: false
layout: post
title: snake game
type: hacks
courses: { compsci: {week: 7} }
---

<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <style>
        body {
            background-color: #FFC0CB; /* Pink background */
            display: flex;
            align-items: center;
            justify-content: center;
            height: 100vh;
            margin: 0;
        }

        button {
            background-color: #00F; /* Blue button */
            color: #FFF;
            padding: 10px;
            font-size: 16px;
            cursor: pointer;
        }

        canvas {
            border: 1px solid #000;
            display: none;
        }
    </style>
    <title>Snake Game</title>
</head>
<body>
    <button id="startButton" onclick="startGame()">Start Game</button>
    <canvas id="snakeCanvas" width="400" height="400"></canvas>
    <script>
        const canvas = document.getElementById('snakeCanvas');
        const ctx = canvas.getContext('2d');

        const GRID_SIZE = 20;
        const GRID_WIDTH = canvas.width / GRID_SIZE;
        const GRID_HEIGHT = canvas.height / GRID_SIZE;

        const snake = {
            body: [{ x: 5, y: 5 }],
            direction: { x: 1, y: 0 }
        };

        let apple = { x: 10, y: 10 };
        let gameRunning = false;

        function draw() {
            // Clear canvas
            ctx.clearRect(0, 0, canvas.width, canvas.height);

            // Draw snake
            ctx.fillStyle = '#00F';
            snake.body.forEach(segment => {
                ctx.fillRect(segment.x * GRID_SIZE, segment.y * GRID_SIZE, GRID_SIZE, GRID_SIZE);
            });

            // Draw apple
            ctx.fillStyle = '#F00';
            ctx.fillRect(apple.x * GRID_SIZE, apple.y * GRID_SIZE, GRID_SIZE, GRID_SIZE);
        }

        function update() {
            const head = { x: snake.body[0].x + snake.direction.x, y: snake.body[0].y + snake.direction.y };
            
            // Check for collision with walls or self
            if (head.x < 0 || head.x >= GRID_WIDTH || head.y < 0 || head.y >= GRID_HEIGHT || isCollision(head)) {
                stopGame();
                return;
            }

            // Check if snake eats the apple
            if (head.x === apple.x && head.y === apple.y) {
                snake.body.unshift({ ...head });
                generateApple();
            } else {
                snake.body.unshift({ ...head });
                snake.body.pop();
            }
        }

        function isCollision(head) {
            return snake.body.slice(1).some(segment => segment.x === head.x && segment.y === head.y);
        }

        function generateApple() {
            apple = {
                x: Math.floor(Math.random() * GRID_WIDTH),
                y: Math.floor(Math.random() * GRID_HEIGHT)
            };

            // Make sure the apple doesn't spawn on the snake
            while (isCollision(apple)) {
                apple = {
                    x: Math.floor(Math.random() * GRID_WIDTH),
                    y: Math.floor(Math.random() * GRID_HEIGHT)
                };
            }
        }

        function resetGame() {
            snake.body = [{ x: 5, y: 5 }];
            snake.direction = { x: 1, y: 0 };
            generateApple();
        }

        function startGame() {
            if (!gameRunning) {
                resetGame();
                gameRunning = true;
                document.getElementById('startButton').style.display = 'none';
                canvas.style.display = 'block';
            }
        }

        function stopGame() {
            gameRunning = false;
            document.getElementById('startButton').style.display = 'block';
            canvas.style.display = 'none';
        }

        function handleKeyPress(event) {
            if (!gameRunning) return;

            switch (event.key) {
                case 'ArrowUp':
                    snake.direction = { x: 0, y: -1 };
                    break;
                case 'ArrowDown':
                    snake.direction = { x: 0, y: 1 };
                    break;
                case 'ArrowLeft':
                    snake.direction = { x: -1, y: 0 };
                    break;
                case 'ArrowRight':
                    snake.direction = { x: 1, y: 0 };
                    break;
            }
        }

        document.addEventListener('keydown', handleKeyPress);

        function gameLoop() {
            if (gameRunning) {
                update();
                draw();
            }
        }

        setInterval(gameLoop, 100); // Adjust the interval to control the game speed
    </script>
</body>
</html>