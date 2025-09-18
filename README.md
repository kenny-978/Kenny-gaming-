#<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Mini Free Fire Demo</title>
  <style>
    body { margin:0; background:#111; overflow:hidden; }
    canvas { display:block; margin:0 auto; background:#222; }
  </style>
</head>
<body>
  <canvas id="game" width="600" height="400"></canvas>
  <script>
    const canvas = document.getElementById("game");
    const ctx = canvas.getContext("2d");

    // player
    const player = { x: 300, y: 200, size: 15, speed: 3, bullets: [], hp: 100 };

    // keys
    const keys = {};

    // enemies + score
    const enemies = [];
    let score = 0;
    let gameOver = false;

    function spawnEnemy() {
      const side = Math.floor(Math.random() * 4);
      let x, y;
      if (side === 0) { x = 0; y = Math.random() * canvas.height; }
      else if (side === 1) { x = canvas.width; y = Math.random() * canvas.height; }
      else if (side === 2) { x = Math.random() * canvas.width; y = 0; }
      else { x = Math.random() * canvas.width; y = canvas.height; }

      enemies.push({ x, y, size: 15, speed: 1.2, hp: 1 });
    }
    setInterval(spawnEnemy, 1500);

    // key controls
    document.addEventListener("keydown", e => keys[e.key] = true);
    document.addEventListener("keyup", e => keys[e.key] = false);

    function shoot() {
      player.bullets.push({ x: player.x, y: player.y, dx: 0, dy: -5 });
    }
    document.addEventListener("keydown", e => { if (e.key === " ") shoot(); });

    function update() {
      if (gameOver) return;

      // movement
      if (keys["ArrowUp"]) player.y -= player.speed;
      if (keys["ArrowDown"]) player.y += player.speed;
      if (keys["ArrowLeft"]) player.x -= player.speed;
      if (keys["ArrowRight"]) player.x += player.speed;

      // keep inside screen
      player.x = Math.max(player.size, Math.min(canvas.width - player.size, player.x));
      player.y = Math.max(player.size, Math.min(canvas.height - player.size, player.y));

      // move bullets
      player.bullets.forEach(b => b.y += b.dy);

      // move enemies toward player
      enemies.forEach(e => {
        const dx = player.x - e.x;
        const dy = player.y - e.y;
        const dist = Math.sqrt(dx*dx + dy*dy);
        if (dist > 0) {
          e.x += (dx / dist) * e.speed;
          e.y += (dy / dist) * e.speed;
        }
      });

      // bullet-enemy collisions
      for (let b of player.bullets) {
        for (let e of enemies) {
          const dx = b.x - e.x, dy = b.y - e.y;
          if (Math.sqrt(dx*dx+dy*dy) < e.size) {
            e.hp = 0;
            b.y = -9999;
            score += 10;
          }
        }
      }

      // enemy-player collisions
      for (let e of enemies) {
        const dx = player.x - e.x, dy = player.y - e.y;
        if (Math.sqrt(dx*dx+dy*dy) < player.size + e.size) {
          e.hp = 0;
          player.hp -= 20;
          if (player.hp <= 0) gameOver = true;
        }
      }

      // clean up
      enemies.forEach((e,i)=>{ if(e.hp<=0) enemies.splice(i,1); });
      player.bullets = player.bullets.filter(b => b.y > 0);
    }

    function draw() {
      ctx.clearRect(0,0,canvas.width,canvas.height);

      // player
      ctx.fillStyle = "cyan";
      ctx.beginPath();
      ctx.arc(player.x, player.y, player.size, 0, Math.PI*2);
      ctx.fill();

      // bullets
      ctx.fillStyle = "yellow";
      player.bullets.forEach(b => ctx.fillRect(b.x-3, b.y-10, 6, 10));

      // enemies
      ctx.fillStyle = "red";
      enemies.forEach(e => {
        ctx.beginPath();
        ctx.arc(e.x, e.y, e.size, 0, Math.PI*2);
        ctx.fill();
      });

      // UI: score + health
      ctx.fillStyle = "white";
      ctx.font = "16px Arial";
      ctx.fillText("Score: " + score, 10, 20);

      ctx.fillStyle = "lime";
      ctx.fillRect(10, 30, player.hp * 2, 15);
      ctx.strokeStyle = "white";
      ctx.strokeRect(10, 30, 200, 15);

      // game over screen
      if (gameOver) {
        ctx.fillStyle = "rgba(0,0,0,0.7)";
        ctx.fillRect(0,0,canvas.width,canvas.height);
        ctx.fillStyle = "white";
        ctx.font = "30px Arial";
        ctx.fillText("GAME OVER", canvas.width/2 - 80, canvas.height/2);
        ctx.font = "20px Arial";
        ctx.fillText("Final Score: " + score, canvas.width/2 - 70, canvas.height/2 + 40);
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
</html> Kenny-gaming-
This project is for gaming 
