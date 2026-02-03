<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Mini GTA Style Game</title>
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<style>
body{margin:0;background:#000;color:#fff;font-family:Arial}
#ui{padding:10px;text-align:center;background:#111}
#stats span{margin:0 10px}
canvas{background:#2c7a3f;display:block;margin:auto;border:2px solid #fff}
</style>
</head>
<body>
<div id="ui">
<h2>🚗 Mini GTA Style Game</h2>
<div id="stats">
<span>💰 Money: <span id="money">0</span></span>
<span>⭐ Respect: <span id="respect">0</span></span>
<span>🚔 Wanted: <span id="wanted">0</span></span>
</div>
<p>Controls: W A S D = Move | Space = Crime</p>
</div>

<canvas id="game" width="800" height="500"></canvas>

<script>
const canvas=document.getElementById('game');
const ctx=canvas.getContext('2d');

let player={x:400,y:250,size:15,speed:3};
let money=0,respect=0,wanted=0;
let keys={};

let police={x:100,y:100,size:15,speed:2,active:false};

const moneyEl=document.getElementById('money');
const respectEl=document.getElementById('respect');
const wantedEl=document.getElementById('wanted');

function updateStats(){moneyEl.textContent=money;respectEl.textContent=respect;wantedEl.textContent=wanted;}

window.addEventListener('keydown',e=>{keys[e.key.toLowerCase()]=true;});
window.addEventListener('keyup',e=>{keys[e.key.toLowerCase()]=false;});

function crime(){
  let gain=Math.floor(Math.random()*100)+50;
  money+=gain;respect+=2;wanted+=10;
  if(wanted>20) police.active=true;
  updateStats();
}

function movePlayer(){
  if(keys['w']) player.y-=player.speed;
  if(keys['s']) player.y+=player.speed;
  if(keys['a']) player.x-=player.speed;
  if(keys['d']) player.x+=player.speed;

  player.x=Math.max(0,Math.min(canvas.width,player.x));
  player.y=Math.max(0,Math.min(canvas.height,player.y));
}

function movePolice(){
  if(!police.active) return;
  let dx=player.x-police.x;
  let dy=player.y-police.y;
  let dist=Math.hypot(dx,dy);
  police.x+=dx/dist*police.speed;
  police.y+=dy/dist*police.speed;

  if(dist<20){
    alert('🚔 Busted! Game Restarted');
    money=0;respect=0;wanted=0;
    police.active=false;
    player.x=400;player.y=250;
    updateStats();
  }
}

function draw(){
  ctx.clearRect(0,0,canvas.width,canvas.height);

  // player
  ctx.fillStyle='blue';
  ctx.beginPath();
  ctx.arc(player.x,player.y,player.size,0,Math.PI*2);
  ctx.fill();

  // police
  if(police.active){
    ctx.fillStyle='red';
    ctx.beginPath();
    ctx.arc(police.x,police.y,police.size,0,Math.PI*2);
    ctx.fill();
  }
}

window.addEventListener('keydown',e=>{if(e.code==='Space') crime();});

function gameLoop(){
  movePlayer();
  movePolice();
  draw();
  requestAnimationFrame(gameLoop);
}

updateStats();
gameLoop();
</script>
</body>
</html>
