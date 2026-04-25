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
<p>Press SPACE to jump / restart</p>

<canvas id="game" width="800" height="200"></canvas>

<script>
const canvas = document.getElementById("game");
const ctx = canvas.getContext("2d");

let doctor, obstacles, speed, frame, score, gameOver;

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
  speed = 5;
  frame = 0;
  score = 0;
  gameOver = false;
}

init();

document.addEventListener("keydown", e => {
  if (e.code === "Space") {
    if (gameOver) {
      init();
    } else if (doctor.grounded) {
      doctor.dy = doctor.jumpPower;
      doctor.grounded = false;
    }
  }
});

function spawnObstacle() {
  obstacles.push({
    x: canvas.width,
    y: 150,
    size: 20
  });
}

function update() {
  if (gameOver) return;

  frame++;

  // Increase difficulty
  if (frame % 300 === 0) speed += 0.5;

  if (frame % Math.max(60, 120 - speed * 5) === 0) {
    spawnObstacle();
  }

  // Physics
  doctor.dy += doctor.gravity;
  doctor.y += doctor.dy;

  if (doctor.y >= 140) {
    doctor.y = 140;
    doctor.dy = 0;
    doctor.grounded = true;
  }

  // Obstacles
  obstacles.forEach((obs, i) => {
    obs.x -= speed;

    // Collision
    if (
      doctor.x < obs.x + obs.size &&
      doctor.x + doctor.width > obs.x &&
      doctor.y < obs.y + obs.size &&
      doctor.y + doctor.height > obs.y
    ) {
      gameOver = true;
    }

    // Remove + score
    if (obs.x + obs.size < 0) {
      obstacles.splice(i, 1);
      score++;
    }
  });
}

function drawDoctor(x, y) {
  // Pixel doctor (simple block style)
  ctx.fillStyle = "#ffffff"; // coat
  ctx.fillRect(x, y, 24, 40);

  ctx.fillStyle = "#f1c27d"; // face
  ctx.fillRect(x + 6, y - 10, 12, 10);

  ctx.fillStyle = "#000"; // eyes
  ctx.fillRect(x + 9, y - 6, 2, 2);
  ctx.fillRect(x + 13, y - 6, 2, 2);

  ctx.fillStyle = "#3498db"; // pants
  ctx.fillRect(x, y + 30, 24, 10);

  ctx.fillStyle = "#e74c3c"; // cross
  ctx.fillRect(x + 10, y + 10, 4, 12);
  ctx.fillRect(x + 6, y + 14, 12, 4);
}

function drawVirus(x, y, size) {
  ctx.fillStyle = "#27ae60";
  ctx.beginPath();
  ctx.arc(x + size/2, y + size/2, size/2, 0, Math.PI * 2);
  ctx.fill();

  // spikes
  for (let i = 0; i < 8; i++) {
    let angle = (Math.PI * 2 / 8) * i;
    let sx = x + size/2 + Math.cos(angle) * (size/2 + 4);
    let sy = y + size/2 + Math.sin(angle) * (size/2 + 4);
    ctx.fillRect(sx, sy, 3, 3);
  }
}

function draw() {
  ctx.clearRect(0, 0, canvas.width, canvas.height);

  // Ground
  ctx.fillStyle = "#7f8c8d";
  ctx.fillRect(0, 180, canvas.width, 2);

  // Doctor
  drawDoctor(doctor.x, doctor.y);

  // Obstacles
  obstacles.forEach(obs => {
    drawVirus(obs.x, obs.y, obs.size);
  });

  // Score
  ctx.fillStyle = "#2c3e50";
  ctx.fillText("Score: " + score, 650, 20);

  if (gameOver) {
    ctx.fillText("Game Over - Press SPACE to restart", 240, 100);
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
