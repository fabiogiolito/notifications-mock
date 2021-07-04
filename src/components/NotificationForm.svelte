<script>
  import { createEventDispatcher } from "svelte";
  const dispatch = createEventDispatcher();

  import Input from "/src/components/Input.svelte";
  import File from "/src/components/File.svelte";

  export let notification;
  export let isFirst;
  export let isLast;

  let appImage = notification.appImage;
  let contactImage = notification.contactImage;
  let title = notification.title;
  let description = notification.description;
  let timeAgo = notification.timeAgo;
  let isStacked = notification.isStacked;

  $: if (appImage || appImage === "") notification.appImage = appImage;
  $: if (contactImage || contactImage === "") notification.contactImage = contactImage;
  $: if (title || title === "") notification.title = title;
  $: if (description || description === "") notification.description = description;
  $: if (timeAgo || timeAgo === "") notification.timeAgo = timeAgo;
  $: if (isStacked || isStacked === false) notification.isStacked = isStacked;

</script>

<div class="bg-white bg-opacity-10 text-white rounded-xl p-4 mb-6 w-80">

  <div class="flex items-center space-x-2 mb-3">
    <p class="flex-1 uppercase text-xs font-semibold opacity-50 select-none">
      Notification
    </p>

    <!-- Up -->
    <button disabled={isFirst} class="-mt-2 text-gray-400 hover:text-gray-200 disabled:opacity-25 disabled:pointer-events-none" on:click={() => dispatch('move-up')}>
      <svg class="inline-block w-5 h-5" xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><line x1="12" y1="19" x2="12" y2="5"></line><polyline points="5 12 12 5 19 12"></polyline></svg>
    </button>

    <!-- Down -->
    <button disabled={isLast} class="-mt-2 text-gray-400 hover:text-gray-200 disabled:opacity-25 disabled:pointer-events-none" on:click={() => dispatch('move-down')}>
      <svg class="inline-block w-5 h-5" xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><line x1="12" y1="5" x2="12" y2="19"></line><polyline points="19 12 12 19 5 12"></polyline></svg>
    </button>

    <!-- Delete -->
    <button disabled={isFirst && isLast} class="-mt-2 text-gray-400 hover:text-gray-200 disabled:opacity-25 disabled:pointer-events-none" on:click={() => dispatch('delete')}>
      <svg class="inline-block w-5 h-5" xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><line x1="18" y1="6" x2="6" y2="18"></line><line x1="6" y1="6" x2="18" y2="18"></line></svg>
    </button>
  </div>

  <div class="space-y-4">
    <Input label="Title" bind:value={title} multiline />
    <Input label="Description" bind:value={description} multiline />
    <Input label="Time ago" bind:value={timeAgo} />
    <File label="App image" bind:image={appImage} />
    <File label="Contact image" bind:image={contactImage} />
    <label class="flex items-center space-x-2 select-none"><input type="checkbox" bind:checked={isStacked} /> <span>Stacked notification</span></label>
  </div>

</div>
