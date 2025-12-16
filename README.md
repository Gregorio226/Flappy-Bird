<!DOCTYPE html>
<html lang="it">
<head>
<meta charset="UTF-8">
<title>Flappy Bird Original Clone</title>
<style>
body { margin:0; overflow:hidden; background:#70c5ce; }
canvas { display:block; margin:0 auto; background:#70c5ce; }
</style>
</head>
<body>
<canvas id="gameCanvas" width="288" height="512"></canvas>
<script>
const canvas=document.getElementById("gameCanvas");
const ctx=canvas.getContext("2d");

// Sprite originale
const sprite=new Image();
sprite.src="https://raw.githubusercontent.com/sourabhv/FlapPyBird/master/resources/spritesheet.png";

// Audio
const FLAP=new Audio('https://actions.google.com/sounds/v1/cartoon/wood_plank_flicks.ogg');
const HIT=new Audio('https://actions.google.com/sounds/v1/cartoon/metal_hit.ogg');
const SCORE_S=new Audio('https://actions.google.com/sounds/v1/cartoon/clang_and_wobble.ogg');

// Stato gioco
const state={current:0,getReady:0,game:1,over:2};

// Controllo
canvas.addEventListener("click",function(){
    switch(state.current){
        case state.getReady: state.current=state.game; break;
        case state.game: bird.flap(); FLAP.play(); break;
        case state.over: pipes.reset(); bird.reset(); score.reset(); state.current=state.getReady; break;
    }
});

// Bird
const bird={
    x:50, y:150, width:34, height:24, frame:0, gravity:0.25, jump:4.6, speed:0, rotation:0,
    draw:function(){
        ctx.save();
        ctx.translate(this.x,this.y);
        ctx.rotate(this.rotation);
        ctx.drawImage(sprite, 276, 112, this.width, this.height, -this.width/2, -this.height/2, this.width, this.height);
        ctx.restore();
    },
    flap:function(){ this.speed=-this.jump; },
    update:function(){
        this.speed+=this.gravity; this.y+=this.speed;
        if(this.y+this.height/2>=canvas.height-112){ this.y=canvas.height-112-this.height/2; if(state.current===state.game){state.current=state.over; HIT.play();} }
        this.rotation=this.speed>=this.jump?90*Math.PI/180:-25*Math.PI/180;
    },
    reset:function(){ this.speed=0; this.y=150; }
};

// Sfondo e terreno
function drawBG(){
    ctx.drawImage(sprite, 0, 0, 288, 512, 0, 0, 288, 512);
}

// Pipes
const pipes={position:[],width:52,height:320,gap:100,dx:2,
    draw:function(){
        for(let i=0;i<this.position.length;i++){
            let p=this.position[i];
            ctx.drawImage(sprite,0,0,this.width,this.height,p.x,p.y,this.width,this.height);
            ctx.drawImage(sprite,52,0,this.width,this.height,p.x,p.y+this.height+this.gap,this.width,this.height);
        }
    },
    update:function(){
        if(state.current!==state.game) return;
        if(frames%100===0){ this.position.push({x:canvas.width,y:-150*(Math.random()+1)}); }
        for(let i=0;i<this.position.length;i++){
            let p=this.position[i]; p.x-=this.dx;
            if(bird.x+bird.width/2>p.x && bird.x-bird.width/2<p.x+this.width && (bird.y-bird.height/2<p.y+this.height || bird.y+bird.height/2>p.y+this.height+this.gap)){ state.current=state.over; HIT.play(); }
            if(p.x+this.width<0){ this.position.shift(); score.value+=1; SCORE_S.play(); score.best=Math.max(score.best,score.value); }
        }
    },
    reset:function(){ this.position=[]; }
};

// Score
const score={value:0,best:0,draw:function(){ctx.fillStyle="#FFF";ctx.strokeStyle="#000";ctx.font="35px Teko";ctx.lineWidth=2;ctx.fillText(this.value,canvas.width/2,50);ctx.strokeText(this.value,canvas.width/2,50);},reset:function(){this.value=0;}};

// Draw
function draw(){ drawBG(); pipes.draw(); bird.draw(); score.draw(); }

// Update
function update(){ bird.update(); pipes.update(); }

let frames=0;
function loop(){ update(); draw(); frames++; requestAnimationFrame(loop); }

loop();
</script>
</body>
</html>
