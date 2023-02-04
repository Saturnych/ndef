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

	let log = '';

	let ignoreRead = false;
	let ndef;
	try {
	  ndef = new NDEFReader();
	} catch (err) {
	  log += "ndef error: " + err.message;
	}

	if (ndef) {

		function write(data) {
			ignoreRead = true;
			return new Promise((resolve, reject) => {
				ndef.addEventListener("reading", event => {
					// Проверяем, хотим ли мы писать в этот тег, или отклоняем.
					ndef.write(data).then(resolve, reject).finally(() => ignoreRead = false);
				}, { once: true });
			});
		}

		ndef.onreading = (event) => {
		  if (ignoreRead) {
		    return; // запись отложена, чтение игнорируется.
		  }
		  log += "We read a tag";
		};

		ndef.scan().then(()=>{
			try {
			  write("Hello World").then(()=>{
					log += "We wrote to a tag!";
				});
			} catch(err) {
			  log += "Error: " + err.message;
			}
		});

  }
</script>

{JSON.stringify(log)}
<br />
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
