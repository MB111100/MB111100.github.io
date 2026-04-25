# MB111100.github.io
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>Dino Runner</title>
<style>
  body {
    margin: 0;
    background: #f7f7f7;
    font-family: Arial, sans-serif;
    text-align: center;
  }

  canvas {
    background: white;
    display: block;
    margin: 40px auto;
    border: 2px solid #333;
  }

  h1 {
    margin-top: 20px;
  }
</style>
</head>
<body>

<h1>Dino Runner 🦖</h1>
<canvas id="game" width="800" height="200"></canvas>
<p>Press SPACE to jump</p>

<script>
const canvas = document.getElementById("game");
const ctx = canvas.getContext("2d");

let dino = {
  x: 50,
  y: 150,
  width: 30,
  height: 30,
  dy: 0,
  gravity: 0.6,
  jumpPower: -10,
  grounded: true
};

let obstacles = [];
let frame = 0;
let score = 0;
let gameOver = false;

document.addEventListener("keydown", e => {
  if (e.code === "Space" && dino.grounded) {
    dino.dy = dino.jumpPower;
    dino.grounded = false;
  }
});

function spawnObstacle() {
  obstacles.push({
    x: canvas.width,
    y: 160,
    width: 20,
    height: 40
  });
}

function update() {
  if (gameOver) return;

  frame++;
  if (frame % 90 === 0) spawnObstacle();

  // Dino physics
  dino.dy += dino.gravity;
  dino.y += dino.dy;

  if (dino.y >= 150) {
    dino.y = 150;
    dino.dy = 0;
    dino.grounded = true;
  }

  // Obstacles
  obstacles.forEach((obs, i) => {
    obs.x -= 5;

    // Collision
    if (
      dino.x < obs.x + obs.width &&
      dino.x + dino.width > obs.x &&
      dino.y < obs.y + obs.height &&
      dino.y + dino.height > obs.y
    ) {
      gameOver = true;
    }

    // Remove off screen
    if (obs.x + obs.width < 0) {
      obstacles.splice(i, 1);
      score++;
    }
  });
}

function draw() {
  ctx.clearRect(0, 0, canvas.width, canvas.height);

  // Ground
  ctx.fillStyle = "#444";
  ctx.fillRect(0, 180, canvas.width, 2);

  // Dino
  ctx.fillStyle = "green";
  ctx.fillRect(dino.x, dino.y, dino.width, dino.height);

  // Obstacles
  ctx.fillStyle = "red";
  obstacles.forEach(obs => {
    ctx.fillRect(obs.x, obs.y, obs.width, obs.height);
  });

  // Score
  ctx.fillStyle = "#000";
  ctx.fillText("Score: " + score, 650, 20);

  if (gameOver) {
    ctx.fillText("Game Over - Refresh to restart", 250, 100);
  }
}

function loop() {
  update();
  draw();
  requestAnimationFrame(loop);
}

loop();
</script>

</body>
</html>
