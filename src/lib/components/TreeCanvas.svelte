<script>
  import { onMount } from 'svelte';

  let canvas;

  onMount(() => {
    const ctx = canvas.getContext('2d');

    // Draw the trunk
    ctx.fillStyle = '#8B4513'; // Brown color
    ctx.fillRect(90, 150, 20, 50);

    // Draw the leaves
    ctx.fillStyle = '#228B22'; // Forest green color
    ctx.beginPath();
    ctx.moveTo(100, 50);
    ctx.lineTo(50, 150);
    ctx.lineTo(150, 150);
    ctx.closePath();
    ctx.fill();
  });
</script>

<canvas bind:this={canvas} width="200" height="200"></canvas>

<style>
  canvas {
    border: 1px solid #ccc;
  }
</style>
