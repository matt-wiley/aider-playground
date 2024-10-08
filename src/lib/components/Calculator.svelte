<script>
  let result = 0;
  let num1 = 0;
  let num2 = 0;

  function add() {
    result = num1 + num2;
  }
</script>

<div>
  <h2>Calculator</h2>
  <input type="number" bind:value={num1} placeholder="Number 1" />
  <input type="number" bind:value={num2} placeholder="Number 2" />
  <button on:click={add}>Add</button>
  <p>Result: {result}</p>
</div>
