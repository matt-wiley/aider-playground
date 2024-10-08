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
    <a href="/test-component1">Test Component 1</a>
  </nav>
  <main class="content">
    <slot></slot>
  </main>
</div>
