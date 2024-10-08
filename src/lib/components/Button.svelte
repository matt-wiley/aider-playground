<script>
  export let label = 'Click me';
  export let onClick = () => {};
</script>

<style>
  button {
    padding: 10px 20px;
    background-color: #007bff;
    color: white;
    border: none;
    border-radius: 5px;
    cursor: pointer;
  }

  button:hover {
    background-color: #0056b3;
  }
</style>

<button on:click={onClick}>
  {label}
</button>
