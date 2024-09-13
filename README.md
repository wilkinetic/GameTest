
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Simple Parkour Game</title>
  <style>
    body {
      margin: 0;
      overflow: hidden;
    }
    canvas {
      display: block;
      background-color: #f0f0f0;
    }
    .win-message {
      position: absolute;
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%);
      font-size: 50px;
      font-weight: bold;
      color: green;
      display: none;
    }
  </style>
</head>
<body>
  <canvas id="gameCanvas"></canvas>
  <div id="winMessage" class="win-message">You Win!</div>
  
  <script>
    const canvas = document.getElementById("gameCanvas");
    const ctx = canvas.getContext("2d");

    canvas.width = window.innerWidth;
    canvas.height = window.innerHeight;

    let player = {
      x: 50,
      y: canvas.height - 100,
      width: 50,
      height: 50,
      color: "blue",
      dy: 0,
      gravity: 0.8,
      jumpPower: -15,
      isJumping: false
    };

    let obstacles = [];
    let gameSpeed = 2; // Slower speed for easier obstacles
    let score = 0;
    const winScore = 10; // Score required to win the game

    function createObstacle() {
      let height = Math.random() * 80 + 50; // Random height for obstacles
      let obstacle = {
        x: canvas.width,
        y: canvas.height - height,
        width: 20,
        height: height,
        color: "red"
      };
      obstacles.push(obstacle);
    }

    function updateObstacles() {
      for (let i = 0; i < obstacles.length; i++) {
        obstacles[i].x -= gameSpeed;
        if (obstacles[i].x + obstacles[i].width < 0) {
          obstacles.splice(i, 1);
          i--;
          score++;
        }
      }
    }

    function drawPlayer() {
      ctx.fillStyle = player.color;
      ctx.fillRect(player.x, player.y, player.width, player.height);
    }

    function drawObstacles() {
      for (let i = 0; i < obstacles.length; i++) {
        ctx.fillStyle = obstacles[i].color;
        ctx.fillRect(obstacles[i].x, obstacles[i].y, obstacles[i].width, obstacles[i].height);
      }
    }

    function updatePlayer() {
      if (player.isJumping) {
        player.dy += player.gravity;
        player.y += player.dy;
        if (player.y + player.height >= canvas.height) {
          player.y = canvas.height - player.height;
          player.isJumping = false;
          player.dy = 0;
        }
      }
    }

    function handleJump() {
      if (!player.isJumping) {
        player.isJumping = true;
        player.dy = player.jumpPower;
      }
    }

    function detectCollision() {
      for (let i = 0; i < obstacles.length; i++) {
        if (player.x < obstacles[i].x + obstacles[i].width &&
            player.x + player.width > obstacles[i].x &&
            player.y < obstacles[i].y + obstacles[i].height &&
            player.y + player.height > obstacles[i].y) {
          return true;
        }
      }
      return false;
    }

    function gameLoop() {
      ctx.clearRect(0, 0, canvas.width, canvas.height);

      drawPlayer();
      drawObstacles();
      updateObstacles();
      updatePlayer();

      if (detectCollision()) {
        alert(`Game Over! Your score: ${score}`);
        document.location.reload();
      }

      if (score >= winScore) {
        document.getElementById('winMessage').style.display = 'block';
        return; // Stop the game loop when player wins
      }

      requestAnimationFrame(gameLoop);
    }

    setInterval(createObstacle, 2500); // Easier obstacle generation with more space

    window.addEventListener("keydown", (e) => {
      if (e.code === "Space" || e.code === "ArrowUp") {
        handleJump();
      }
    });

    gameLoop();
  </script>
</body>
</html>
