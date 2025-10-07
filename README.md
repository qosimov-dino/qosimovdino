<!DOCTYPE html>
<html lang="ru">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Qosimov Dino 2.0</title>
<style>
  * { margin: 0; padding: 0; box-sizing: border-box; }
  body {
    font-family: Arial, sans-serif;
    background: linear-gradient(to top, #87ceeb, #f0f8ff);
    overflow-y: auto;
  }
  canvas {
    display: block;
    margin: 40px auto;
    background: linear-gradient(to top, #f4e1a6 20%, #87ceeb 80%);
    border: 3px solid #333;
    border-radius: 10px;
  }
  #ui {
    text-align: center;
    color: #333;
  }
  button {
    margin-top: 10px;
    padding: 10px 20px;
    font-size: 18px;
    border: none;
    border-radius: 8px;
    background: #4caf50;
    color: white;
    cursor: pointer;
    transition: 0.2s;
  }
  button:hover { background: #45a049; }
</style>
</head>
<body>

<div id="ui">
  <h1>🦖 Qosimov Dino</h1>
  <p>Нажми <b>Пробел</b> или <b>тапни</b> чтобы прыгнуть</p>
  <p>Счёт: <span id="score">0</span></p>
  <button id="newGame">Новая игра</button>
</div>

<canvas id="gameCanvas" width="800" height="300"></canvas>

<script>
const canvas = document.getElementById('gameCanvas');
const ctx = canvas.getContext('2d');
const scoreEl = document.getElementById('score');
const newGameBtn = document.getElementById('newGame');

let dino, obstacles, score, gravity, gameSpeed, gameOver, cloudTimer;

function resetGame() {
  dino = { x: 50, y: 230, w: 40, h: 40, dy: 0, jumping: false };
  obstacles = [];
  clouds = [];
  score = 0;
  gravity = 0.8;
  gameSpeed = 4;
  gameOver = false;
  cloudTimer = 0;
  scoreEl.textContent = score;
}

function drawDino() {
  // Тело
  ctx.fillStyle = '#2e7d32';
  ctx.fillRect(dino.x, dino.y, dino.w, dino.h);
  // Глаза
  ctx.fillStyle = '#fff';
  ctx.fillRect(dino.x + 8, dino.y + 8, 8, 8);
}

function drawObstacle(ob) {
  ctx.fillStyle = '#8b4513';
  ctx.fillRect(ob.x, ob.y, ob.w, ob.h);
}

function drawCloud(cloud) {
  ctx.fillStyle = 'white';
  ctx.beginPath();
  ctx.arc(cloud.x, cloud.y, cloud.r, 0, Math.PI * 2);
  ctx.fill();
}

function update() {
  if (gameOver) return;
  ctx.clearRect(0, 0, canvas.width, canvas.height);

  // Небо и земля
  ctx.fillStyle = '#87ceeb';
  ctx.fillRect(0, 0, canvas.width, canvas.height);
  ctx.fillStyle = '#c2b280';
  ctx.fillRect(0, 270, canvas.width, 30);

  // Динозавр
  dino.y += dino.dy;
  if (dino.y + dino.h < 270) dino.dy += gravity;
  else { dino.y = 230; dino.dy = 0; dino.jumping = false; }
  drawDino();

  // Облака
  cloudTimer++;
  if (cloudTimer % 150 === 0) {
    clouds.push({ x: canvas.width, y: 50 + Math.random()*50, r: 20+Math.random()*10 });
  }
  for (let i = clouds.length - 1; i >= 0; i--) {
    clouds[i].x -= 1.5;
    drawCloud(clouds[i]);
    if (clouds[i].x + clouds[i].r < 0) clouds.splice(i,1);
  }

  // Препятствия
  if (Math.random() < 0.015) {
    obstacles.push({ x: canvas.width, y: 240, w: 20, h: 30 });
  }
  for (let i = obstacles.length - 1; i >= 0; i--) {
    let ob = obstacles[i];
    ob.x -= gameSpeed;
    drawObstacle(ob);

    // Столкновение
    if (dino.x < ob.x + ob.w &&
        dino.x + dino.w > ob.x &&
        dino.y + dino.h > ob.y) {
      gameOver = true;
    }

    if (ob.x + ob.w < 0) {
      obstacles.splice(i, 1);
      score++;
      scoreEl.textContent = score;
      if (score % 5 === 0) gameSpeed += 0.5;
    }
  }

  // Game Over
  if (gameOver) {
    ctx.fillStyle = '#ff3333';
    ctx.font = '40px Arial';
    ctx.fillText('💀 Game Over', 300, 150);
    return;
  }

  requestAnimationFrame(update);
}

function jump() {
  if (!dino.jumping) {
    dino.dy = -13;
    dino.jumping = true;
  }
}

document.addEventListener('keydown', e => { if (e.code === 'Space') jump(); });
canvas.addEventListener('click', jump);
newGameBtn.addEventListener('click', () => { resetGame(); update(); });

resetGame();
update();
</script>

</body>
</html>
