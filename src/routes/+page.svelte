<script lang="ts">
	import { setContext, onMount } from 'svelte';
	import Dialog, { Title, Content, Actions } from '@smui/dialog';
	import Button, { Label, Icon } from '@smui/button';
	import { Html5QrcodeScanner } from 'html5-qrcode';

	function onScanSuccess(decodedText, decodedResult) {
    alert(`Code scanned: ${decodedText}`); // decodedResult
	}

	onMount(() => {
		var html5QrcodeScanner = new Html5QrcodeScanner("qr-reader", { fps: 10, qrbox: 250 });
		console.log('window.location:', html5QrcodeScanner);
		html5QrcodeScanner.render(onScanSuccess);
	});

	let open = false;
	let clicked = 'Nothing yet.';

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
	if (!writer) writer = reader;

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
<div id="qr-reader" style="width: 600px"></div>
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

<Dialog
  bind:open
  aria-labelledby="simple-title"
  aria-describedby="simple-content"
>
  <!-- Title cannot contain leading whitespace due to mdc-typography-baseline-top() -->
  <Title id="simple-title">Dialog Title</Title>
  <Content id="simple-content">Super awesome dialog body text?</Content>
  <Actions>
    <Button on:click={() => (clicked = 'No')}>
      <Label>No</Label>
    </Button>
    <Button on:click={() => (clicked = 'Yes')}>
      <Label>Yes</Label>
    </Button>
  </Actions>
</Dialog>

<Button on:click={() => (open = true)}>
  <Label>Open Dialog</Label>
</Button>

<pre class="status">Clicked: {clicked}</pre>

<style>
	.grayed {
		opacity: 0.6;
	}
</style>
