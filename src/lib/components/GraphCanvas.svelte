<script>
  import { onMount } from 'svelte';

  let canvas;
  let nodes = [];
  let edges = [];
  let isDragging = false;
  let dragNode = null;
  let selectedNode = null;

  onMount(() => {
    const ctx = canvas.getContext('2d');
    canvas.addEventListener('click', addNode);
    canvas.addEventListener('mousedown', startDrag);
    canvas.addEventListener('mousemove', drag);
    canvas.addEventListener('mouseup', endDrag);
    canvas.addEventListener('dblclick', addEdge);
    draw();
  });

  function addNode(event) {
    const rect = canvas.getBoundingClientRect();
    const x = event.clientX - rect.left;
    const y = event.clientY - rect.top;
    nodes.push({ x, y });
    draw();
  }

  function addEdge(event) {
    const rect = canvas.getBoundingClientRect();
    const x = event.clientX - rect.left;
    const y = event.clientY - rect.top;
    const node = nodes.find(node => Math.hypot(node.x - x, node.y - y) < 10);

    if (node) {
      if (selectedNode) {
        if (selectedNode !== node) {
          const startIndex = nodes.indexOf(selectedNode);
          const endIndex = nodes.indexOf(node);
          edges.push({ start: startIndex, end: endIndex });
          draw();
        }
        selectedNode = null;
      } else {
        selectedNode = node;
      }
    }
  }

  function startDrag(event) {
    const rect = canvas.getBoundingClientRect();
    const x = event.clientX - rect.left;
    const y = event.clientY - rect.top;
    dragNode = nodes.find(node => Math.hypot(node.x - x, node.y - y) < 10);
    if (dragNode) {
      isDragging = true;
    }
  }

  function drag(event) {
    if (isDragging && dragNode) {
      const rect = canvas.getBoundingClientRect();
      dragNode.x = event.clientX - rect.left;
      dragNode.y = event.clientY - rect.top;
      draw();
    }
  }

  function endDrag() {
    isDragging = false;
    dragNode = null;
  }

  function draw() {
    const ctx = canvas.getContext('2d');
    ctx.clearRect(0, 0, canvas.width, canvas.height);

    // Draw edges
    ctx.strokeStyle = '#000';
    edges.forEach(edge => {
      ctx.beginPath();
      ctx.moveTo(nodes[edge.start].x, nodes[edge.start].y);
      ctx.lineTo(nodes[edge.end].x, nodes[edge.end].y);
      ctx.stroke();
    });

    // Draw nodes
    nodes.forEach(node => {
      ctx.fillStyle = '#007bff';
      ctx.beginPath();
      ctx.arc(node.x, node.y, 10, 0, Math.PI * 2);
      ctx.fill();
    });
  }
</script>

<canvas bind:this={canvas} width="400" height="400"></canvas>

<style>
  canvas {
    border: 1px solid #ccc;
    cursor: pointer;
  }
</style>
