<script>
  // Add any necessary script logic here

	import Button from "$lib/components/Button.svelte";

  function handleClick() {
    alert("Button clicked!");
  }

</script>

<div class="test-component1">
  <h1>Test Component 1</h1>
  <p>This is the Test Component 1 page.</p>
  <Button label="Click me" onClick={handleClick} />
</div>

<style>
  .test-component1 {
    padding: 15px;
  }
</style>