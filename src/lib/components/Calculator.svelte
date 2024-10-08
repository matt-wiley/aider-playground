<script>
  let result = 0;
  let num1 = 0;
  let num2 = 0;

  function add() {
    result = num1 + num2;
  }
</script>

<style>
  .calculator {
    width: 300px;
    margin: 20px auto;
    padding: 20px;
    border-radius: 8px;
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
    background-color: #f9f9f9;
  }
  .display {
    width: 100%;
    height: 40px;
    margin-bottom: 10px;
    padding: 5px;
    font-size: 1.2em;
    text-align: right;
    border: 1px solid #ccc;
    border-radius: 4px;
    background-color: #fff;
  }
  .buttons {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    gap: 10px;
  }
  button {
    padding: 10px;
    font-size: 1em;
    border: none;
    border-radius: 4px;
    background-color: #e0e0e0;
    cursor: pointer;
    transition: background-color 0.3s;
  }
  button:hover {
    background-color: #d0d0d0;
  }
</style>

<div class="calculator">
  <h2>Calculator</h2>
  <input class="display" type="text" bind:value={result} readonly />
  <div class="buttons">
    <button on:click={() => (num1 = 0, num2 = 0, result = 0)}>C</button>
    <button on:click={() => (num1 = num1 * 10 + 7)}>7</button>
    <button on:click={() => (num1 = num1 * 10 + 8)}>8</button>
    <button on:click={() => (num1 = num1 * 10 + 9)}>9</button>
    <button on:click={() => (num1 = num1 * 10 + 4)}>4</button>
    <button on:click={() => (num1 = num1 * 10 + 5)}>5</button>
    <button on:click={() => (num1 = num1 * 10 + 6)}>6</button>
    <button on:click={() => (num1 = num1 * 10 + 1)}>1</button>
    <button on:click={() => (num1 = num1 * 10 + 2)}>2</button>
    <button on:click={() => (num1 = num1 * 10 + 3)}>3</button>
    <button on:click={() => (num1 = num1 * 10)}>0</button>
    <button on:click={add}>+</button>
  </div>
</div>
