<script>
  import { createEventDispatcher } from "svelte";
  import { slide } from "svelte/transition";

  const dispatch = createEventDispatcher();

  import Input from "/src/components/Input.svelte";
  import File from "/src/components/File.svelte";

  export let index = 0;
  export let notification;
  export let isFirst;
  export let isLast;
  export let isFocused;
  export let stackable;

  let icon = notification.icon;
  let contact = notification.contact;
  let title = notification.title;
  let description = notification.description;
  let timeAgo = notification.timeAgo;
  let appName = notification.appName;
  let isStacked = notification.isStacked;

  $: if (icon || icon === "") notification.icon = icon;
  $: if (contact || contact === "") notification.contact = contact;
  $: if (title || title === "") notification.title = title;
  $: if (description || description === "") notification.description = description;
  $: if (timeAgo || timeAgo === "") notification.timeAgo = timeAgo;
  $: if (isStacked || isStacked === "") notification.isStacked = isStacked;
  $: if (appName || appName === "") notification.appName = appName;

  let appIcons = [
    "app_twitter.png",
    "app_messages.png",
    "app_tiktok.png",
    "app_mail.png",
  ];

  let contactImages = [
    "contact_1.png",
    "contact_2.png",
    "contact_3.png",
    "contact_4.png",
  ];

</script>

<div class="bg-white bg-opacity-10 text-white w-full space-y-4 border-b border-gray-800">

  <div class="flex items-center">
    <button on:click={() => dispatch('focus')} class="p-4 flex-1 uppercase text-left text-xs font-semibold opacity-50 select-none">
      Notification
    </button>

    <!-- Up -->
    <button disabled={isFirst} class="p-4 px-2 -mt-2 text-gray-400 hover:text-gray-200 disabled:opacity-25 disabled:pointer-events-none" on:click={() => dispatch('move-up')}>
      <svg class="inline-block w-5 h-5" xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><line x1="12" y1="19" x2="12" y2="5"></line><polyline points="5 12 12 5 19 12"></polyline></svg>
    </button>

    <!-- Down -->
    <button disabled={isLast} class="p-4 px-2 -mt-2 text-gray-400 hover:text-gray-200 disabled:opacity-25 disabled:pointer-events-none" on:click={() => dispatch('move-down')}>
      <svg class="inline-block w-5 h-5" xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><line x1="12" y1="5" x2="12" y2="19"></line><polyline points="19 12 12 19 5 12"></polyline></svg>
    </button>

    <!-- Delete -->
    <button disabled={isFirst && isLast} class="p-4 pl-2 -mt-2 text-gray-400 hover:text-gray-200 disabled:opacity-25 disabled:pointer-events-none" on:click={() => dispatch('delete')}>
      <svg class="inline-block w-5 h-5" xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><line x1="18" y1="6" x2="6" y2="18"></line><line x1="6" y1="6" x2="18" y2="18"></line></svg>
    </button>

  </div>

  {#if isFocused}
    {#if index < 2}
    <div transition:slide class="p-4 pt-0 space-y-4">
      <Input label="App name" bind:value={appName} />
      <Input label="Title" bind:value={title} multiline />
      <Input label="Description" bind:value={description} multiline />
      <Input label="Time ago" placeholder="now" bind:value={timeAgo} />
      <File label="App image" bind:image={icon} options={appIcons} uploadedImage={!appIcons.includes(icon) && icon} />
      <File label="Contact image" bind:image={contact} options={contactImages} css="rounded-full" uploadedImage={!contactImages.includes(contact) && contact} />

      {#if stackable}
        <label class="flex items-center space-x-2 select-none">
          <input class="accent-yellow-500" type="checkbox" bind:checked={isStacked} />
          <span>Stacked notification</span>
        </label>
      {/if}

    </div>
    {:else}
      <div transition:slide class="p-4 pt-0">
        <Input label="App name" bind:value={appName} />
      </div>
    {/if}
  {/if}

</div>
