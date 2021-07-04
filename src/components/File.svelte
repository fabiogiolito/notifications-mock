<script>

  export let label = "";
  export let image = "";
  export let options = [];
  export let css = "rounded-xl";

  let inputFile;
  let customImage;

  function handleFileChange(e) {
    const [file] = inputFile.files;
    customImage = URL.createObjectURL(file);
    image = customImage;
  }

  function handleSelect(img) {
    if (image == img) {
      image = "";
    } else {
      image = img;
    }
  }

</script>


<div>

  {#if label}
    <p class="tracking-wide opacity-80 mb-1 select-none">{label}</p>
  {/if}

  <div class="flex space-x-1">

    {#if customImage}
      <button
        on:click={() => handleSelect(customImage)}
        class="border-2 border-transparent ring-2 {css}
          {image == customImage ? 'ring-yellow-400' : 'ring-gray-700'}
        "
        style="font-size: 0px;"
      >
        <span class="inline-block w-10 h-10 bg-cover" style="background-image: url('{customImage}');" />
      </button>
    {/if}

    {#each options as option, index}
      <button
        on:click={() => handleSelect(option)}
        class="border-2 border-transparent ring-2 {css}
          {image == option ? 'ring-yellow-400' : 'ring-gray-700'}
        "
        style="font-size: 0px;"
      >
        <span class="inline-block w-10 h-10 bg-cover" style="background-image: url('{option}');" />
      </button>
    {/each}

    <label class="relative cursor-pointer border-2 border-gray-500 {css}">
      <div class="w-10 h-10 grid place-items-center">
        <svg class="w-4 h-4" xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><line x1="12" y1="5" x2="12" y2="19"></line><line x1="5" y1="12" x2="19" y2="12"></line></svg>
      </div>
      <input class="hidden" type="file" bind:this={inputFile} on:change={handleFileChange} />
    </label>

  </div>

</div>
