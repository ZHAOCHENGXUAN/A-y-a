<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Heart Animation for Aya</title>
  <style>
    canvas {
      background-color: black;
      display: block;
      margin: 0 auto;
    }
    body {
      margin: 0;
      overflow: hidden;
    }
  </style>
</head>
<body>
<canvas id="heartCanvas" width="640" height="640"></canvas>
<script>
const canvas = document.getElementById('heartCanvas');
const ctx = canvas.getContext('2d');

const CENTER_X = canvas.width / 2;
const CENTER_Y = canvas.height / 2;
const IMAGE_ENLARGE = 11;
const HEART_COLOR = '#ff7171';

function heartFunction(t, scale = IMAGE_ENLARGE) {
  let x = 16 * Math.pow(Math.sin(t), 3);
  let y = -(13 * Math.cos(t) - 5 * Math.cos(2 * t) - 2 * Math.cos(3 * t) - Math.cos(4 * t));
  x *= scale;
  y *= scale;
  x += CENTER_X;
  y += CENTER_Y;
  return { x, y };
}

function curve(p) {
  return 2 * (3 * Math.sin(4 * p)) / (2 * Math.PI);
}

function randomInt(min, max) {
  return Math.floor(Math.random() * (max - min + 1)) + min;
}

function generateHeartPoints() {
  const points = [];
  const edgePoints = [];
  const centerPoints = [];

  for (let i = 0; i < 2000; i++) {
    let t = Math.random() * 2 * Math.PI;
    points.push(heartFunction(t));
  }

  for (const pt of points) {
    for (let i = 0; i < 3; i++) {
      const beta = 0.05;
      const ratioX = -beta * Math.log(Math.random());
      const ratioY = -beta * Math.log(Math.random());
      const dx = ratioX * (pt.x - CENTER_X);
      const dy = ratioY * (pt.y - CENTER_Y);
      edgePoints.push({ x: pt.x - dx, y: pt.y - dy });
    }
  }

  for (let i = 0; i < 4000; i++) {
    const pt = points[randomInt(0, points.length - 1)];
    const beta = 0.17;
    const ratioX = -beta * Math.log(Math.random());
    const ratioY = -beta * Math.log(Math.random());
    const dx = ratioX * (pt.x - CENTER_X);
    const dy = ratioY * (pt.y - CENTER_Y);
    centerPoints.push({ x: pt.x - dx, y: pt.y - dy });
  }

  return { points, edgePoints, centerPoints };
}

const heartData = generateHeartPoints();
let frame = 0;

function animate() {
  ctx.clearRect(0, 0, canvas.width, canvas.height);


  const ratio = 10 * curve((frame / 100) * Math.PI);

  function calcPos(pt) {
    const dist2 = Math.pow(pt.x - CENTER_X, 2) + Math.pow(pt.y - CENTER_Y, 2);
    const force = 1 / Math.pow(dist2, 0.52);
    const dx = ratio * force * (pt.x - CENTER_X) + randomInt(-1, 1);
    const dy = ratio * force * (pt.y - CENTER_Y) + randomInt(-1, 1);
    return { x: pt.x - dx, y: pt.y - dy };
  }

  function drawPoints(points, sizeRange) {
    for (const pt of points) {
      const { x, y } = calcPos(pt);
      const size = randomInt(sizeRange[0], sizeRange[1]);
      ctx.fillStyle = HEART_COLOR;
      ctx.fillRect(x, y, size, size);
    }
  }

  drawPoints(heartData.points, [1, 3]);
  drawPoints(heartData.edgePoints, [1, 2]);
  drawPoints(heartData.centerPoints, [1, 2]);

  // Draw text in the center
  ctx.fillStyle = 'white';
  ctx.font = 'bold 24px Helvetica';
  ctx.textAlign = 'center';
  ctx.fillText('for Aya Failali Jatabi', CENTER_X, CENTER_Y);

  frame++;
  requestAnimationFrame(animate);
}

animate();
</script>
</body>
</html>
