<style>
  .container {
    display: flex;
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
  .navbar a {
    display: block;
    margin: 10px 0;
    text-decoration: none;
    color: #333;
  }
</style>

<div class="container">
  <nav class="navbar">
    <h2>Navbar</h2>
    <a href="/">Home</a>
    <a href="/components">Components</a>
    <a href="/components/test-component1">Test Component 1</a>
    <a href="/components/tree-canvas">Tree Canvas</a>
    <a href="/components/graph-canvas">Graph Canvas</a>
    <a href="/components/svelte-canvas-graph">Svelte Canvas Graph</a>
  </nav>
  <main class="content">
    <slot></slot>
  </main>
</div>
