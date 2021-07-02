<script>
  import Input from "/src/components/Input.svelte";

  export let notification;

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

  <p class="mb-3 uppercase text-xs font-semibold opacity-50">Notification</p>

  <div class="space-y-4">
    <Input label="Title" bind:value={title} multiline />
    <Input label="Description" bind:value={description} multiline />
    <Input label="Time ago" bind:value={timeAgo} />
  </div>

</div>
