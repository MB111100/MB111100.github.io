# MB111100.github.io
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>Doctor Runner</title>
<style>
  body {
    margin: 0;
    background: #eef3f7;
    font-family: Arial, sans-serif;
    text-align: center;
  }

  canvas {
    background: #ffffff;
    display: block;
    margin: 40px auto;
    border: 2px solid #2c3e50;
  }
</style>
</head>
<body>

<h1>Doctor Runner 🧑‍⚕️</h1>
<p>SPACE = jump / restart / continue</p>

<canvas id="game" width="800" height="200"></canvas>

<script>
const canvas = document.getElementById("game");
const ctx = canvas.getContext("2d");

let doctor, obstacles, coins, speed, frame, score, gameOver, won, messageTimer;

function init() {
  doctor = {
    x: 60,
    y: 140,
    width: 24,
    height: 40,
    dy: 0,
    gravity: 0.6,
    jumpPower: -11,
    grounded: true
  };

  obstacles = [];
  coins = [];
  speed = 5;
  frame = 0;
  score = 0;
  gameOver = false;
  won = false;
  messageTimer = 0;
}

init();

document.addEventListener("keydown", e => {
  if (e.code === "Space") {
    if (gameOver) {
      init();
    } else if (won) {
      won = false;
    } else if (doctor.grounded) {
      doctor.dy = doctor.jumpPower;
      doctor.grounded = false;
    }
  }
});

function spawnObstacle() {
  let type = Math.random();

  // Single
  if (type < 0.5) {
    obstacles.push({ x: canvas.width, y: 150, w: 20, h: 20 });
  }
  // Double horizontal
  else if (type < 0.75) {
    obstacles.push({ x: canvas.width, y: 150, w: 20, h: 20 });
    obstacles.push({ x: canvas.width + 25, y: 150, w: 20, h: 20 });
  }
  // Double vertical
  else {
    obstacles.push({ x: canvas.width, y: 150, w: 20, h: 20 });
    obstacles.push({ x: canvas.width, y: 120, w: 20, h: 20 });
  }
}

function spawnCoin(label) {
  coins.push({
    x: canvas.width,
    y: 110,
    size: 18,
    label: label,
    collected: false
  });
}

function update() {
  if (gameOver || won) return;

  frame++;

  // difficulty scaling
  if (frame % 300 === 0) speed += 0.5;

  // random spawn timing
  if (frame % Math.floor(70 + Math.random() * 60) === 0) {
    spawnObstacle();
  }

  // spawn milestone coins
  if (score === 10 && !coins.find(c => c.label === "M1")) spawnCoin("M1");
  if (score === 20 && !coins.find(c => c.label === "M2")) spawnCoin("M2");
  if (score === 30 && !coins.find(c => c.label === "M3")) spawnCoin("M3");

  // physics
  doctor.dy += doctor.gravity;
  doctor.y += doctor.dy;

  if (doctor.y >= 140) {
    doctor.y = 140;
    doctor.dy = 0;
    doctor.grounded = true;
  }

  // obstacles
  obstacles.forEach((obs, i) => {
    obs.x -= speed;

    if (
      doctor.x < obs.x + obs.w &&
      doctor.x + doctor.width > obs.x &&
      doctor.y < obs.y + obs.h &&
      doctor.y + doctor.height > obs.y
    ) {
      gameOver = true;
    }

    if (obs.x + obs.w < 0) {
      obstacles.splice(i, 1);
      score++;
    }
  });

  // coins
  coins.forEach((coin, i) => {
    coin.x -= speed;

    if (
      !coin.collected &&
      doctor.x < coin.x + coin.size &&
      doctor.x + doctor.width > coin.x &&
      doctor.y < coin.y + coin.size &&
      doctor.y + doctor.height > coin.y
    ) {
      coin.collected = true;

      if (coin.label === "M3") {
        won = true;
        messageTimer = 180;
      }
    }

    if (coin.x + coin.size < 0) {
      coins.splice(i, 1);
    }
  });
}

function drawDoctor(x, y) {
  ctx.fillStyle = "#ffffff";
  ctx.fillRect(x, y, 24, 40);

  ctx.fillStyle = "#f1c27d";
  ctx.fillRect(x + 6, y - 10, 12, 10);

  ctx.fillStyle = "#000";
  ctx.fillRect(x + 9, y - 6, 2, 2);
  ctx.fillRect(x + 13, y - 6, 2, 2);

  ctx.fillStyle = "#3498db";
  ctx.fillRect(x, y + 30, 24, 10);

  ctx.fillStyle = "#e74c3c";
  ctx.fillRect(x + 10, y + 10, 4, 12);
  ctx.fillRect(x + 6, y + 14, 12, 4);
}

function drawVirus(x, y, w, h) {
  ctx.fillStyle = "#27ae60";
  ctx.fillRect(x, y, w, h);
}

function drawCoin(c) {
  ctx.fillStyle = "#f1c40f";
  ctx.fillRect(c.x, c.y, c.size, c.size);

  ctx.fillStyle = "#000";
  ctx.font = "10px Arial";
  ctx.fillText(c.label, c.x + 2, c.y + 12);
}

function draw() {
  ctx.clearRect(0, 0, canvas.width, canvas.height);

  // ground
  ctx.fillStyle = "#7f8c8d";
  ctx.fillRect(0, 180, canvas.width, 2);

  drawDoctor(doctor.x, doctor.y);

  obstacles.forEach(o => drawVirus(o.x, o.y, o.w, o.h));
  coins.forEach(c => { if (!c.collected) drawCoin(c); });

  ctx.fillStyle = "#2c3e50";
  ctx.fillText("Score: " + score, 650, 20);

  if (gameOver) {
    ctx.fillText("Game Over - SPACE to restart", 250, 100);
  }

  if (won) {
    ctx.fillText("Congrats, you a Doctor!", 260, 90);
    ctx.fillText("Press SPACE to continue", 270, 110);
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
