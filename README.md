<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Parkour Game with Block Character</title>
  <style>
    body {
      margin: 0;
      overflow: hidden;
    }
    canvas {
      display: block;
      background-color: #FFA500; /* Light blue background for visibility */
      border: 2px solid black;
      margin: 0 auto;
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
      text-align: center;
    }
    .win-message a {
      display: block;
      margin-top: 20px;
      font-size: 30px;
      color: blue;
      text-decoration: underline;
      cursor: pointer;
    }
    .game-over-message {
      position: absolute;
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%);
      font-size: 40px;
      font-weight: bold;
      color: red;
      display: none;
      text-align: center;
    }
  </style>
</head>
<body>
  <canvas id="gameCanvas" width="800" height="400"></canvas>
  <div id="winMessage" class="win-message">
    You Win!
    <a id="redirectLink" href="#">Click here to proceed</a>
  </div>
  <div id="gameOverMessage" class="game-over-message">
    Game Over! Press Space to Restart
  </div>
  
  <script>
    const canvas = document.getElementById("gameCanvas");
    const ctx = canvas.getContext("2d");

    // Block character design
    let player = {
      x: 50,
      y: canvas.height - 100,
      width: 50,
      height: 50,
      dy: 0,
      gravity: 0.8,
      jumpPower: -20, // Negative value to jump upward
      isJumping: false,
      color: "#c58c85", // Character color
    };

    let obstacles = [];
    let gameSpeed = 12; // Increased speed for faster gameplay
    let score = 0;
    const winScore = 20; // Win after 20 obstacles
    let gameOver = false;

    function createObstacle() {
      let height = Math.random() * 80 + 50;
      let obstacle = {
        x: canvas.width,
        y: canvas.height - height,
        width: 20,
        height: height,
        color: "green" // Green for rectangle obstacles
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

    function drawRectangle(obstacle) {
      // Draw green rectangular obstacle
      ctx.fillStyle = obstacle.color;
      ctx.fillRect(obstacle.x, obstacle.y, obstacle.width, obstacle.height);
    }

    function drawPlayer() {
      // Draw block character with face
      ctx.fillStyle = player.color;
      ctx.fillRect(player.x, player.y, player.width, player.height);

      // Add eyes
      ctx.fillStyle = "#ffffff"; // White eyes
      ctx.fillRect(player.x + 10, player.y + 10, 10, 10); // Left eye
      ctx.fillRect(player.x + 30, player.y + 10, 10, 10); // Right eye

      // Add mouth
      ctx.fillStyle = "#000000"; // Black mouth
      ctx.fillRect(player.x + 20, player.y + 30, 10, 5); // Mouth
    }

    function drawObstacles() {
      for (let i = 0; i < obstacles.length; i++) {
        drawRectangle(obstacles[i]); // Draw rectangle for each obstacle
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
      if (gameOver) return; // Stop game if game over
      
      ctx.clearRect(0, 0, canvas.width, canvas.height);

      drawPlayer();
      drawObstacles();
      updateObstacles();
      updatePlayer();

      if (detectCollision()) {
        gameOver = true;
        document.getElementById('gameOverMessage').style.display = 'block';
        return;
      }

      if (score >= winScore) {
        document.getElementById('winMessage').style.display = 'block';
        return;
      }

      requestAnimationFrame(gameLoop);
    }

    function resetGame() {
      player.x = 50;
      player.y = canvas.height - 100;
      player.dy = 0;
      player.isJumping = false;
      obstacles = [];
      score = 0;
      gameOver = false;
      document.getElementById('gameOverMessage').style.display = 'none';
      gameLoop();
    }

    // Increase the interval between obstacles
    setInterval(createObstacle, 2500); // Obstacles appear every 2.5 seconds now

    window.addEventListener("keydown", (e) => {
      if (e.code === "Space" || e.code === "ArrowUp") {
        if (gameOver) {
          resetGame(); // Restart game if it's over
        } else {
          handleJump(); // Jump if the game is still running
        }
      }
    });

    // Redirect to another page when the win link is clicked
    const redirectLink = document.getElementById("redirectLink");
    redirectLink.addEventListener("click", function() {
      window.location.href = "https://docs.google.com/forms/d/e/1FAIpQLSfpFcSd2nrM-sI3WAGBJHL-IEluDGJpkLuKvpSu9SAotzjrjA/viewform?usp=sf_link"; // Change this to the desired URL
    });

    gameLoop();
  </script>
</body>
</html>

