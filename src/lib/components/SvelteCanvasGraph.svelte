<script>
  import { onMount } from 'svelte';

  let nodes = [
    { id: 'A', x: 300, y: 50 },
    { id: 'B', x: 200, y: 150 },
    { id: 'C', x: 400, y: 150 }
  ];
  let edges = [
    { from: 'A', to: 'B' },
    { from: 'A', to: 'C' }
  ];

  let selectedNode = null;
  let isDragging = false;
  let isAddingEdge = false;
  let edgeStart = null;
  let mode = 'move'; // 'move', 'addNode', 'addEdge', 'removeNode', 'removeEdge'

  let graphContainer;
  let graphWidth = 600;
  let graphHeight = 400;

  function handleNodeMouseDown(node, event) {
    if (mode === 'move') {
      selectedNode = node;
      isDragging = true;
    } else if (mode === 'removeNode') {
      nodes = nodes.filter(n => n !== node);
      edges = edges.filter(e => e.from !== node.id && e.to !== node.id);
    } else if (mode === 'addEdge') {
      isAddingEdge = true;
      edgeStart = node;
    }
    event.stopPropagation();
  }

  function handleMouseMove(event) {
    if (isDragging && selectedNode) {
      const rect = graphContainer.getBoundingClientRect();
      selectedNode.x = event.clientX - rect.left;
      selectedNode.y = event.clientY - rect.top;
      nodes = nodes; // trigger Svelte reactivity
    }
  }

  function handleMouseUp(event) {
    if (isAddingEdge && selectedNode && selectedNode !== edgeStart) {
      edges = [...edges, { from: edgeStart.id, to: selectedNode.id }];
    }
    isDragging = false;
    isAddingEdge = false;
    edgeStart = null;
  }

  function handleGraphClick(event) {
    if (mode === 'addNode') {
      const rect = graphContainer.getBoundingClientRect();
      const newId = String.fromCharCode(65 + nodes.length); // A, B, C, ...
      nodes = [...nodes, { 
        id: newId, 
        x: event.clientX - rect.left, 
        y: event.clientY - rect.top 
      }];
    }
  }

  function renameNode() {
    if (selectedNode) {
      const newId = prompt('Enter new node name:', selectedNode.id);
      if (newId && newId !== selectedNode.id) {
        edges.forEach(edge => {
          if (edge.from === selectedNode.id) edge.from = newId;
          if (edge.to === selectedNode.id) edge.to = newId;
        });
        selectedNode.id = newId;
        nodes = nodes; // trigger Svelte reactivity
      }
    } else {
      alert('Please select a node first.');
    }
  }

  function removeEdge() {
    if (selectedNode) {
      const edgeIndex = edges.findIndex(e => e.from === selectedNode.id || e.to === selectedNode.id);
      if (edgeIndex !== -1) {
        edges.splice(edgeIndex, 1);
        edges = edges; // trigger Svelte reactivity
      } else {
        alert('No edge connected to the selected node.');
      }
    } else {
      alert('Please select a node first.');
    }
  }

  $: edgePaths = edges.map(edge => {
    const fromNode = nodes.find(n => n.id === edge.from);
    const toNode = nodes.find(n => n.id === edge.to);
    return `M${fromNode.x},${fromNode.y} L${toNode.x},${toNode.y}`;
  });

  onMount(() => {
    const rect = graphContainer.getBoundingClientRect();
    graphWidth = rect.width;
    graphHeight = rect.height;
  });
</script>

<div class="graph-container" 
     bind:this={graphContainer}
     on:click={handleGraphClick}
     on:mousemove={handleMouseMove}
     on:mouseup={handleMouseUp}>
  <svg width="100%" height="100%">
    {#each edgePaths as path}
      <path d={path} stroke="black" stroke-width="2" fill="none" />
    {/each}
  </svg>
  {#each nodes as node (node.id)}
    <div class="node" 
         style="left: {node.x}px; top: {node.y}px;"
         on:mousedown={(e) => handleNodeMouseDown(node, e)}>
      {node.id}
    </div>
  {/each}
</div>

<div class="controls">
  <button on:click={() => mode = 'move'}>Move</button>
  <button on:click={() => mode = 'addNode'}>Add Node</button>
  <button on:click={() => mode = 'addEdge'}>Add Edge</button>
  <button on:click={() => mode = 'removeNode'}>Remove Node</button>
  <button on:click={removeEdge}>Remove Edge</button>
  <button on:click={renameNode}>Rename Node</button>
</div>

<style>
  .graph-container {
    width: 600px;
    height: 400px;
    border: 1px solid #ccc;
    position: relative;
    overflow: hidden;
  }
  .node {
    position: absolute;
    width: 60px;
    height: 40px;
    background-color: white;
    border: 2px solid black;
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 20px;
    cursor: pointer;
    user-select: none;
    transform: translate(-50%, -50%);
  }
  .controls {
    margin-top: 10px;
  }
  button {
    margin-right: 5px;
  }
</style>