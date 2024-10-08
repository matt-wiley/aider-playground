<script>
  let result = 0;
  let currentInput = '0';
  let operator = null;
  let previousInput = '';

  function clear() {
    currentInput = '';
    previousInput = '';
    operator = null;
    result = 0;
  }

  function inputNumber(number) {
    if (currentInput === '0') {
      currentInput = number;
    } else {
      currentInput = currentInput + number;
    }
  }

  function chooseOperation(selectedOperator) {
    if (currentInput === '') return;
    if (previousInput !== '') {
      compute();
    }
    operator = selectedOperator;
    previousInput = currentInput;
    currentInput = '';
  }

  function compute() {
    const prev = parseFloat(previousInput);
    const current = parseFloat(currentInput);
    if (isNaN(prev) || isNaN(current)) return;
    switch (operator) {
      case '+':
        result = prev + current;
        break;
      case '-':
        result = prev - current;
        break;
      default:
        return;
    }
    currentInput = result.toString();
    operator = null;
    previousInput = '';
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
  <input class="display" type="text" bind:value={currentInput} readonly />
  <div class="buttons">
    <button on:click={clear}>C</button>
    <button on:click={() => inputNumber('7')}>7</button>
    <button on:click={() => inputNumber('8')}>8</button>
    <button on:click={() => inputNumber('9')}>9</button>
    <button on:click={() => chooseOperation('+')}>+</button>
    <button on:click={() => inputNumber('4')}>4</button>
    <button on:click={() => inputNumber('5')}>5</button>
    <button on:click={() => inputNumber('6')}>6</button>
    <button on:click={() => chooseOperation('-')}>-</button>
    <button on:click={() => inputNumber('1')}>1</button>
    <button on:click={() => inputNumber('2')}>2</button>
    <button on:click={() => inputNumber('3')}>3</button>
    <button on:click={compute}>=</button>
    <button on:click={() => inputNumber('0')}>0</button>
  </div>
</div>
