# SNAKE-GAME

<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Snake Game</title>
  <style>
    body {
      display: flex;
      justify-content: center;
      align-items: center;
      height: 100vh;
      background: linear-gradient(135deg, #0f2027, #203a43, #2c5364);
      color: white;
      font-family: Arial, sans-serif;
      margin: 0;
      overflow: hidden;
    }
    canvas {
      border: 3px solid #fff;
      background: #000;
      box-shadow: 0 0 20px rgba(0,255,0,0.7);
    }
    #score {
      position: absolute;
      top: 20px;
      font-size: 22px;
      font-weight: bold;
      text-shadow: 0 0 10px lime;
    }
    #gameOver {
      position: absolute;
      top: 40%;
      font-size: 40px;
      font-weight: bold;
      color: red;
      text-shadow: 0 0 20px yellow;
      display: none;
    }
  </style>
</head>
<body>
  <div id="score">Score: 0</div>
  <div id="gameOver">💀 Game Over! Refresh to Play Again 💀</div>
  <canvas id="gameCanvas" width="400" height="400"></canvas>

  <script>
    const canvas = document.getElementById("gameCanvas");
    const ctx = canvas.getContext("2d");

    const box = 20;
    let score = 0;

    // Snake array
    let snake = [];
    snake[0] = { x: 9 * box, y: 10 * box };

    // Food with random color
    let food = randomFood();
    let foodPulse = 0; // for glowing effect

    function randomFood() {
      const colors = ["red", "yellow", "blue", "lime", "pink", "cyan", "orange", "purple"];
      return {
        x: Math.floor(Math.random() * 20) * box,
        y: Math.floor(Math.random() * 20) * box,
        color: colors[Math.floor(Math.random() * colors.length)]
      };
    }

    // Direction
    let d;
    document.addEventListener("keydown", direction);

    function direction(event) {
      if (event.keyCode == 37 && d != "RIGHT") d = "LEFT";
      else if (event.keyCode == 38 && d != "DOWN") d = "UP";
      else if (event.keyCode == 39 && d != "LEFT") d = "RIGHT";
      else if (event.keyCode == 40 && d != "UP") d = "DOWN";
    }

    function draw() {
      ctx.clearRect(0, 0, canvas.width, canvas.height);

      // Draw snake with gradient colors
      for (let i = 0; i < snake.length; i++) {
        let gradient = ctx.createLinearGradient(snake[i].x, snake[i].y, snake[i].x+box, snake[i].y+box);
        if (i === 0) {
          gradient.addColorStop(0, "lime");
          gradient.addColorStop(1, "green");
        } else {
          gradient.addColorStop(0, "white");
          gradient.addColorStop(1, "gray");
        }
        ctx.fillStyle = gradient;
        ctx.fillRect(snake[i].x, snake[i].y, box, box);
        ctx.strokeStyle = "black";
        ctx.strokeRect(snake[i].x, snake[i].y, box, box);
      }

      // Animate food glow
      foodPulse += 0.1;
      let glow = Math.sin(foodPulse) * 0.5 + 0.5;
      ctx.fillStyle = food.color;
      ctx.beginPath();
      ctx.arc(food.x + box / 2, food.y + box / 2, box / 2 + glow * 3, 0, Math.PI * 2);
      ctx.fill();
      ctx.closePath();

      // Old head
      let snakeX = snake[0].x;
      let snakeY = snake[0].y;

      // Movement
      if (d == "LEFT") snakeX -= box;
      if (d == "UP") snakeY -= box;
      if (d == "RIGHT") snakeX += box;
      if (d == "DOWN") snakeY += box;

      // Wrap around walls
      if (snakeX >= canvas.width) snakeX = 0;
      if (snakeX < 0) snakeX = canvas.width - box;
      if (snakeY >= canvas.height) snakeY = 0;
      if (snakeY < 0) snakeY = canvas.height - box;

      // Eat food
      if (snakeX == food.x && snakeY == food.y) {
        score++;
        document.getElementById("score").innerText = "Score: " + score;
        food = randomFood(); // New food with random color
      } else {
        // Remove tail
        snake.pop();
      }

      // Add new head
      let newHead = { x: snakeX, y: snakeY };

      // Game over if snake collides with itself
      if (collision(newHead, snake)) {
        clearInterval(game);
        document.getElementById("gameOver").style.display = "block"; // Fade game over message
      }

      snake.unshift(newHead);
    }

    function collision(head, array) {
      for (let i = 0; i < array.length; i++) {
        if (head.x == array[i].x && head.y == array[i].y) {
          return true;
        }
      }
      return false;
    }

    let game = setInterval(draw, 120);
  </script>
</body>
</html>
