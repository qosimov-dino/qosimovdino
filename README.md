<!DOCTYPE html>
<html lang="ru">
<head>
<meta charset="UTF-8">
<title>Dino Game HashTemail</title>
<style>
body { margin:0; background:#f7f7f7; overflow-y:auto; font-family:sans-serif; }
#gameCanvas { display:block; margin:5vh auto; background:#fff; border:2px solid #000; }
#score,#level { text-align:center; font-size:4vw; margin-top:1vh; }
</style>
</head>
<body>

<canvas id="gameCanvas"></canvas>
<div id="score">Очки: 0</div>
<div id="level">Уровень: 1</div>

<script>
const canvas=document.getElementById('gameCanvas');
const ctx=canvas.getContext('2d');
canvas.width=window.innerWidth*0.9;
canvas.height=window.innerHeight*0.5;

const dino={x:50,y:canvas.height-70,width:50,height:50,dy:0,gravity:0.7,jumpPower:15,isJumping:false,
draw(){ctx.fillStyle='black';ctx.fillRect(this.x,this.y,this.width,this.height);},
jump(){if(!this.isJumping){this.dy=-this.jumpPower;this.isJumping=true;}},
update(){this.y+=this.dy;if(this.y+this.height>=canvas.height){this.y=canvas.height-this.height;this.dy=0;this.isJumping=false;}this.dy+=this.gravity;}};

let clouds=[],obstacles=[],speed=6,score=0,level=1;

function spawnCloud(){const cloud={x:canvas.width,y:Math.random()*(canvas.height/2),width:60,height:30};clouds.push(cloud);setTimeout(spawnCloud,4000+Math.random()*3000);}
function spawnObstacle(){const width=20+Math.random()*20,height=40+Math.random()*20;const obstacle={x:canvas.width,y:canvas.height-height,width,height};obstacles.push(obstacle);let interval=1500+Math.random()*2000-level*100;setTimeout(spawnObstacle,Math.max(600,interval));}

function draw(){ctx.clearRect(0,0,canvas.width,canvas.height);ctx.fillStyle='lightgray';clouds.forEach(c=>ctx.fillRect(c.x,c.y,c.width,c.height));dino.draw();ctx.fillStyle='green';obstacles.forEach(o=>ctx.fillRect(o.x,o.y,o.width,o.height));}
function update(){dino.update();clouds.forEach(c=>c.x-=speed*0.3);clouds=clouds.filter(c=>c.x+c.width>0);obstacles.forEach(o=>o.x-=speed);
obstacles.forEach(o=>{if(dino.x<o.x+o.width&&dino.x+dino.width>o.x&&dino.y<o.y+o.height&&dino.y+dino.height>o.y){alert('Игра окончена! Очки: '+score);document.location.reload();}});
obstacles=obstacles.filter(o=>{if(o.x+o.width<0){score+=1;document.getElementById('score').textContent='Очки: '+score;if(score%5===0){level+=1;document.getElementById('level').textContent='Уровень: '+level;speed+=1;}return false;}return true;});}

function gameLoop(){draw();update();requestAnimationFrame(gameLoop);}

// управление ПК и мобильные устройства
document.addEventListener('keydown',e=>{if(e.code==='Space'||e.key==='ArrowUp')dino.jump();});
document.addEventListener('touchstart',()=>dino.jump());
document.addEventListener('click',()=>dino.jump());

spawnCloud();spawnObstacle();gameLoop();
</script>

</body>
</html>
