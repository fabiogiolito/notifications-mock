<script>

  export let label = "";
  export let btnLabel = "Choose image"
  export let image = "";

  let inputFile;

  $: console.log("==== input changed", image);

  function handleFileChange(e) {
    const [file] = inputFile.files;
    image = URL.createObjectURL(file);
  }

  function handleDeleteImage(e) {
    image = "";
  }

</script>


<div>

  {#if label}
    <p class="tracking-wide opacity-80 mb-1 select-none">{label}</p>
  {/if}

  <div class="flex space-x-4">
    <div class="relative flex-1">
      <span class="inline-block w-full p-2 bg-red-500 rounded-lg text-center">{btnLabel}</span>
      <input type="file" bind:this={inputFile} on:change={handleFileChange} class="absolute inset-0 opacity-0 cursor-pointer" />
    </div>

    <!-- Delete -->
    {#if image}
      <button on:click={handleDeleteImage}>
        <svg class="inline-block w-5 h-5" xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
          <polyline points="3 6 5 6 21 6"></polyline>
          <path d="M19 6v14a2 2 0 0 1-2 2H7a2 2 0 0 1-2-2V6m3 0V4a2 2 0 0 1 2-2h4a2 2 0 0 1 2 2v2"></path>
          <line x1="10" y1="11" x2="10" y2="17"></line>
          <line x1="14" y1="11" x2="14" y2="17"></line>
        </svg>
      </button>
    {/if}
  </div>

</div>
