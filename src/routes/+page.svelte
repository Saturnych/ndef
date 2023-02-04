<script lang="ts">
	import Button, { Label, Icon } from '@smui/button';

	let clicked = 0;

	function handleClick(event: CustomEvent | MouseEvent) {
		event = event as MouseEvent;
		if (event.button === 0) {
			clicked++;
		}
	}

	function reset() {
		clicked = 0;
	}

	//console.log('window.location:', window.location);

	let ndef;
	try {
	  ndef = new NDEFReader();
	} catch (err) {
	  console.error("ndef error:", err.message);
	}
	
	if (ndef) {
		console.log("ndef:", ndef);
  }
</script>

{JSON.stringify(ndef)}

<Button on:mousedown={handleClick}>
	<Icon class="material-icons">thumb_up</Icon>
	<Label>Click Me</Label>
</Button>
<p class="mdc-typography--body1">
	{#if clicked}
		You've clicked the button {clicked} time{clicked === 1 ? '' : 's'}. You can
		<a on:click={reset} href="javascript:void(0);">reset it</a>.
	{:else}
		<span class="grayed">You haven't clicked the button.</span>
	{/if}
</p>

<style>
	.grayed {
		opacity: 0.6;
	}
</style>
