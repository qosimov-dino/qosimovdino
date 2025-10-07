# qosimovdino<!DOCTYPE html>
<html lang="ru">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Qosimov Dino</title>
<style>
<div style="height: 400px;"></div>
<footer style="text-align:center; padding: 20px; background:#eee;">
  <p>© 2025 Qosimov Game Studio</p>
</footer>  * { margin: 0; padding: 0; box-sizing: border-box; }
  body {
    background: linear-gradient(to top, #87ceeb, #e0f7fa);
    overflow: hidden;
    font-family: "Segoe UI", sans-serif;
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

let dino, obstacles, score, gravity, gameSpeed, gameOver;

function resetGame() {
  dino = { x: 50, y: 230, w: 40, h: 40, dy: 0, jumping: false };
  obstacles = [];
  score = 0;
  gravity = 0.8;
  gameSpeed = 4;
  gameOver = false;
  scoreEl.textContent = score;
}

function drawDino() {
  ctx.fillStyle = '#2e7d32';
  ctx.fillRect(dino.x, dino.y, dino.w, dino.h);
  ctx.fillStyle = '#fff';
  ctx.fillRect(dino.x + 8, dino.y + 8, 8, 8); // глаз
}

function drawObstacle(ob) {
  ctx.fillStyle = '#8b4513';
  ctx.fillRect(ob.x, ob.y, ob.w, ob.h);
}

function update() {
  if (gameOver) return;
  ctx.clearRect(0, 0, canvas.width, canvas.height);

  // Земля
  ctx.fillStyle = '#c2b280';
  ctx.fillRect(0, 270, canvas.width, 30);

  // Обновление динозавра
  dino.y += dino.dy;
  if (dino.y + dino.h < 270) dino.dy += gravity;
  else {
    dino.y = 230;
    dino.dy = 0;
    dino.jumping = false;
  }
  drawDino();

  // Создание препятствий
  if (Math.random() < 0.015) {
    obstacles.push({ x: canvas.width, y: 240, w: 20, h: 30 });
  }

  // Обновление препятствий
  for (let i = 0; i < obstacles.length; i++) {
    let ob = obstacles[i];
    ob.x -= gameSpeed;
    drawObstacle(ob);

    // Проверка столкновения
    if (
      dino.x < ob.x + ob.w &&
      dino.x + dino.w > ob.x &&
      dino.y + dino.h > ob.y
    ) {
      gameOver = true;
    }

    if (ob.x + ob.w < 0) {
      obstacles.splice(i, 1);
      score++;
      scoreEl.textContent = score;
      if (score % 5 === 0) gameSpeed += 0.5;
    }
  }

  // Текст Game Over
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

document.addEventListener('keydown', e => {
  if (e.code === 'Space') jump();
});

canvas.addEventListener('click', jump);
newGameBtn.addEventListener('click', () => {
  resetGame();
  update();
});

resetGame();
update();
</script>

</body>
</html>
