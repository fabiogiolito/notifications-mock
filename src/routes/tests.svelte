<script>
  import domtoimage from 'dom-to-image';
  import { saveAs } from 'file-saver';

  let title = "Welcome to Sveltekit";

  let container;
  let snapshots;

  function handleSnapshot() {
    domtoimage.toPng(container)
    .then(function (dataUrl) {
        var img = new Image();
        img.src = dataUrl;
        snapshots.prepend(img);
    })
    .catch(function (error) {
        console.error('oops, something went wrong!', error);
    });
  }

  function handleSaveImage() {
    domtoimage.toBlob(container)
    .then(function (blob) {
      console.log(blob);
      saveAs(blob, 'my-node.png');
    });
  }

</script>

<input type="text" bind:value={title} />

<div class="container" bind:this={container}>
  <h1>{title}</h1>
  <p>Visit <a href="https://kit.svelte.dev">kit.svelte.dev</a> to read the documentation</p>
</div>

<button on:click={handleSnapshot}>Snapshot</button>
<button on:click={handleSaveImage}>Download image</button>

<div class="snapshots" bind:this={snapshots}></div>

<style>
  .container {
    font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;
    width: 400px;
    height: 400px;
    background: lightcoral;
    color: whitesmoke;
    padding: 2rem;
  }
</style>
