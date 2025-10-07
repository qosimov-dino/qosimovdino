<html lang="ru">
<head>
<meta charset="UTF-8">
<title>Игра Динозаврик</title>
<style>
  body {
    margin: 0;
    background-color: #f0f0f0;
    overflow: hidden;
    font-family: sans-serif;
  }

  #gameArea {
    position: relative;
    width: 90vw;
    max-width: 800px;
    height: 25vh;
    margin: 5vh auto;
    background-color: #fff;
    border: 2px solid #000;
    overflow: hidden;
  }

  #dino {
    position: absolute;
    bottom: 0;
    left: 5%;
    width: 5vw;
    height: 10vh;
    background-color: #333;
  }

  .cactus {
    position: absolute;
    bottom: 0;
    width: 2vw;
    height: 10vh;
    background-color: green;
  }

  #score {
    text-align: center;
    font-size: 4vw;
    margin-top: 2vh;
  }
</style>
</head>
<body>

<div id="gameArea">
  <div id="dino"></div>
</div>
<div id="score">Очки: 0</div>

<script>
const dino = document.getElementById('dino');
const gameArea = document.getElementById('gameArea');
const scoreDisplay = document.getElementById('score');

let isJumping = false;
let position = 0;
let score = 0;
let gameOver = false;

// Прыжок
function jump() {
  if (isJumping || gameOver) return;
  isJumping = true;

  let upInterval = setInterval(() => {
    if (position >= gameArea.offsetHeight * 0.75) { // высота прыжка относительно экрана
      clearInterval(upInterval);

      let downInterval = setInterval(() => {
        if (position <= 0) {
          clearInterval(downInterval);
          isJumping = false;
        }
        position -= gameArea.offsetHeight * 0.05;
        dino.style.bottom = position + 'px';
      }, 20);

    } else {
      position += gameArea.offsetHeight * 0.05;
      dino.style.bottom = position + 'px';
    }
  }, 20);
}

document.addEventListener('keydown', (event) => {
  if (event.code === 'Space' || event.key === 'ArrowUp') jump();
});

// Генерация кактусов
let lastCactusTime = 0;

function createCactus() {
  if (gameOver) return;

  const now = Date.now();
  if (now - lastCactusTime < 1000) { 
    setTimeout(createCactus, 500);
    return;
  }
  lastCactusTime = now;

  const cactus = document.createElement('div');
  cactus.classList.add('cactus');
  cactus.style.left = gameArea.offsetWidth + 'px';
  gameArea.appendChild(cactus);

  let cactusInterval = setInterval(() => {
    let cactusLeft = parseFloat(cactus.style.left);

    // Проверка столкновения
    if (
      cactusLeft < dino.offsetLeft + dino.offsetWidth &&
      cactusLeft + cactus.offsetWidth > dino.offsetLeft &&
      position < dino.offsetHeight
    ) {
      alert('Игра окончена! Очки: ' + score);
      gameOver = true;
      clearInterval(cactusInterval);
      return;
    }

    if (cactusLeft < -cactus.offsetWidth) {
      clearInterval(cactusInterval);
      cactus.remove();
      score += 1;
      scoreDisplay.textContent = 'Очки: ' + score;
    } else {
      cactus.style.left = cactusLeft - (gameArea.offsetWidth * 0.005) + 'px';
    }

  }, 20);

  let randomTime = Math.random() * 2000 + 1500;
  setTimeout(createCactus, randomTime);
}

createCactus();
</script>

</body>
</html>
