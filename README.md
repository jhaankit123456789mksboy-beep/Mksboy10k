# Mksboy10k
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="MKS BOY" content="width=device-width, initial-scale=1.0">
    <title>Neon Snake Game MKS </title>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;700&display=swap" rel="stylesheet">
    <style>
        /* Global styles */
        body {
            font-family: 'Inter', sans-serif;
            background-color: #1a1a2e; /* Dark background */
            color: #e0e0e0;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            margin: 0;
            overflow: hidden; /* Prevent scrolling */
        }

        /* Game container */
        .game-container {
            background-color: #0f0f1dMKS;
            border-radius: 15px;
            padding: 20px;
            box-shadow: 0 0 30px rgba(0, 255, 0, 0.4); /* Green glow effect */
            display: flex;
            flex-direction: column;
            align-items: center;
            position: relative;
            max-width: 90vw; /* Reverted max-width */
            max-height: 90vh; /* Reverted max-height */
            margin: 20px;
        }

        /* Score display */
        #score {
            font-size: 2.2em;
            font-weight: 700;
            margin-bottom: 15px;
            color: #00ff00; /* Neon green */
            text-shadow: 0 0 10px #00ff00, 0 0 20px #00ff00;
        }

        /* Canvas styling */
        canvas {
            background-color: #000000; /* Black grid background */
            border: 5px solid #00ff00; /* Neon green border */
            border-radius: 10px;
            box-shadow: 0 0 20px rgba(0, 255, 0, 0.6);
            display: block;
            touch-action: none; /* Disable default touch actions on canvas */
        }

        /* Game Over message */
        #game-over-screen {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background-color: rgba(0, 0, 0, 0.8);
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            border-radius: 15px;
            z-index: 10;
            text-align: center;
            padding: 20px;
            box-sizing: border-box;
            opacity: 0; /* Start hidden */
            transition: opacity 0.5s ease-in-out; /* Fade in effect */
            pointer-events: none; /* Allow clicks through when hidden */
        }

        #game-over-screen.show {
            opacity: 1;
            pointer-events: auto; /* Enable clicks when shown */
        }

        #game-over-screen h2 {
            font-size: 3.5em; /* Slightly larger */
            color: #ff0000; /* Neon red */
            text-shadow: 0 0 10px #ff0000, 0 0 20px #ff0000, 0 0 30px #ff0000; /* More intense glow */
            margin-bottom: 25px; /* More spacing */
        }

        #game-over-screen button {
            background-color: #00ff00; /* Neon green */
            color: #1a1a2e;
            border: none;
            padding: 18px 35px; /* Larger padding */
            font-size: 1.6em; /* Slightly larger font */
            font-weight: 700;
            border-radius: 12px; /* Slightly more rounded */
            cursor: pointer;
            box-shadow: 0 0 18px rgba(0, 255, 0, 0.8); /* More intense shadow */
            transition: background-color 0.3s, box-shadow 0.3s, transform 0.2s; /* Add transform for bounce */
            text-transform: uppercase; /* Make text uppercase */
            letter-spacing: 1px;
            margin-top: 10px; /* Spacing between buttons */
        }

        #game-over-screen button:hover {
            background-color: #00e600;
            box-shadow: 0 0 30px rgba(0, 255, 0, 1.2); /* Even more intense glow on hover */
            transform: translateY(-3px); /* Slight lift effect */
        }
        #game-over-screen button:active {
            transform: translateY(0); /* Press down effect */
            box-shadow: 0 0 10px rgba(0, 255, 0, 0.5);
        }

        /* LLM Response Area */
        #llm-response {
            margin-top: 25px;
            padding: 15px;
            background-color: rgba(0, 255, 0, 0.1); /* Light green translucent background */
            border: 1px solid #00ff00;
            border-radius: 10px;
            color: #e0e0e0;
            font-size: 1.1em;
            max-width: 80%;
            min-height: 50px; /* Give it some minimum height */
            display: flex;
            justify-content: center;
            align-items: center;
            text-shadow: 0 0 5px rgba(0, 255, 0, 0.3);
            opacity: 0;
            transition: opacity 0.5s ease-in-out;
        }
        #llm-response.show {
            opacity: 1;
        }
        #llm-response.loading {
            font-style: italic;
            color: #999;
        }


        /* Mobile controls */
        .mobile-controls {
            display: none; /* Hidden by default, shown on smaller screens */
            margin-top: 25px; /* More spacing */
            gap: 15px; /* More gap */
            grid-template-areas:
                ". up ."
                "left . right"
                ". down .";
            width: 240px; /* Larger control area */
            height: 240px; /* Larger control area */
        }

        .mobile-controls button {
            background-color: #33334d; /* Darker background for buttons */
            color: #00ff00; /* Neon green text */
            border: 2px solid #00ff00;
            border-radius: 50%; /* Circular buttons */
            width: 70px; /* Larger buttons */
            height: 70px; /* Larger buttons */
            font-size: 2.2em; /* Larger arrow icons */
            display: flex;
            justify-content: center;
            align-items: center;
            cursor: pointer;
            box-shadow: 0 0 12px rgba(0, 255, 0, 0.6); /* Slightly more intense glow */
            transition: background-color 0.2s, box-shadow 0.2s, transform 0.1s; /* Add transform */
            user-select: none; /* Prevent text selection */
            -webkit-tap-highlight-color: transparent; /* Remove tap highlight on mobile */
        }

        .mobile-controls button:active {
            background-color: #00ff00;
            color: #1a1a2e;
            box-shadow: 0 0 25px rgba(0, 255, 0, 1.2); /* More intense glow on press */
            transform: scale(0.95); /* Slight press effect */
        }

        .mobile-controls .up { grid-area: up; }
        .mobile-controls .down { grid-area: down; }
        .mobile-controls .left { grid-area: left; }
        .mobile-controls .right { grid-area: right; }

        /* Media queries for responsiveness */
        @media (max-width: 768px) {
            .game-container {
                padding: 15px;
                box-shadow: 0 0 20px rgba(0, 255, 0, 0.3);
            }
            #score {
                font-size: 1.8em;
                margin-bottom: 10px;
            }
            #game-over-screen h2 {
                font-size: 2.5em;
            }
            #game-over-screen button {
                padding: 12px 25px;
                font-size: 1.2em;
            }
            .mobile-controls {
                display: grid; /* Show on smaller screens */
                width: 200px;
                height: 200px;
                gap: 10px;
            }
            .mobile-controls button {
                width: 60px;
                height: 60px;
                font-size: 1.8em;
            }
            #llm-response {
                font-size: 1em;
                padding: 10px;
            }
        }

        @media (max-width: 480px) {
            .game-container {
                padding: 10px;
                margin: 10px;
            }
            #score {
                font-size: 1.5em;
            }
            #game-over-screen h2 {
                font-size: 2em;
            }
            #game-over-screen button {
                padding: 10px 20px;
                font-size: 1em;
            }
            .mobile-controls {
                width: 180px;
                height: 180px;
            }
            .mobile-controls button {
                width: 50px;
                height: 50px;
                font-size: 1.5em;
            }
            #llm-response {
                font-size: 0.9em;
                padding: 8px;
            }
        }
    </style>
</head>
<body>
    <div class="game-container">
        <div id="score">SCORE: 0</div>
        <canvas id="gameCanvas"></canvas>
        <div id="game-over-screen">
            <h2>GAME OVER!</h2>
            <button id="restartButton">RESTART GAME</button>
            <button id="getWisdomButton"><a href="https://www.instagram.com/ff_hacker_boy_m.s?igsh=Mxfledhla3btc2v4za==" target="_blank" style="color: #00FF00; font-weight: bold;">
  👉 Follow on Instagram 👈
</a>
✨</button>
            <div id="llm-response"></div>
        </div>
    </div>

    <div class="mobile-controls">
        <button class="control-button up" data-direction="up">⬆</button>
        <button class="control-button left" data-direction="left">⬅</button>
        <button class="control-button right" data-direction="right">➡</button>
        <button class="control-button down" data-direction="down">⬇</button>
    </div>

    <script>
        // Get canvas and context
        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');
        const scoreDisplay = document.getElementById('score');
        const gameOverScreen = document.getElementById('game-over-screen');
        const restartButton = document.getElementById('restartButton');
        const getWisdomButton = document.getElementById('getWisdomButton'); // New button
        const llmResponseDiv = document.getElementById('llm-response'); // New div for LLM response
        const mobileControls = document.querySelector('.mobile-controls');

        // Game variables
        const gridSize = 20; // 20x20 grid
        let cellSize; // Calculated dynamically
        let snake;
        let food;
        let direction;
        let score;
        let gameOver;
        let gameInterval;
        let gameSpeed = 150; // Initial speed in ms
        let lastDirection; // To prevent immediate reverse
        let isEating = false; // New state variable for mouth animation

        // Function to set canvas size and cell size based on window
        function setCanvasSize() {
            const minDimension = Math.min(window.innerWidth, window.innerHeight) * 0.7;
            const size = Math.min(minDimension, 400);
            canvas.width = size;
            canvas.height = size;
            cellSize = canvas.width / gridSize;
        }

        // Initialize game
        function initGame() {
            setCanvasSize(); // Set initial canvas size
            snake = [{ x: 10, y: 10 }]; // Start in the middle
            food = {};
            direction = 'right';
            lastDirection = 'right';
            score = 0;
            gameOver = false;
            isEating = false; // Reset eating state
            scoreDisplay.textContent = `SCORE: ${score}`;
            gameOverScreen.classList.remove('show'); // Hide game over screen with fade out
            llmResponseDiv.classList.remove('show'); // Hide LLM response
            llmResponseDiv.textContent = ''; // Clear previous response
            gameSpeed = 150; // Reset speed

            generateFood();
            clearInterval(gameInterval); // Clear any existing interval
            gameInterval = setInterval(gameLoop, gameSpeed);
        }

        // Generate random food position
        function generateFood() {
            let newFoodX, newFoodY;
            let collisionWithSnake;
            do {
                newFoodX = Math.floor(Math.random() * gridSize);
                newFoodY = Math.floor(Math.random() * gridSize);
                collisionWithSnake = snake.some(segment => segment.x === newFoodX && segment.y === newFoodY);
            } while (collisionWithSnake); // Ensure food doesn't spawn on snake

            food = { x: newFoodX, y: newFoodY };
        }

        // Draw everything on canvas
        function draw() {
            // Clear canvas
            ctx.clearRect(0, 0, canvas.width, canvas.height);

            // Draw subtle grid lines
            ctx.strokeStyle = 'rgba(0, 255, 0, 0.1)'; /* Very light neon green for grid */
            ctx.lineWidth = 0.5;
            for (let i = 0; i <= gridSize; i++) {
                ctx.beginPath();
                ctx.moveTo(i * cellSize, 0);
                ctx.lineTo(i * cellSize, canvas.height);
                ctx.stroke();
                ctx.beginPath();
                ctx.moveTo(0, i * cellSize);
                ctx.lineTo(canvas.width, i * cellSize);
                ctx.stroke();
            }

            // Draw snake
            ctx.fillStyle = '#00ff00'; // Neon green
            ctx.shadowColor = '#00ff00';
            ctx.shadowBlur = 15;

            // Draw snake body segments
            for (let i = 1; i < snake.length; i++) {
                const segment = snake[i];
                ctx.fillRect(segment.x * cellSize, segment.y * cellSize, cellSize, cellSize);
            }

            // Draw snake head with mouth
            const head = snake[0];
            ctx.fillRect(head.x * cellSize, head.y * cellSize, cellSize, cellSize); // Draw head body

            // Draw mouth
            ctx.fillStyle = '#1a1a2e'; // Dark color for the mouth
            ctx.shadowBlur = 0; // No shadow for mouth for sharper look

            let mouthWidth = cellSize / 4;
            let mouthHeight = cellSize / 4;
            const mouthOffset = cellSize / 8;

            if (isEating) {
                // Adjust mouth size for "open" state
                if (direction === 'left' || direction === 'right') {
                    mouthWidth = cellSize / 2; // Wider mouth
                } else { // up or down
                    mouthHeight = cellSize / 2; // Taller mouth
                }
            }

            if (direction === 'right') {
                ctx.fillRect(head.x * cellSize + cellSize - mouthWidth - mouthOffset, head.y * cellSize + (cellSize - mouthHeight) / 2, mouthWidth, mouthHeight);
            } else if (direction === 'left') {
                ctx.fillRect(head.x * cellSize + mouthOffset, head.y * cellSize + (cellSize - mouthHeight) / 2, mouthWidth, mouthHeight);
            } else if (direction === 'up') {
                ctx.fillRect(head.x * cellSize + (cellSize - mouthWidth) / 2, head.y * cellSize + mouthOffset, mouthWidth, mouthHeight);
            } else if (direction === 'down') {
                ctx.fillRect(head.x * cellSize + (cellSize - mouthWidth) / 2, head.y * cellSize + cellSize - mouthHeight - mouthOffset, mouthWidth, mouthHeight);
            }

            // Draw food with a pulsating effect (visual only, not actual size change)
            ctx.fillStyle = '#ff0000'; // Neon red
            ctx.shadowColor = '#ff0000';
            // Make shadow blur pulsate slightly for food
            const pulse = Math.sin(Date.now() * 0.005) * 5 + 15; // Pulse between 10 and 20
            ctx.shadowBlur = pulse;
            ctx.fillRect(food.x * cellSize, food.y * cellSize, cellSize, cellSize);

            // Reset shadow for next drawings (if any)
            ctx.shadowBlur = 0;
        }

        // Move the snake
        function moveSnake() {
            const head = { x: snake[0].x, y: snake[0].y };

            // Update head position based on direction
            switch (direction) {
                case 'up':
                    head.y--;
                    break;
                case 'down':
                    head.y++;
                    break;
                case 'left':
                    head.x--;
                    break;
                case 'right':
                    head.x++;
                    break;
            }

            // Add new head to the beginning
            snake.unshift(head);

            // Check if food was eaten
            if (head.x === food.x && head.y === food.y) {
                score++;
                scoreDisplay.textContent = `SCORE: ${score}`;
                generateFood();
                isEating = true; // Set eating state to true
                setTimeout(() => {
                    isEating = false; // Reset eating state after a short delay
                }, 100); // Mouth stays open for 100ms
                // Increase speed slightly
                if (gameSpeed > 50) { // Don't go below 50ms
                    gameSpeed -= 5;
                    clearInterval(gameInterval);
                    gameInterval = setInterval(gameLoop, gameSpeed);
                }
            } else {
                // Remove tail if no food eaten
                snake.pop();
            }

            lastDirection = direction; // Update lastDirection after movement
        }

        // Check for collisions
        function checkCollision() {
            const head = snake[0];

            // Wall collision
            if (head.x < 0 || head.x >= gridSize || head.y < 0 || head.y >= gridSize) {
                gameOver = true;
            }

            // Self-collision (start checking from 4th segment to avoid immediate self-collision)
            for (let i = 4; i < snake.length; i++) {
                if (head.x === snake[i].x && head.y === snake[i].y) {
                    gameOver = true;
                    break;
                }
            }

            if (gameOver) {
                endGame();
            }
        }

        // Game loop
        function gameLoop() {
            if (gameOver) return;

            moveSnake();
            checkCollision();
            draw(); // Redraw every frame for pulsating food effect and mouth animation
        }

        // End game
        function endGame() {
            clearInterval(gameInterval);
            gameOverScreen.classList.add('show'); // Show game over screen with fade in
        }

        // Function to get Snake Wisdom from LLM
        async function getSnakeWisdom() {
            llmResponseDiv.textContent = 'Generating snake wisdom...';
            llmResponseDiv.classList.add('show', 'loading');
            getWisdomButton.disabled = true; // Disable button during generation

            const prompt = `The player just finished a Snake game with a score of ${score}. Provide a short, creative, and encouraging game over message or a piece of 'snake wisdom' related to their performance. Keep it under 50 words.`;

            let chatHistory = [];
            chatHistory.push({ role: "user", parts: [{ text: prompt }] });
            const payload = { contents: chatHistory };
            const apiKey = ""; // Canvas will automatically provide this in runtime
            const apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.0-flash:generateContent?key=${apiKey}`;

            try {
                const response = await fetch(apiUrl, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify(payload)
                });
                const result = await response.json();

                if (result.candidates && result.candidates.length > 0 &&
                    result.candidates[0].content && result.candidates[0].content.parts &&
                    result.candidates[0].content.parts.length > 0) {
                    const text = result.candidates[0].content.parts[0].text;
                    llmResponseDiv.textContent = text;
                } else {
                    llmResponseDiv.textContent = 'Failed to get wisdom. Try again!';
                    console.error('LLM response structure unexpected:', result);
                }
            } catch (error) {
                llmResponseDiv.textContent = 'Error fetching wisdom. Check console for details.';
                console.error('Error calling Gemini API:', error);
            } finally {
                llmResponseDiv.classList.remove('loading');
                getWisdomButton.disabled = false; // Re-enable button
            }
        }


        // Event Listeners

        // Keyboard controls
        document.addEventListener('keydown', e => {
            if (gameOver) return; // Ignore input if game is over

            switch (e.key) {
                case 'ArrowUp':
                    if (lastDirection !== 'down') direction = 'up';
                    break;
                case 'ArrowDown':
                    if (lastDirection !== 'up') direction = 'down';
                    break;
                case 'ArrowLeft':
                    if (lastDirection !== 'right') direction = 'left';
                    break;
                case 'ArrowRight':
                    if (lastDirection !== 'left') direction = 'right';
                    break;
            }
        });

        // Mobile controls (buttons)
        mobileControls.addEventListener('click', e => {
            if (gameOver) return;
            const target = e.target.closest('.control-button');
            if (target) {
                const newDirection = target.dataset.direction;
                if (newDirection === 'up' && lastDirection !== 'down') direction = 'up';
                else if (newDirection === 'down' && lastDirection !== 'up') direction = 'down';
                else if (newDirection === 'left' && lastDirection !== 'right') direction = 'left';
                else if (newDirection === 'right' && lastDirection !== 'left') direction = 'right';
            }
        });

        // Restart button
        restartButton.addEventListener('click', initGame);

        // Get Wisdom button
        getWisdomButton.addEventListener('click', getSnakeWisdom);

        // Handle window resize
        window.addEventListener('resize', () => {
            if (!gameOver) { // Only re-initialize if game is not over to avoid disrupting game state
                initGame();
            } else { // If game is over, just resize the canvas
                setCanvasSize();
                draw(); // Redraw the game over screen content
            }
        });

        // Initialize the game when the window loads
        window.onload = initGame;
    </script>
</body>
</html>

