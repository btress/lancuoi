<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Fireworks with Text</title>
  <style>
    body {
      margin: 0;
      overflow: hidden;
      background-color: black;
    }
    canvas {
      display: block;
    }
  </style>
</head>
<body>
  <canvas id="fireworksCanvas"></canvas>
  <script>
    const canvas = document.getElementById('fireworksCanvas');
    const ctx = canvas.getContext('2d');
    canvas.width = window.innerWidth;
    canvas.height = window.innerHeight;

    class Rocket {
      constructor(x, y, color) {
        this.x = x;
        this.y = y;
        this.color = color;
        this.speedY = Math.random() * -6 - 8;
        this.particles = [];
        this.exploded = false;
      }
      update() {
        if (!this.exploded) {
          this.y += this.speedY;
          this.speedY += 0.2;
          if (this.speedY >= 0) {
            this.explode();
          }
        } else {
          this.particles.forEach((particle, index) => {
            particle.update();
            if (particle.alpha <= 0) {
              this.particles.splice(index, 1);
            }
          });
        }
      }
      explode() {
        this.exploded = true;
        for (let i = 0; i < 50; i++) {
          const angle = Math.random() * Math.PI * 2;
          const speed = Math.random() * 3 + 1;
          const color = this.color;
          this.particles.push(new Particle(this.x, this.y, color, speed, angle));
        }
      }
      draw() {
        if (!this.exploded) {
          ctx.fillStyle = this.color;
          ctx.beginPath();
          ctx.arc(this.x, this.y, 3, 0, Math.PI * 2);
          ctx.closePath();
          ctx.fill();
        } else {
          this.particles.forEach(particle => particle.draw());
        }
      }
    }

    class Particle {
      constructor(x, y, color, speed, angle) {
        this.x = x;
        this.y = y;
        this.color = color;
        this.speed = speed;
        this.angle = angle;
        this.alpha = 1;
        this.gravity = 0.02;
        this.size = Math.random() * 2 + 1;
      }
      update() {
        this.x += Math.cos(this.angle) * this.speed;
        this.y += Math.sin(this.angle) * this.speed + this.gravity;
        this.alpha -= 0.02;
      }
      draw() {
        ctx.globalAlpha = this.alpha;
        ctx.fillStyle = this.color;
        ctx.beginPath();
        ctx.arc(this.x, this.y, this.size, 0, Math.PI * 2);
        ctx.closePath();
        ctx.fill();
      }
    }

    const rockets = [];
    const colors = ['#ff6347', '#87cefa', '#32cd32', '#ff69b4', '#ffa500', '#1e90ff'];

    function createMultipleRockets() {
      for (let i = 0; i < 100; i++) { // Tạo 5 pháo hoa cùng lúc
        const x = Math.random() * canvas.width;
        const y = canvas.height;
        const color = colors[Math.floor(Math.random() * colors.length)];
        rockets.push(new Rocket(x, y, color));
      }
    }

    function drawText() {
      ctx.globalAlpha = 1;
      ctx.fillStyle = 'white';
      ctx.font = 'bold 60px Arial';
      ctx.textAlign = 'center';
      ctx.fillText('HAPPY NEW YEAR', canvas.width / 2, canvas.height / 2);
    }

    function animate() {
      ctx.fillStyle = 'rgba(0, 0, 0, 0.2)';
      ctx.fillRect(0, 0, canvas.width, canvas.height);

      rockets.forEach((rocket, index) => {
        rocket.update();
        rocket.draw();
        if (rocket.exploded && rocket.particles.length === 0) {
          rockets.splice(index, 1);
        }
      });

      drawText(); // Vẽ chữ ở giữa
      requestAnimationFrame(animate);
    }

    setInterval(createMultipleRockets, 800); // Tạo nhiều pháo hoa định kỳ
    animate();
  </script>
</body>
</html>
