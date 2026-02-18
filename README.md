<!DOCTYPE html>
<html>
<head>
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Mobile Runner Game</title>
<style>
body {
  margin: 0;
  background: #222;
  overflow: hidden;
  touch-action: manipulation;
}
canvas {
  display: block;
  margin: auto;
  background: #333;
}
</style>
</head>
<body>

<canvas id="game" width="360" height="600"></canvas>

<script>
const canvas = document.getElementById("game");
const ctx = canvas.getContext("2d");

const WIDTH = canvas.width;
const HEIGHT = canvas.height;
const LANE = WIDTH / 3;

let playerLane = 1;
let speed = 4;
let score = 0;
let coins = 0;
let obstacles = [];
let coinItems = [];
let gameOver = false;

const player = {
  y: HEIGHT - 90,
  w: LANE - 40,
  h: 70
};

function spawnObstacle() {
  if (Math.random() < 0.03) {
    obstacles.push({
      lane: Math.floor(Math.random() * 3),
      y: -60
    });
  }
}

function spawnCoin() {
  if (Math.random() < 0.02) {
    coinItems.push({
      lane: Math.floor(Math.random() * 3),
      y: -40
    });
  }
}

canvas.addEventListener("touchstart", e => {
  const x = e.touches[0].clientX;
  if (x < window.innerWidth / 2 && playerLane > 0) playerLane--;
  if (x >= window.innerWidth / 2 && playerLane < 2) playerLane++;
});

function update() {
  if (gameOver) return;

  spawnObstacle();
  spawnCoin();

  obstacles.forEach(o => o.y += speed);
  coinItems.forEach(c => c.y += speed);

  obstacles = obstacles.filter(o => o.y < HEIGHT);
  coinItems = coinItems.filter(c => c.y < HEIGHT);

  const px = playerLane * LANE + 20;

  obstacles.forEach(o => {
    const ox = o.lane * LANE + 20;
    if (
      px < ox + player.w &&
      px + player.w > ox &&
      player.y < o.y + 60 &&
      player.y + player.h > o.y
    ) {
      gameOver = true;
    }
  });

  coinItems.forEach((c, i) => {
    const cx = c.lane * LANE + 40;
    if (
      px < cx + 30 &&
      px + player.w > cx &&
      player.y < c.y + 30 &&
      player.y + player.h > c.y
    ) {
      coinItems.splice(i, 1);
      coins++;
    }
  });

  score++;
  speed += 0.002;
}

function draw() {
  ctx.clearRect(0, 0, WIDTH, HEIGHT);

  ctx.fillStyle = "#fff";
  for (let i = 1; i < 3; i++) {
    ctx.fillRect(LANE * i, 0, 2, HEIGHT);
  }

  ctx.fillStyle = "#4aa3ff";
  ctx.fillRect(playerLane * LANE + 20, player.y, player.w, player.h);

  ctx.fillStyle = "red";
  obstacles.forEach(o => {
    ctx.fillRect(o.lane * LANE + 20, o.y, player.w, 60);
  });

  ctx.fillStyle = "gold";
  coinItems.forEach(c => {
    ctx.beginPath();
    ctx.arc(c.lane * LANE + LANE/2, c.y + 15, 12, 0, Math.PI * 2);
    ctx.fill();
  });

  ctx.fillStyle = "#fff";
  ctx.fillText("Score: " + score, 10, 20);
  ctx.fillText("Coins: " + coins, 10, 40);

  if (gameOver) {
    ctx.fillStyle = "red";
    ctx.font = "30px Arial";
    ctx.fillText("GAME OVER", 90, 300);
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
