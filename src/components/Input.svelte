<script>
  export let multiline = false;
  export let label;
  export let value;
  export let css = "p-2 px-3 bg-white text-gray-700 rounded-lg border";
  export let placeholder = "";
</script>

<style>
  textarea, input {
    text-align: inherit;
    letter-spacing: inherit;
    text-transform: inherit;
    background: transparent;
    padding: inherit;
    font: inherit;
    border-radius: inherit;
    white-space: pre-wrap;
    word-break: normal;
    resize: none;
    outline: none;
    overflow: hidden;
  }
</style>

<div>

  {#if label}
    <p class="tracking-wide opacity-80 mb-1 select-none">{label}</p>
  {/if}

  <div class="relative overflow-hidden {css}">
    {#if multiline}
      <textarea bind:value class="absolute inset-0 w-full h-full" {placeholder}></textarea>
    {:else}
      <input type="text" bind:value class="absolute inset-0 w-full h-full" {placeholder} />
    {/if}
    <span class="opacity-0 pointer-events-none whitespace-pre-wrap break-words">{value}_</span>
  </div>

</div>
