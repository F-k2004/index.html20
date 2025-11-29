<!DOCTYPE html>
<html lang="fa">
<head>
  <meta charset="UTF-8">
  <title>🌊 شبیه‌ساز آب واقعی</title>
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <style>
    body {
      margin: 0;
      verflow: hidden;
      background: #000;
    }
    canvas {
      display: block;
    }
  </style>
</head>

<body>

<canvas id="water"></canvas>

<script>
  const canvas = document.getElementById("water");
  const ctx = canvas.getContext("2d");

  let w, h;
  function resize() {
    w = canvas.width = window.innerWidth;
    h = canvas.height = window.innerHeight;
  }
  window.addEventListener("resize", resize);
  resize();

  // گرید شبیه‌سازی آب
  const cols = 180;
  const rows = 100;

  let water = [];
  let prev = [];

  function init() {
    for (let y = 0; y < rows; y++) {
      water[y] = [];
      prev[y] = [];
      for (let x = 0; x < cols; x++) {
        water[y][x] = 0;
        prev[y][x] = 0;
      }
    }
  }
  init();

  let damping = 0.985;

  // اضافه کردن موج با موس
  canvas.addEventListener("mousemove", (e) => {
    let x = Math.floor((e.clientX / w) * cols);
    let y = Math.floor((e.clientY / h) * rows);

    if (x > 1 && x < cols-1 && y > 1 && y < rows-1) {
      water[y][x] = 255;
    }
  });

  function update() {
    // محاسبه‌ی موج‌ها
    for (let y = 1; y < rows - 1; y++) {
      for (let x = 1; x < cols - 1; x++) {
        water[y][x] =
          ((prev[y-1][x] +
          prev[y+1][x] +
          prev[y][x-1] +
          prev[y][x+1]) / 2 - water[y][x]) * damping;
      }
    }

    // جابه‌جایی بافرها
    let temp = prev;
    prev = water;
    water = temp;
  }

  function draw() {
    let cellW = w / cols;
    let cellH = h / rows;

    ctx.clearRect(0, 0, w, h);

    for (let y = 1; y < rows - 1; y++) {
      for (let x = 1; x < cols - 1; x++) {
        let v = water[y][x];

        let brightness = v * 2 + 100;  
        if (brightness < 0) brightness = 0;
        if (brightness > 255) brightness = 255;

        ctx.fillStyle = `rgb(0, ${brightness}, ${255})`;
        ctx.fillRect(x * cellW, y * cellH, cellW, cellH);
      }
    }
  }

  function animate() {
    update();
    draw();
    requestAnimationFrame(animate);
  }

  animate();
</script>

</body>
</html>
