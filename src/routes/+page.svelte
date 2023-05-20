<script>
	import Inputs from "./inputs.svelte";
	import Results from "./results.svelte";
	let resultObject;
	let isCalculated;
</script>
<Inputs bind:resultObject bind:isCalculated />
{#if isCalculated}
	<Results bind:resultObject />
{/if}
