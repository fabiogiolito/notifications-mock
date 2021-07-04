<script>
  import { slide } from "svelte/transition";
  import { flip } from 'svelte/animate';

  /*
    TODO
    - More app image options
    - Placeholder app image when nothing selected
    - More contact image options
    - Device form (locked, datetime, wallpaper)
    - Download resulting image
    - Compare to screenshot and see what needs to be adjusted
    - Add notification.media option
  */


  import NotificationForm from "/src/components/NotificationForm.svelte";

  let device = {
    isLocked: false,
    time: "9:41",
    date: "Monday, June 7",
  };

  let defaultNotification = {
    icon: "app_mail.png",
    contact: undefined,
    title: "Medium \nStats for your stories",
    description: "Here's a notification description",
    timeAgo: "30m ago",
    isStacked: false,
  };

  let notifications = [
    {
      id: Math.random().toString(36).substr(2, 9),
      icon: "app_messages.png",
      contact: "contact_image.jpg",
      title: "Fabio Giolito",
      description: "Can you bring a big salad? I'm on dessert duty.",
      isStacked: true,
    },
  ];

  let focusedNotification;
  $: if (notifications.length == 1) focusedNotification = notifications[0].id;

  Array.prototype.move = function (from, to) {
    this.splice(to, 0, this.splice(from, 1)[0]);
  };

  function toggleLock() {
    device.isLocked = !device.isLocked;
  }

  function handleAddNotification() {
    let newNotification = {...defaultNotification, id: Math.random().toString(36).substr(2, 9)};
    notifications = [...notifications, newNotification];
    focusedNotification = newNotification.id;
  }

  function handleMoveUp(index) {
    if (index) { // index not first
      notifications.move(index, index - 1);
      notifications = notifications;
    }
  }

  function handleMoveDown(index) {
    if (index < notifications.length - 1) { // index not last
      notifications.move(index, index + 1);
      notifications = notifications;
    }
  }

  function handleDelete(index) {
    notifications.splice(index, 1);
    notifications = notifications;
  }

  function handleFocus(index) {
    if (focusedNotification == notifications[index].id) {
      focusedNotification = "";
    } else {
      focusedNotification = notifications[index].id;
    }
  }

</script>

<!-- Container -->
<div class="w-screen h-screen overflow-hidden grid grid-cols-2">

  <!-- Device border -->
  <div class="grid place-items-center">

    <!-- Screen -->
    <div class="relative w-[375px] h-[812px] bg-cover bg-center pt-14 px-[10px] flex flex-col overflow-hidden" style="background-image: url('https://9to5mac.com/wp-content/uploads/sites/6/2021/06/1881.WWDC_2021_Light-428w-926h@3xiphone.jpg?quality=82&strip=all')">

      <!-- =================================== -->
      <!-- Top section -->
      <div class="text-white text-center flex flex-col items-center justify-center space-y-2 mb-6">

        <!-- Lock -->
        <button on:click={toggleLock}>
          {#if device.isLocked}
            <svg width="64" height="40" viewBox="0 0 64 40" fill="currentColor" xmlns="http://www.w3.org/2000/svg">
              <path d="M20 21.5C20 19.8431 21.3431 18.5 23 18.5H41C42.6569 18.5 44 19.8431 44 21.5V36.5C44 38.1569 42.6569 39.5 41 39.5H23C21.3431 39.5 20 38.1569 20 36.5V21.5Z" />
              <path fill-rule="evenodd" clip-rule="evenodd" d="M32 5.5C28.8265 5.5 26 8.09216 26 11V23C26 23.8284 25.3284 24.5 24.5 24.5C23.6716 24.5 23 23.8284 23 23V11C23 6.17586 27.4415 2.5 32 2.5C36.5585 2.5 41 6.17586 41 11V23C41 23.8284 40.3284 24.5 39.5 24.5C38.6716 24.5 38 23.8284 38 23V11C38 8.09216 35.1735 5.5 32 5.5Z" />
            </svg>
          {:else}
            <svg width="64" height="40" viewBox="0 0 64 40" fill="currentColor" xmlns="http://www.w3.org/2000/svg">
              <path d="M20 21.5C20 19.8431 21.3431 18.5 23 18.5H41C42.6569 18.5 44 19.8431 44 21.5V36.5C44 38.1569 42.6569 39.5 41 39.5H23C21.3431 39.5 20 38.1569 20 36.5V21.5Z" />
              <path fill-rule="evenodd" clip-rule="evenodd" d="M47 3.5C43.8265 3.5 41 6.09216 41 9V21C41 21.8284 40.3284 22.5 39.5 22.5C38.6716 22.5 38 21.8284 38 21V9C38 4.17586 42.4415 0.5 47 0.5C51.5585 0.5 56 4.17586 56 9V16C56 16.8284 55.3284 17.5 54.5 17.5C53.6716 17.5 53 16.8284 53 16V9C53 6.09216 50.1735 3.5 47 3.5Z" />
            </svg>
          {/if}
        </button>

        <!-- Date Time -->
        <p class="leading-none text-[83px] font-extralight">{device.time}</p>
        <p class="leading-none text-[22px] font-light">{device.date}</p>

      </div>

      <!-- =================================== -->
      <!-- Notifications list -->
      <div class="flex-1 space-y-2">

        <!-- Notification group -->
        {#each notifications as notification, index (notification.id)}
          <button class="text-left w-full" on:click={() => handleFocus(index)} in:slide={{ duration: 200 }} animate:flip={{ duration: 200 }}>
            <!-- Notification -->
            <div class="bg-[#f5f5f5] bg-opacity-60 backdrop-blur-lg rounded-2xl flex items-center p-[10px]">

              <div class="w-8 h-8 relative mr-[10px]">
                {#if notification.contact}
                  <img src={notification.contact} class="rounded-full w-8 h-8 object-cover" alt="contact">
                  <img src={notification.icon || "app_mail.png"} class="rounded absolute bottom-[-2px] right-[-4px] w-[14px] h-[14px] object-cover" alt="app">
                {:else}
                  <img src={notification.icon || "app_mail.png"} class="rounded-lg object-cover" alt="app">
                {/if}
              </div>

              <div class="flex-1">
                <div class="flex items-start mb-[2px]">
                  <p class="text-black text-opacity-75 flex-1 font-semibold text-[15px] whitespace-pre-wrap leading-tight">
                    {notification.title || "Notification title"}
                  </p>
                  <p class="text-black text-opacity-30 text-[13px]">{notification.timeAgo || "now"}</p>
                </div>
                {#if notification.description}
                  <p class="leading-tight text-[15px] text-black text-opacity-60 whitespace-pre-wrap">
                    {notification.description}
                  </p>
                {/if}
              </div>

            </div>
            {#if notification.isStacked}
              <div transition:slide={{ duration: 200 }} class="h-2 mx-4 rounded-b-2xl bg-[#f5f5f5] bg-opacity-40 backdrop-blur-lg" />
              <div transition:slide={{ duration: 200 }} class="h-2 mx-8 rounded-b-2xl bg-[#f5f5f5] bg-opacity-20 backdrop-blur-lg" />
            {/if}
          </button>
        {/each}

      </div>

      <!-- =================================== -->
      <!-- Status bar -->
      <div class="absolute top-0 inset-x-0 text-white">
        <svg width="375" height="44" viewBox="0 0 375 44" fill="none" xmlns="http://www.w3.org/2000/svg">
          <path opacity="0.35" d="M338.667 17.8334H355.333C356.53 17.8334 357.5 18.8034 357.5 20V26C357.5 27.1967 356.53 28.1667 355.333 28.1667H338.667C337.47 28.1667 336.5 27.1967 336.5 26V20C336.5 18.8034 337.47 17.8334 338.667 17.8334Z" stroke="currentColor"/>
          <path opacity="0.4" d="M359 21V25C359.805 24.6613 360.328 23.8732 360.328 23C360.328 22.1269 359.805 21.3388 359 21" fill="currentColor"/>
          <path d="M338 20.4334C338 19.8259 338.492 19.3334 339.1 19.3334H354.9C355.508 19.3334 356 19.8259 356 20.4334V25.5667C356 26.1742 355.508 26.6667 354.9 26.6667H339.1C338.492 26.6667 338 26.1742 338 25.5667V20.4334Z" fill="currentColor"/>
          <path fill-rule="evenodd" clip-rule="evenodd" d="M323.33 19.6081C325.546 19.6082 327.677 20.4597 329.283 21.9865C329.404 22.1043 329.597 22.1029 329.716 21.9831L330.872 20.8165C330.933 20.7557 330.966 20.6735 330.966 20.5879C330.965 20.5023 330.931 20.4205 330.87 20.3605C326.655 16.3209 320.005 16.3209 315.79 20.3605C315.729 20.4204 315.694 20.5023 315.693 20.5878C315.693 20.6734 315.726 20.7557 315.787 20.8165L316.943 21.9831C317.062 22.103 317.255 22.1045 317.376 21.9865C318.982 20.4596 321.114 19.6081 323.33 19.6081ZM323.33 23.4038C324.547 23.4037 325.721 23.8563 326.624 24.6735C326.746 24.7895 326.938 24.7869 327.057 24.6678L328.212 23.5011C328.273 23.4399 328.307 23.3569 328.306 23.2707C328.305 23.1844 328.269 23.1021 328.207 23.0421C325.459 20.4858 321.203 20.4858 318.455 23.0421C318.393 23.1021 318.357 23.1844 318.357 23.2707C318.356 23.357 318.39 23.44 318.451 23.5011L319.605 24.6678C319.724 24.7869 319.916 24.7895 320.038 24.6735C320.94 23.8568 322.113 23.4043 323.33 23.4038ZM325.643 25.9576C325.645 26.0441 325.611 26.1275 325.549 26.1881L323.552 28.2038C323.493 28.263 323.413 28.2964 323.33 28.2964C323.247 28.2964 323.167 28.263 323.108 28.2038L321.111 26.1881C321.049 26.1275 321.015 26.0441 321.017 25.9576C321.019 25.871 321.056 25.7891 321.12 25.7311C322.396 24.6523 324.264 24.6523 325.54 25.7311C325.604 25.7892 325.641 25.8711 325.643 25.9576Z" fill="currentColor"/>
          <path fill-rule="evenodd" clip-rule="evenodd" d="M309.666 17.6667H308.666C308.114 17.6667 307.666 18.1145 307.666 18.6667V27.3334C307.666 27.8857 308.114 28.3334 308.666 28.3334H309.666C310.218 28.3334 310.666 27.8857 310.666 27.3334V18.6667C310.666 18.1145 310.218 17.6667 309.666 17.6667ZM303.999 20.0001H304.999C305.552 20.0001 305.999 20.4478 305.999 21.0001V27.3334C305.999 27.8857 305.552 28.3334 304.999 28.3334H303.999C303.447 28.3334 302.999 27.8857 302.999 27.3334V21.0001C302.999 20.4478 303.447 20.0001 303.999 20.0001ZM300.333 22.3334H299.333C298.78 22.3334 298.333 22.7811 298.333 23.3334V27.3334C298.333 27.8857 298.78 28.3334 299.333 28.3334H300.333C300.885 28.3334 301.333 27.8857 301.333 27.3334V23.3334C301.333 22.7811 300.885 22.3334 300.333 22.3334ZM295.666 24.3334H294.666C294.114 24.3334 293.666 24.7811 293.666 25.3334V27.3334C293.666 27.8857 294.114 28.3334 294.666 28.3334H295.666C296.218 28.3334 296.666 27.8857 296.666 27.3334V25.3334C296.666 24.7811 296.218 24.3334 295.666 24.3334Z" fill="currentColor"/>
          <line opacity="0.25" x1="303" y1="37" x2="346" y2="37" stroke="currentColor" stroke-width="3" stroke-linecap="round"/>
        </svg>
      </div>

      <!-- =================================== -->
      <!-- Home indicator -->
      <div class="absolute bottom-0 inset-x-0 p-2 text-white">
        <div class="w-[134px] h-[5px] bg-current rounded-full mx-auto" />
      </div>

    </div>
  </div>

  <!-- =================================== -->
  <!-- FORMS -->
  <div class="bg-gray-800 flex flex-col place-items-center py-[20vh] scrollbar scrollbar-thin scrollbar-track-gray-700 scrollbar-thumb-gray-600">

    <div class="w-100">

      {#each notifications as notification, index (notification.id)}
        <div transition:slide={{ duration: 200 }} animate:flip={{ duration: 200 }}>
          <NotificationForm
            bind:notification
            isFirst={index == 0}
            isLast={index == notifications.length - 1}
            isFocused={focusedNotification == notification.id}
            on:move-up={() => handleMoveUp(index)}
            on:move-down={() => handleMoveDown(index)}
            on:delete={() => handleDelete(index)}
            on:focus={() => handleFocus(index)}
          />
        </div>
      {/each}

      <button class="block w-full bg-gray-700 bg-opacity-50 hover:bg-opacity-90 p-4 uppercase text-white text-left text-xs font-semibold opacity-50 select-none" on:click={handleAddNotification}>
        New notification
      </button>

    </div>

  </div>

</div>
