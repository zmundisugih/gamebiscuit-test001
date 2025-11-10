<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Bouncing Smiley</title>
  <style>
    :root{
      --bg:#0f1724;
      --panel: rgba(255,255,255,0.06);
      --accent: #ffd166;
    }
    html,body{
      height:100%;
      margin:0;
      font-family: system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial;
      background: linear-gradient(180deg,var(--bg), #071022 120%);
      color: #e6eef8;
      -webkit-font-smoothing:antialiased;
      -moz-osx-font-smoothing:grayscale;
    }
    .wrap{
      height:100vh;
      display:grid;
      grid-template-rows: auto 1fr auto;
      gap:12px;
      padding:16px;
      box-sizing:border-box;
    }
    header{
      display:flex;
      align-items:center;
      gap:12px;
    }
    h1{
      margin:0;
      font-size:18px;
      font-weight:600;
    }
    .panel{
      background:var(--panel);
      border-radius:12px;
      padding:10px;
      display:flex;
      align-items:center;
      gap:12px;
      box-shadow: 0 6px 18px rgba(2,6,23,0.6), inset 0 1px 0 rgba(255,255,255,0.02);
    }
    main{
      display:flex;
      gap:12px;
      align-items:center;
      justify-content:center;
    }
    canvas{
      border-radius:12px;
      background: linear-gradient(180deg, rgba(255,255,255,0.02), rgba(255,255,255,0.0));
      box-shadow: 0 10px 30px rgba(2,6,23,0.6);
      max-width:100%;
      height:auto;
    }
    footer{
      display:flex;
      gap:12px;
      align-items:center;
      justify-content:space-between;
      flex-wrap:wrap;
    }
    label{
      font-size:13px;
      display:flex;
      gap:8px;
      align-items:center;
      color: #cfe6ff;
    }
    input[type="range"]{
      accent-color: var(--accent);
    }
    button{
      background:transparent;
      border:1px solid rgba(255,255,255,0.06);
      color:inherit;
      padding:8px 12px;
      border-radius:10px;
      cursor:pointer;
    }
    .small{
      font-size:13px;
      opacity:0.9;
    }
    @media (max-width:600px){
      .wrap{ padding:10px; }
      canvas{ width:100%; height:50vh; }
    }
  </style>
</head>
<body>
  <div class="wrap">
    <header>
      <div class="panel" role="banner" aria-label="Bouncing smiley demo">
        <h1>Bouncing Smiley — demo</h1>
        <div class="small">Click canvas to pause / resume</div>
      </div>
    </header>

    <main>
      <!-- Canvas where the smiley bounces -->
      <canvas id="stage" width="900" height="500" aria-label="Animated bouncing smiley"></canvas>
    </main>

    <footer>
      <div class="panel small" style="gap:10px;">
        <label>
          Speed
          <input id="speed" type="range" min="0" max="6" step="0.1" value="2.5" />
        </label>
        <label>
          Size
          <input id="size" type="range" min="30" max="180" step="1" value="90" />
        </label>
        <button id="reset">Reset</button>
      </div>

      <div class="small">Tip: Open devtools (F12) to see FPS. Works offline — just open file in browser.</div>
    </footer>
  </div>

  <script>
    (function(){
      const canvas = document.getElementById('stage');
      const ctx = canvas.getContext('2d');
      const speedInput = document.getElementById('speed');
      const sizeInput = document.getElementById('size');
      const resetBtn = document.getElementById('reset');

      // High-DPI support
      function resizeCanvas() {
        const dpr = Math.max(1, window.devicePixelRatio || 1);
        // maintain aspect ratio while scaling
        const rect = canvas.getBoundingClientRect();
        canvas.width = Math.round(rect.width * dpr);
        canvas.height = Math.round(rect.height * dpr);
        ctx.setTransform(dpr, 0, 0, dpr, 0, 0);
      }

      // If canvas has no CSS size set, give it a responsive size
      function ensureCssSize() {
        if (!canvas.style.width) {
          canvas.style.width = Math.min(window.innerWidth - 64, 1200) + 'px';
          canvas.style.height = Math.min(window.innerHeight - 220, 600) + 'px';
        }
      }
      ensureCssSize();
      resizeCanvas();
      window.addEventListener('resize', () => {
        resizeCanvas();
      });

      // Smiley properties
      const ball = {
        x: canvas.width/2 / (window.devicePixelRatio || 1),
        y: canvas.height/2 / (window.devicePixelRatio || 1),
        vx: 150, // px/s
        vy: 90,  // px/s
        radius: Number(sizeInput.value), // px
        colorFace: '#ffd166',
        colorStroke: '#e08900',
      };

      // convert slider speed to multiplier
      function speedMultiplier() {
        return Number(speedInput.value) / 2.5; // 1.0 is default
      }

      // Draw a smiley face at (x,y) with given radius on ctx
      function drawSmiley(ctx, x, y, r) {
        // face
        ctx.beginPath();
        ctx.fillStyle = ball.colorFace;
        ctx.strokeStyle = ball.colorStroke;
        ctx.lineWidth = Math.max(2, r * 0.06);
        ctx.ellipse(x, y, r, r, 0, 0, Math.PI * 2);
        ctx.fill();
        ctx.stroke();

        // eyes
        const eyeOffsetX = r * 0.45;
        const eyeOffsetY = r * -0.2;
        const eyeR = Math.max(2, r * 0.12);

        ctx.beginPath();
        ctx.fillStyle = '#111827';
        ctx.ellipse(x - eyeOffsetX, y + eyeOffsetY, eyeR, eyeR * 1.1, 0, 0, Math.PI * 2);
        ctx.ellipse(x + eyeOffsetX, y + eyeOffsetY, eyeR, eyeR * 1.1, 0, 0, Math.PI * 2);
        ctx.fill();

        // smile (arc)
        ctx.beginPath();
        const smileR = r * 0.6;
        ctx.lineWidth = Math.max(2, r * 0.08);
        ctx.strokeStyle = '#111827';
        ctx.arc(x, y + r * 0.12, smileR, Math.PI * 0.15, Math.PI * 0.85, false);
        ctx.stroke();
      }

      let lastTime = performance.now();
      let paused = false;

      // Initialize ball position relative to CSS pixel coordinates
      function resetBall() {
        const rect = canvas.getBoundingClientRect();
        ball.radius = Number(sizeInput.value);
        ball.x = rect.width * 0.25 + Math.random() * rect.width * 0.5;
        ball.y = rect.height * 0.3 + Math.random() * rect.height * 0.4;

        // random velocity direction scaled by slider
        const baseSpeed = 150;
        const mult = speedMultiplier();
        const angle = Math.random() * Math.PI * 2;
        ball.vx = Math.cos(angle) * baseSpeed * mult;
        ball.vy = Math.sin(angle) * baseSpeed * mult;
      }

      resetBtn.addEventListener('click', () => {
        speedInput.value = 2.5;
        sizeInput.value = 90;
        resetBall();
      });

      // Pause / resume on click
      canvas.addEventListener('click', () => {
        paused = !paused;
      });

      // Update slider live
      speedInput.addEventListener('input', () => {
        const mult = speedMultiplier();
        // scale velocity while preserving direction
        const speed = Math.hypot(ball.vx, ball.vy);
        const dirX = ball.vx / (speed || 1);
        const dirY = ball.vy / (speed || 1);
        const baseSpeed = 150;
        ball.vx = dirX * baseSpeed * mult;
        ball.vy = dirY * baseSpeed * mult;
      });

      sizeInput.addEventListener('input', () => {
        ball.radius = Number(sizeInput.value);
      });

      // Main animation loop
      function loop(now) {
        const dt = (now - lastTime) / 1000; // seconds
        lastTime = now;

        // clear
        const rect = canvas.getBoundingClientRect();
        ctx.clearRect(0, 0, rect.width, rect.height);

        if (!paused) {
          // update position
          ball.x += ball.vx * dt;
          ball.y += ball.vy * dt;

          // collisions with walls (simple elastic)
          if (ball.x - ball.radius < 0) {
            ball.x = ball.radius;
            ball.vx = Math.abs(ball.vx);
          } else if (ball.x + ball.radius > rect.width) {
            ball.x = rect.width - ball.radius;
            ball.vx = -Math.abs(ball.vx);
          }
          if (ball.y - ball.radius < 0) {
            ball.y = ball.radius;
            ball.vy = Math.abs(ball.vy);
          } else if (ball.y + ball.radius > rect.height) {
            ball.y = rect.height - ball.radius;
            ball.vy = -Math.abs(ball.vy);
          }
        }

        // draw smiley
        drawSmiley(ctx, ball.x, ball.y, ball.radius);

        requestAnimationFrame(loop);
      }

      // Fix initial CSS sizing if canvas had default width/height attributes
      // set a responsive CSS size if not provided by style
      (function makeInitialSize(){
        const rect = canvas.getBoundingClientRect();
        if (rect.width === 0 || rect.height === 0) {
          // set reasonable defaults
          canvas.style.width = '900px';
          canvas.style.height = '500px';
          resizeCanvas();
        }
      })();

      // Start
      lastTime = performance.now();
      requestAnimationFrame(loop);

      // Ensure ball resets when window resizes to keep within bounds
      window.addEventListener('resize', () => {
        resizeCanvas();
        // keep ball inside
        const rect = canvas.getBoundingClientRect();
        ball.x = Math.min(Math.max(ball.radius, ball.x), rect.width - ball.radius);
        ball.y = Math.min(Math.max(ball.radius, ball.y), rect.height - ball.radius);
      });

      // Small accessibility: keyboard p toggles pause
      window.addEventListener('keydown', (e) => {
        if (e.key === 'p' || e.key === ' ') {
          paused = !paused;
        }
      });
    })();
  </script>
</body>
</html>
