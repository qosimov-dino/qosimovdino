<!DOCTYPE html>
<html lang="ru">
<head>
<meta charset="UTF-8">
<title>Chrome Dino Clone Improved</title>
<style>
body {
  margin: 0;
  background-color: #f7f7f7;
  overflow: hidden;
  font-family: sans-serif;
}

#gameCanvas {
  display: block;
  margin: 5vh auto;
  background-color: #fff;
  border: 2px solid #000;
}

#score, #level {
  text-align: center;
  font-size: 4vw;
  margin-top: 1vh;
}
</style>
</head>
<body>

<canvas id="gameCanvas"></canvas>
<div id="score">Очки: 0</div>
<div id="level">Уровень: 1</div>

<script>
// --- Настройка Canvas ---
const canvas = document.getElementById('gameCanvas');
const ctx = canvas.getContext('2d');
canvas.width = window.innerWidth * 0.9;
canvas.height = window.innerHeight * 0.5;

// --- Игровые объекты ---
const dino = {
  x: 50,
  y: canvas.height - 70,
  width: 50,
  height: 50,
  dy: 0,
  gravity: 0.7,
  jumpPower: 15,
  isJumping: false,
  draw() {
    // Можно заменить на спрайт:
    ctx.fillStyle = 'black';
    ctx.fillRect(this.x, this.y, this.width, this.height);
  },
  jump() {
    if (!this.isJumping) {
      this.dy = -this.jumpPower;
      this.isJumping = true;
    }
  },
  update() {
    this.y += this.dy;
    if (this.y + this.height >= canvas.height) {
      this.y = canvas.height - this.height;
      this.dy = 0;
      this.isJumping = false;
    }
    this.dy += this.gravity;
  }
};

// --- Фоновые облака ---
let clouds = [];
function spawnCloud() {
  const cloud = { x: canvas.width, y: Math.random() * (canvas.height/2), width: 60, height: 30 };
  clouds.push(cloud);
  setTimeout(spawnCloud, 4000 + Math.random() * 3000);
}

// --- Препятствия ---
let obstacles = [];
let speed = 6;
function spawnObstacle() {
  const width = 20 + Math.random() * 20;
  const height = 40 + Math.random() * 20;
  const obstacle = { x: canvas.width, y: canvas.height - height, width, height };
  obstacles.push(obstacle);
  let interval = 1500 + Math.random() * 2000 - level*100;
  setTimeout(spawnObstacle, Math.max(600, interval));
}

// --- Очки и уровень ---
let score = 0;
let level = 1;

// --- Отрисовка ---
function draw() {
  ctx.clearRect(0, 0, canvas.width, canvas.height);

  // облака
  ctx.fillStyle = 'lightgray';
  clouds.forEach(c => ctx.fillRect(c.x, c.y, c.width, c.height));

  // динозаврик
  dino.draw();

  // препятствия
  ctx.fillStyle = 'green';
  obstacles.forEach(o => ctx.fillRect(o.x, o.y, o.width, o.height));
}

// --- Обновление состояния ---
function update() {
  dino.update();

  clouds.forEach(c => c.x -= speed * 0.3);
  clouds = clouds.filter(c => c.x + c.width > 0);

  obstacles.forEach(o => o.x -= speed);

  // Столкновения
  obstacles.forEach(o => {
    if (dino.x < o.x + o.width &&
        dino.x + dino.width > o.x &&
        dino.y < o.y + o.height &&
        dino.y + dino.height > o.y) {
      alert('Игра окончена! Очки: ' + score);
      document.location.reload();
    }
  });

  // Подсчёт очков
  obstacles = obstacles.filter(o => {
    if (o.x + o.width < 0) {
      score += 1;
      document.getElementById('score').textContent = 'Очки: ' + score;
      if (score % 5 === 0) {
        level += 1;
        document.getElementById('level').textContent = 'Уровень: ' + level;
        speed += 1;
      }
      return false;
    }
    return true;
  });
}

// --- Игровой цикл ---
function gameLoop() {
  draw();
  update();
  requestAnimationFrame(gameLoop);
}

// --- Управление ---
document.addEventListener('keydown', e => {
  if (e.code === 'Space' || e.key === 'ArrowUp') dino.jump();
});

// --- Старт ---
spawnCloud();
spawnObstacle();
gameLoop();
</script>

</body>
</html>
