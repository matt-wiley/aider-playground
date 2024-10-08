<script>
  export let data;
</script>

<style>
  .container {
    display: flex;
    padding: 10px;
    border-radius: 4px;
    transition: background-color 0.3s, color 0.3s;
  }
  .navbar a:hover {
    background-color: #ddd;
    color: #000;
  }
  .navbar {
    width: 200px;
    background-color: #f4f4f4;
    padding: 15px;
  }
  .content {
    flex-grow: 1;
    padding: 15px;
  }
  .sidebar a {
    display: block;
    margin: 10px 0;
    text-decoration: none;
    color: #333;
  }
</style>

<div class="container">
  <nav class="navbar">
    <ul>
      <li><a href="/components/svelte-canvas-graph">Svelte Canvas Graph</a></li>
      <li><a href="/components/test-component1">Test Component 1</a></li>
      <li><a href="/components/tree-canvas">Tree Canvas</a></li>
    </ul>
  </nav>
  <main class="content">
    <slot />
  </main>
</div>
