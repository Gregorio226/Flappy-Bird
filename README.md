# Flappy-Bird<!DOCTYPE html>
<html lang="it">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Flappy Gregorio</title>

  <!-- PWA Manifest -->
  <link rel="manifest" href="manifest.json">
  <meta name="theme-color" content="#70c5ce">

  <style>
    body {
      margin: 0;
      display: flex;
      justify-content: center;
      align-items: center;
      height: 100vh;
      background: linear-gradient(#70c5ce, #dff6ff);
      font-family: Arial, sans-serif;
    }
    canvas {
      border: 2px solid #000;
    }
  </style>
</head>
<body>

<canvas id="game" width="400" height="600"></canvas>

<script>
// --- Game Code ---
const canvas = document.getElementById("game");
const ctx = canvas.getContext("2d");
let state = "menu";
let best = localStorage.getItem("best") || 0;

// Audio
const AudioCtx = window.AudioContext || window.webkitAudioContext;
const audioCtx = new AudioCtx();
function playSound(type){
  const osc = audioCtx.createOscillator();
  const gain = audioCtx.createGain();
  osc.connect(gain); gain.connect(audioCtx.destination);
  osc.frequency.value = type==="jump"?600:type==="point"?900:200;
  gain.gain.value = 0.1;
  osc.start(); osc.stop(audioCtx.currentTime +0.1);
}

// Bird
const bird={x:80,y:250,w:34,h:24,v:0,g:0.5,j:-8,flap:0};
let groundX=0;
const pipes=[]; const pipeW=60; let pipeGap=150; let speed=2; let frame=0; let score=0;

function resetGame(){bird.y=250;bird.v=0;pipes.length=0;frame=0;score=0;speed=2;pipeGap=150;state="play";}
function drawBird(){bird.flap=(bird.flap+1)%20;ctx.fillStyle="yellow";ctx.fillRect(bird.x,bird.y,bird.w,bird.h);if(bird.flap<10)ctx.fillRect(bird.x-6,bird.y+6,6,10);ctx.fillStyle="black";ctx.fillRect(bird.x+22,bird.y+6,4,4);ctx.fillStyle="orange";ctx.fillRect(bird.x+bird.w,bird.y+10,6,4);}
function drawGround(){ctx.fillStyle="#ded895";ctx.fillRect(0,560,canvas.width,40);ctx.fillStyle="#c2b280";ctx.fillRect(groundX,560,canvas.width,40);ctx.fillRect(groundX+canvas.width,560,canvas.width,40);groundX-=speed;if(groundX<=-canvas.width)groundX=0;}
function drawPipes(){ctx.fillStyle="#4CAF50";pipes.forEach(p=>{ctx.fillRect(p.x,0,pipeW,p.top);ctx.fillRect(p.x,p.top+pipeGap,pipeW,canvas.height);});}
function updatePipes(){if(frame%120===0)pipes.push({x:canvas.width,top:Math.random()*220+50,passed:false});pipes.forEach(p=>{p.x-=speed;if(!p.passed&&p.x+pipeW<bird.x){p.passed=true;score++;playSound("point");speed+=0.15;pipeGap-=1;}if(bird.x+bird.w>p.x&&bird.x<p.x+pipeW&&(bird.y<p.top||bird.y+bird.h>p.top+pipeGap))endGame();});if(pipes.length&&pipes[0].x+pipeW<0)pipes.shift();}
function updateBird(){bird.v+=bird.g;bird.y+=bird.v;if(bird.y+bird.h>560||bird.y<0)endGame();}
function endGame(){state="gameover";playSound("hit");if(score>best){best=score;localStorage.setItem("best",best);}}
function drawHUD(){ctx.fillStyle="white";ctx.font="24px Arial";ctx.fillText(score,20,40);ctx.font="14px Arial";ctx.fillText("BEST: "+best,20,60);}
function menuScreen(){ctx.fillStyle="rgba(0,0,0,0.4)";ctx.fillRect(0,0,canvas.width,canvas.height);ctx.fillStyle="white";ctx.font="36px Arial";ctx.fillText("FLAPPY GREGORIO",50,260);ctx.font="18px Arial";ctx.fillText("CLICCA PER GIOCARE",90,310);}
function pauseScreen(){ctx.fillStyle="rgba(0,0,0,0.5)";ctx.fillRect(0,0,canvas.width,canvas.height);ctx.fillStyle="white";ctx.font="36px Arial";ctx.fillText("PAUSA",130,280);ctx.font="18px Arial";ctx.fillText("CLICCA PER CONTINUARE",70,320);}
function gameOverScreen(){ctx.fillStyle="rgba(0,0,0,0.5)";ctx.fillRect(0,0,canvas.width,canvas.height);ctx.fillStyle="white";ctx.font="36px Arial";ctx.fillText("GAME OVER",90,260);ctx.font="18px Arial";ctx.fillText("SCORE: "+score,140,300);ctx.fillText("CLICCA PER RIPROVARE",80,340);}
function loop(){ctx.clearRect(0,0,canvas.width,canvas.height);if(state=="menu")menuScreen();if(state=="play"){drawBird();drawPipes();drawGround();drawHUD();updateBird();updatePipes();frame++;}if(state=="pause")pauseScreen();if(state=="gameover"){drawBird();drawPipes();drawGround();gameOverScreen();}requestAnimationFrame(loop);}
function input(){audioCtx.resume();if(state=="menu")resetGame();else if(state=="play"){bird.v=bird.j;playSound("jump");}else if(state=="pause")state="play";else if(state=="gameover")resetGame();}
document.addEventListener("click",input);
document.addEventListener("touchstart",input);
document.addEventListener("keydown",e=>{if(e.key=="p"||e.key=="P"){if(state=="play")state="pause";else if(state=="pause")state="play";}});
loop();
</script>

</body>
</html>{
  "name": "Flappy Gregorio",
  "short_name": "Flappy",
  "start_url": ".",
  "display": "standalone",
  "background_color": "#70c5ce",
  "theme_color": "#70c5ce",
  "icons": [
    {
      "src": "icon.png",
      "sizes": "192x192",
      "type": "image/png"
    },
    {
      "src": "icon.png",
      "sizes": "512x512",
      "type": "image/png"
    }
  ]
}<img width="1536" height="1024" alt="16609" src="https://github.com/user-attachments/assets/ed41475b-f9d4-4917-8a12-2483cf86ada8" />
