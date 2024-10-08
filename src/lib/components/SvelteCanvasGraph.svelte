<script>
  import { onMount } from 'svelte';

  let nodes = [
    { id: 'A', x: 300, y: 50 },
    { id: 'B', x: 200, y: 150 },
    { id: 'C', x: 400, y: 150 }
  ];
  let edges = [
    { id: '1', from: 'A', to: 'B', label: 'Edge 1' },
    { id: '2', from: 'A', to: 'C', label: 'Edge 2' }
  ];

  let selectedNode = null;
  let selectedEdge = null;
  let isDragging = false;
  let isAddingEdge = false;
  let edgeStart = null;
  let mode = 'move'; // 'move', 'addNode', 'addEdge', 'removeNode'

  let graphContainer;
  let graphWidth = 600;
  let graphHeight = 400;

  let showEdgeMenu = false;
  let edgeMenuPosition = { x: 0, y: 0 };

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
      const newEdgeId = (edges.length + 1).toString();
      edges = [...edges, { id: newEdgeId, from: edgeStart.id, to: selectedNode.id, label: `Edge ${newEdgeId}` }];
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
    showEdgeMenu = false; // Hide edge menu when clicking elsewhere
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
        edges = edges; // trigger Svelte reactivity
      }
    } else {
      alert('Please select a node first.');
    }
  }

  function handleEdgeClick(edge, event) {
    selectedEdge = edge;
    showEdgeMenu = true;
    edgeMenuPosition = { x: event.clientX, y: event.clientY };
    event.stopPropagation();
  }

  function deleteEdge() {
    if (selectedEdge) {
      edges = edges.filter(e => e !== selectedEdge);
      showEdgeMenu = false;
    }
  }

  function renameEdge() {
    if (selectedEdge) {
      const newLabel = prompt('Enter new edge label:', selectedEdge.label);
      if (newLabel && newLabel !== selectedEdge.label) {
        selectedEdge.label = newLabel;
        edges = edges; // trigger Svelte reactivity
      }
      showEdgeMenu = false;
    }
  }

  $: edgePaths = edges.map(edge => {
    const fromNode = nodes.find(n => n.id === edge.from);
    const toNode = nodes.find(n => n.id === edge.to);
    const midX = (fromNode.x + toNode.x) / 2;
    const midY = (fromNode.y + toNode.y) / 2;
    return {
      path: `M${fromNode.x},${fromNode.y} L${toNode.x},${toNode.y}`,
      labelX: midX,
      labelY: midY,
      edge
    };
  });


  function isTree() {
    // Step 1: Check if the number of edges is correct for a tree
    if (edges.length !== nodes.length - 1) {
      return false;
    }

    // Step 2: Check if the graph is connected and has no cycles using DFS
    const adjacencyList = {};
    nodes.forEach(node => {
      adjacencyList[node.id] = [];
    });
    edges.forEach(edge => {
      adjacencyList[edge.from].push(edge.to);
      adjacencyList[edge.to].push(edge.from);
    });

    const visited = new Set();
    function dfs(node, parent) {
      if (visited.has(node)) {
        return false; // Cycle detected
      }
      visited.add(node);
      for (const neighbor of adjacencyList[node]) {
        if (neighbor !== parent) {
          if (!dfs(neighbor, node)) {
            return false;
          }
        }
      }
      return true;
    }

    // Start DFS from the first node
    const startNode = nodes[0].id;
    if (!dfs(startNode, null)) {
      return false;
    }

    // Check if all nodes were visited (graph is connected)
    return visited.size === nodes.length;
  }


  function reorganizeAsTree() {
    if (!isTree()) {
      alert("The graph is not a tree. Cannot reorganize.");
      return;
    }

    // Find the root (node with no incoming edges)
    const root = nodes.find(node => !edges.some(edge => edge.to === node.id));
    if (!root) {
      alert("Cannot find root node. Unable to reorganize.");
      return;
    }

    // Create adjacency list
    const adjacencyList = {};
    nodes.forEach(node => {
      adjacencyList[node.id] = [];
    });
    edges.forEach(edge => {
      adjacencyList[edge.from].push(edge.to);
    });

    // Set up layout parameters
    const levelHeight = 100;
    const nodeWidth = 60;
    let maxNodesInLevel = 1;

    // Perform BFS to assign levels and positions
    const queue = [{node: root.id, level: 0, index: 0}];
    const processed = new Set();
    const nodeLevels = {};
    const nodeIndices = {};

    while (queue.length > 0) {
      const {node, level, index} = queue.shift();
      nodeLevels[node] = level;
      nodeIndices[node] = index;
      processed.add(node);

      const children = adjacencyList[node].filter(child => !processed.has(child));
      maxNodesInLevel = Math.max(maxNodesInLevel, children.length);

      children.forEach((child, i) => {
        queue.push({node: child, level: level + 1, index: i});
      });
    }

    // Update node positions
    nodes = nodes.map(node => ({
      ...node,
      y: nodeLevels[node.id] * levelHeight + 50,
      x: (nodeIndices[node.id] + 1) * (graphWidth / (maxNodesInLevel + 1))
    }));

    // Trigger Svelte reactivity
    nodes = nodes;
  }


  function checkIfTree() {
    const result = isTree();
    alert(result ? "The graph is a tree!" : "The graph is not a tree.");
  }


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
    {#each edgePaths as {path, labelX, labelY, edge}}
      <g on:click={(e) => handleEdgeClick(edge, e)}>
        <path d={path} stroke="black" stroke-width="2" fill="none" />
        <text x={labelX} y={labelY} text-anchor="middle" alignment-baseline="middle" fill="blue">
          {edge.label}
        </text>
      </g>
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

{#if showEdgeMenu}
  <div class="edge-menu" style="left: {edgeMenuPosition.x}px; top: {edgeMenuPosition.y}px;">
    <button on:click={deleteEdge}>Delete</button>
    <button on:click={renameEdge}>Rename</button>
    <button on:click={() => showEdgeMenu = false}>Cancel</button>
  </div>
{/if}

<div class="controls">
  <button on:click={() => mode = 'move'}>Move</button>
  <button on:click={() => mode = 'addNode'}>Add Node</button>
  <button on:click={() => mode = 'addEdge'}>Add Edge</button>
  <button on:click={() => mode = 'removeNode'}>Remove Node</button>
  <button on:click={renameNode}>Rename Node</button>
  <button on:click={checkIfTree}>Check if Tree</button>
  <button on:click={reorganizeAsTree}>Reorganize as Tree</button>
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
  .edge-menu {
    position: fixed;
    background-color: white;
    border: 1px solid black;
    padding: 5px;
    z-index: 1000;
  }
</style>