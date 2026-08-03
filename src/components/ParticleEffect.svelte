<script>
  import { onMount } from 'svelte';

  let canvas;
  let particles = [];
  const maxParticles = 30;

  class Particle {
    constructor(x, y) {
      this.x = x;
      this.y = y;
      this.size = Math.random() * 3 + 1;
      this.speedX = Math.random() * 2 - 1;
      this.speedY = Math.random() * 2 - 1;
      this.life = 1;
      this.decay = Math.random() * 0.02 + 0.01;
      this.color = `hsl(${Math.random() * 60 + 250}, 80%, 70%)`;
    }

    update() {
      this.x += this.speedX;
      this.y += this.speedY;
      this.life -= this.decay;
      this.size *= 0.99;
    }

    draw(ctx) {
      ctx.save();
      ctx.globalAlpha = this.life;
      ctx.fillStyle = this.color;
      ctx.shadowBlur = 10;
      ctx.shadowColor = this.color;
      ctx.beginPath();
      ctx.arc(this.x, this.y, this.size, 0, Math.PI * 2);
      ctx.fill();
      ctx.restore();
    }
  }

  let mouseX = -100;
  let mouseY = -100;

  function handleMouse(e) {
    mouseX = e.clientX;
    mouseY = e.clientY;
    for (let i = 0; i < 3; i++) {
      particles.push(new Particle(mouseX, mouseY));
    }
  }

  function animate(ctx) {
    ctx.clearRect(0, 0, canvas.width, canvas.height);
    
    for (let i = particles.length - 1; i >= 0; i--) {
      particles[i].update();
      particles[i].draw(ctx);
      if (particles[i].life <= 0) {
        particles.splice(i, 1);
      }
    }
    
    requestAnimationFrame(() => animate(ctx));
  }

  onMount(() => {
    canvas.width = window.innerWidth;
    canvas.height = window.innerHeight;

    const ctx = canvas.getContext('2d');
    window.addEventListener('mousemove', handleMouse);
    animate(ctx);

    return () => {
      window.removeEventListener('mousemove', handleMouse);
    };
  });
</script>

<canvas
  bind:this={canvas}
  style="position:fixed; top:0; left:0; pointer-events:none; z-index:9999;"
/>