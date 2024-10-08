<script lang="ts">
	import { onMount } from 'svelte';

	export let width = 400;
	export let height = 400;
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
		canvas.addEventListener('contextmenu', deleteNode);
		draw();
	});

	function addNode(event) {
		if (!event.ctrlKey) {
			const rect = canvas.getBoundingClientRect();
			const x = event.clientX - rect.left;
			const y = event.clientY - rect.top;
			nodes.push({ x, y });
			draw();
		}
	}

	function addEdge(event) {
		const rect = canvas.getBoundingClientRect();
		const x = event.clientX - rect.left;
		const y = event.clientY - rect.top;
		const node = nodes.find((node) => Math.hypot(node.x - x, node.y - y) < 10);

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
		dragNode = nodes.find((node) => Math.hypot(node.x - x, node.y - y) < 10);
		if (event.ctrlKey && dragNode) {
			selectedNode = dragNode;
		} else if (dragNode) {
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
		if (selectedNode) {
			const rect = canvas.getBoundingClientRect();
			const x = event.clientX - rect.left;
			const y = event.clientY - rect.top;
			const endNode = nodes.find((node) => Math.hypot(node.x - x, node.y - y) < 10);

			if (endNode && endNode !== selectedNode) {
				const startIndex = nodes.indexOf(selectedNode);
				const endIndex = nodes.indexOf(endNode);
				edges.push({ start: startIndex, end: endIndex });
			}
			selectedNode = null;
		}
		if (isDragging) {
			isDragging = false;
			dragNode = null;
		}
	}

	function deleteNode(event) {
		event.preventDefault();
		const rect = canvas.getBoundingClientRect();
		const x = event.clientX - rect.left;
		const y = event.clientY - rect.top;
		const nodeIndex = nodes.findIndex((node) => Math.hypot(node.x - x, node.y - y) < 10);

		if (nodeIndex !== -1) {
			nodes.splice(nodeIndex, 1);
			edges = edges.filter((edge) => edge.start !== nodeIndex && edge.end !== nodeIndex);

			// Update edge indices
			edges = edges.map((edge) => ({
				start: edge.start > nodeIndex ? edge.start - 1 : edge.start,
				end: edge.end > nodeIndex ? edge.end - 1 : edge.end
			}));

			draw();
		}
	}
	function draw() {
		const ctx = canvas.getContext('2d');
		ctx.clearRect(0, 0, canvas.width, canvas.height);

		// Draw edges
		ctx.strokeStyle = '#000';
		edges.forEach((edge) => {
			ctx.beginPath();
			ctx.moveTo(nodes[edge.start].x, nodes[edge.start].y);
			ctx.lineTo(nodes[edge.end].x, nodes[edge.end].y);
			ctx.stroke();
		});

		// Draw nodes
		nodes.forEach((node) => {
			ctx.fillStyle = '#007bff';
			ctx.beginPath();
			ctx.arc(node.x, node.y, 10, 0, Math.PI * 2);
			ctx.fill();
		});
	}

</script>

<canvas bind:this={canvas} {width} {height}></canvas>

<style>
	canvas {
		border: 1px solid #ccc;
		cursor: pointer;
	}
</style>
