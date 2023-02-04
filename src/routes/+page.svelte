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
	const consoleLog = (data) => {
	  log += data + "\n<br>" ;
	};

	let ignoreRead = false;
	let reader, writer;
	try {
	  reader = new NDEFReader();
		writer = new NDEFWriter();
	} catch (err) {
	  consoleLog("ndef error: " + err.message);
	}

	async function readTag() {
			if (!reader) {
				consoleLog('No reader enabled!');
				return;
			}
	    try {
	      await reader.scan();
	      reader.onreading = event => {
					consoleLog("onreading event: " + JSON.stringify(event));
					const decoder = new TextDecoder();
	        for (const record of event.message.records) {
	          consoleLog("Record type:  " + record.recordType);
	          consoleLog("MIME type:    " + record.mediaType);
	          consoleLog("=== data ===\n" + decoder.decode(record.data));
	        }
	      }
	    } catch(error) {
	      consoleLog(error);
	    }
	}

	async function writeTag() {
			if (!writer) {
				consoleLog('No writer enabled!');
				return;
			}
	    try {
	      await writer.write("helloworld!");
	      consoleLog("NDEF message written!");
	    } catch(error) {
	      consoleLog(error);
	    }
	}
</script>
<p>
<Button on:mousedown={readTag}>
	<Icon class="material-icons">thumb_up</Icon>
	<Label>Test NFC Read</Label>
</Button>
<Button on:mousedown={writeTag}>
	<Icon class="material-icons">thumb_down</Icon>
	<Label>Test NFC Write</Label>
</Button>
</p>
<p>{@html log}</p>
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
