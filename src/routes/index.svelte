<script>
  import { slide } from "svelte/transition";
  import { flip } from 'svelte/animate';
  import { saveAs } from 'file-saver';
  import domtoimage from 'dom-to-image';

  import Nav from "/src/components/Nav.svelte";
  import NotificationForm from "/src/components/NotificationForm.svelte";

  let device = {
    time: "9:41",
    date: "Monday, June 6",
  };

  let defaultNotifications = [{
    appName: "Mail",
    icon: "app_mail.png",
    title: "Medium",
    description: "Stats for your stories",
    timeAgo: "30m ago",
  },{
    appName: "Messages",
    icon: "app_messages.png",
    title: "Graham Clarke",
    description: "Call me when you have the chance.",
    timeAgo: "30m ago",
  },{
    appName: "TokTok",
    icon: "app_tiktok.png",
    title: "TikTok",
    description: "Your video went viral!",
    timeAgo: "30m ago",
  },{
    appName: "Twitter",
    icon: "app_twitter.png",
    title: "Twitter",
    description: "You have a new follower.",
    timeAgo: "30m ago",
  },];

  let notifications = [
    {
      id: Math.random().toString(36).substr(2, 9),
      appName: "Twitter",
      icon: "app_twitter.png",
      contact: "contact_1.png",
      title: "Fabio Giolito (@fabiogiolito)",
      description: "Hey, I made a thing to create notification mockups.",
    },
  ];

  let widgetType = 'small';

  const date = new Date();
  let useCustomTime = false;
  let useCustomDate = true;

  let screen;
  let focusedNotification;
  $: if (notifications.length == 1) focusedNotification = notifications[0].id;

  Array.prototype.move = function (from, to) {
    this.splice(to, 0, this.splice(from, 1)[0]);
  };

  function countMoreNotificationsText(notifications) {
    let text = `${notifications.length - 2} more from `;
    let appNames = [...new Set(notifications.slice(2).map(n => n.appName))];
    if (appNames.length > 3) {
      text += `${appNames.slice(0,2).join(', ')} and more`;
    } else if (appNames.length > 1) {
      let lastAppName = appNames.pop();
      text += `${appNames.slice(0,2).join(', ')} and ${lastAppName}`;
    } else {
      text += appNames[0];
    }
    return text;
  }

  function handleAddNotification() {
    let newNotification = {...defaultNotifications[Math.floor(Math.random() * defaultNotifications.length)], id: Math.random().toString(36).substr(2, 9)};
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

  function printTime() {
    return `${date.getHours()}:${date.getMinutes()}`
  }

  function printDate() {
    return `${date.toLocaleString('en-US', {
      weekday: 'long',
      month: 'long',
      day: 'numeric'
    })}`
}

</script>

<!-- <div class="fixed top-0 right-0 m-6">
  <button on:click={handleSaveImage} class="bg-yellow-500 p-3 px-6 rounded-lg block w-full font-medium">Save as image</button>
</div> -->

<!-- Container -->
<div class="flex-1 overflow-hidden grid grid-cols-2">

  <!-- Device border -->
  <div class="grid place-items-center select-none">

    <!-- Screen -->
    <div bind:this={screen} class="relative w-[375px] h-[812px] pt-20 pb-16 px-[10px] flex flex-col overflow-hidden">
      <!-- Wallpaper -->
      <img src="wallpaper-16.png" srcset="wallpaper-16.png 1x, wallpaper-16@2x.png 2x" class="absolute inset-0 z-10 w-full h-full object-cover" alt="wallpaper" />

      <!-- =================================== -->
      <!-- Top section -->
      <div class="relative z-20 text-white text-center flex flex-col items-center justify-center space-y-2 mb-6">

        <!-- Date Time -->
        <p on:click={e => useCustomDate = !useCustomDate} class="cursor-pointer leading-none text-[18px] w-full font-medium">
          {useCustomDate ? printDate() : device.date}
        </p>
        <p on:click={e => useCustomTime = !useCustomTime} class="cursor-pointer leading-none text-[83px] w-full font-medium">
          {useCustomTime ? printTime() : device.time}
        </p>

        {#if widgetType == 'small'}
          <img src="widgets_small.svg" alt="widgets" />
        {:else if widgetType == 'mixed'}
          <img src="widgets_mixed.svg" alt="widgets" />
        {/if}

      </div>

      <!-- Spacer -->
      <span class="flex-1" />

      <!-- =================================== -->
      <!-- Notifications list -->
      <div class="relative z-20 flex flex-col pb-16">

        <!-- Notification group -->
        {#each notifications.slice(0,3) as notification, index (notification.id)}
          <button
            class="text-left w-full relative transform origin-bottom transition-transform
              {index == 1 ? 'scale-90 -mt-7' : ''}
              {index == 2 ? 'scale-75 -mt-8' : ''}
            "
            style="z-index: {100 - index};"
            on:click={() => handleFocus(index)}
            in:slide={{ duration: 200 }}
            animate:flip={{ duration: 200 }}
          >
            <!-- Notification -->
            <div class="
              bg-[#f5f5f5] bg-opacity-60 backdrop-blur-lg rounded-2xl flex p-[10px]
              {index == 0 ? 'items-center' : 'items-end'}
            ">

              <!-- Image -->
              {#if index < 2}
                <div class="w-10 h-10 relative mr-[10px] flex-shrink-0">
                  {#if notification.contact}
                    <img src={notification.contact} class="rounded-full w-10 h-10 object-cover" alt="contact">
                    <img src={notification.icon || "app_icon.png"} class="rounded absolute bottom-[-2px] right-[-4px] w-4 h-4 object-cover" alt="app">
                  {:else}
                    <img src={notification.icon || "app_icon.png"} class="rounded-lg object-cover" alt="app">
                  {/if}
                </div>
              {/if}

              <!-- Text -->
              {#if index == 0}
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

              {:else if index == 1}
                <div class="flex-1 whitespace-nowrap truncate mr-2">
                  <span class="text-black text-opacity-75 flex-1 font-semibold text-[15px] leading-tight">
                    {notification.title || "Notification title"}
                  </span>
                  {#if notification.description}
                    <span class="leading-tight text-[15px] text-black text-opacity-60 truncate">
                      {notification.description}
                    </span>
                  {/if}
                </div>
                <span class="whitespace-nowrap text-black text-opacity-30 text-[13px]">{notification.timeAgo || "now"}</span>
              {:else}
                <div class="flex-1 pt-4 font-medium text-center truncate opacity-80">
                  {countMoreNotificationsText(notifications)}
                </div>
              {/if}

            </div>
          </button>
        {/each}


        <!-- Shortcut buttons -->
        <div class="absolute left-0 -bottom-2 w-full flex justify-between px-10">
          <div class="bg-black bg-opacity-25 backdrop-blur-lg rounded-full">
            <svg width="48" height="49" viewBox="0 0 48 49" fill="none" xmlns="http://www.w3.org/2000/svg">
              <path d="M22.6244 34.8574H24.9681C25.5541 34.8574 25.9935 34.7077 26.2865 34.4082C26.586 34.1087 26.7357 33.653 26.7357 33.041V22.6699C26.7357 22.1556 26.7911 21.7096 26.9017 21.332C27.0124 20.9544 27.1622 20.6224 27.351 20.3359L27.9857 19.3984C28.2071 19.0534 28.3861 18.7083 28.5228 18.3633C28.6661 18.0182 28.7377 17.6374 28.7377 17.2207V16.4199H18.8549V17.2207C18.8549 17.6374 18.9232 18.0182 19.0599 18.3633C19.1967 18.7083 19.3789 19.0534 19.6068 19.3984L20.2318 20.3359C20.4206 20.6224 20.5704 20.9544 20.681 21.332C20.7917 21.7096 20.847 22.1556 20.847 22.6699V33.041C20.847 33.653 20.9968 34.1087 21.2963 34.4082C21.5957 34.7077 22.0385 34.8574 22.6244 34.8574ZM23.7963 27.3477C23.3991 27.3477 23.0801 27.2174 22.8392 26.957C22.5983 26.6901 22.4779 26.3516 22.4779 25.9414V23.2656C22.4779 22.8555 22.5983 22.5234 22.8392 22.2695C23.0801 22.0156 23.3991 21.8919 23.7963 21.8984C24.1934 21.9049 24.5124 22.0352 24.7533 22.2891C24.9942 22.5365 25.1146 22.862 25.1146 23.2656V25.9414C25.1146 26.3516 24.9942 26.6901 24.7533 26.957C24.5124 27.2174 24.1934 27.3477 23.7963 27.3477ZM23.7963 26.6543C24.0111 26.6543 24.1967 26.5762 24.3529 26.4199C24.5157 26.2637 24.597 26.0749 24.597 25.8535C24.597 25.6387 24.5157 25.4499 24.3529 25.2871C24.1967 25.1243 24.0111 25.043 23.7963 25.043C23.5814 25.043 23.3926 25.1243 23.2299 25.2871C23.0671 25.4499 22.9857 25.6387 22.9857 25.8535C22.9857 26.0749 23.0671 26.2637 23.2299 26.4199C23.3926 26.5762 23.5814 26.6543 23.7963 26.6543ZM18.8549 15.2773H28.7377V14.8086C28.7377 14.1901 28.5879 13.7344 28.2885 13.4414C27.989 13.1419 27.5463 12.9922 26.9603 12.9922H20.6322C20.0463 12.9922 19.6036 13.1419 19.3041 13.4414C19.0046 13.7344 18.8549 14.1901 18.8549 14.8086V15.2773Z" fill="white"/>
            </svg>
          </div>
          <div class="bg-black bg-opacity-50 backdrop-blur-lg rounded-full">
            <svg width="49" height="49" viewBox="0 0 49 49" fill="none" xmlns="http://www.w3.org/2000/svg">
              <path d="M17.4292 31H31.5708C32.3754 31 32.9814 30.7955 33.3888 30.3865C33.7963 29.9827 34 29.3744 34 28.5616V20.3262C34 19.5186 33.7963 18.913 33.3888 18.5092C32.9814 18.1002 32.3754 17.8956 31.5708 17.8956H29.3583C29.152 17.8956 28.987 17.8799 28.8632 17.8485C28.7394 17.817 28.6285 17.7646 28.5305 17.6911C28.4377 17.6125 28.332 17.5076 28.2134 17.3765L27.5945 16.6686C27.4036 16.4536 27.1922 16.2884 26.9601 16.173C26.728 16.0577 26.4134 16 26.0163 16H22.9296C22.5324 16 22.2178 16.0577 21.9857 16.173C21.7588 16.2884 21.5474 16.4536 21.3514 16.6686L20.7325 17.3765C20.5623 17.5705 20.405 17.7069 20.2606 17.7855C20.1162 17.8589 19.8918 17.8956 19.5875 17.8956H17.4292C16.6194 17.8956 16.0109 18.1002 15.6034 18.5092C15.2011 18.913 15 19.5186 15 20.3262V28.5616C15 29.3744 15.2011 29.9827 15.6034 30.3865C16.0109 30.7955 16.6194 31 17.4292 31ZM24.5 28.6796C23.9121 28.6796 23.3628 28.5695 22.8522 28.3492C22.3416 28.129 21.8929 27.8222 21.5061 27.4289C21.1245 27.0357 20.8227 26.5794 20.601 26.0603C20.3844 25.5412 20.2761 24.9801 20.2761 24.377C20.2761 23.7792 20.3844 23.2208 20.601 22.7016C20.8227 22.1825 21.1245 21.7263 21.5061 21.333C21.8929 20.9397 22.3416 20.6329 22.8522 20.4127C23.3628 20.1924 23.9121 20.0823 24.5 20.0823C25.2788 20.0823 25.9879 20.2737 26.6274 20.6565C27.267 21.0393 27.775 21.5558 28.1515 22.2061C28.528 22.8563 28.7162 23.58 28.7162 24.377C28.7162 24.9801 28.6079 25.5412 28.3913 26.0603C28.1747 26.5794 27.873 27.0357 27.4862 27.4289C27.0993 27.8222 26.6507 28.129 26.1401 28.3492C25.6295 28.5695 25.0828 28.6796 24.5 28.6796ZM24.5 27.484C25.0622 27.484 25.5727 27.345 26.0318 27.0671C26.4959 26.7892 26.8647 26.4169 27.138 25.9502C27.4114 25.4782 27.548 24.9539 27.548 24.377C27.548 23.8055 27.4114 23.2863 27.138 22.8196C26.8647 22.3477 26.4959 21.9727 26.0318 21.6948C25.5727 21.4116 25.0622 21.2701 24.5 21.2701C23.9378 21.2701 23.4247 21.4116 22.9605 21.6948C22.5015 21.9727 22.1327 22.3477 21.8542 22.8196C21.5809 23.2863 21.4442 23.8055 21.4442 24.377C21.4442 24.9539 21.5809 25.4782 21.8542 25.9502C22.1327 26.4169 22.5015 26.7892 22.9605 27.0671C23.4247 27.345 23.9378 27.484 24.5 27.484ZM29.2345 21.4824C29.2345 21.215 29.3325 20.9816 29.5285 20.7824C29.7296 20.5779 29.9669 20.4756 30.2402 20.4756C30.5033 20.4756 30.7328 20.5779 30.9287 20.7824C31.1299 20.9816 31.2305 21.215 31.2305 21.4824C31.2305 21.7656 31.1299 22.0042 30.9287 22.1982C30.7328 22.3922 30.5033 22.4893 30.2402 22.4893C29.9669 22.4945 29.7296 22.4001 29.5285 22.2061C29.3325 22.0068 29.2345 21.7656 29.2345 21.4824Z" fill="white"/>
            </svg>
          </div>
        </div>
      </div>

      <!-- =================================== -->
      <!-- Status bar -->
      <div class="absolute top-0 inset-x-0 z-20 text-white">
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
      <div class="absolute bottom-0 inset-x-0 z-20 p-2 text-white">
        <div class="w-[134px] h-[5px] bg-current rounded-full mx-auto" />
      </div>

    </div>
  </div>

  <!-- =================================== -->
  <!-- FORMS -->
  <div class="bg-gray-800 flex flex-col place-items-center pt-[20vh] pb-[5vh] scrollbar scrollbar-thin scrollbar-track-gray-700 scrollbar-thumb-gray-600">

    <div class="w-80 max-h-[80vh]">

      <div class="flex items-center space-x-2 w-full bg-gray-700 bg-opacity-50 hover:bg-opacity-90 p-4 uppercase text-white text-left text-xs font-semibold opacity-50 select-none">
        <span class="flex-1">Widgets</span>
        <select bind:value={widgetType} class="bg-white border-0 px-2 py-1 text-gray-900 rounded">
          <option value="none">None</option>
          <option value="small">Small</option>
          <option value="mixed">Mixed</option>
        </select>
      </div>

      {#each notifications as notification, index (notification.id)}
        <div transition:slide={{ duration: 200 }} animate:flip={{ duration: 200 }}>
          <NotificationForm
            bind:notification
            index={index}
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

      <button class="flex space-x-2 w-full bg-gray-700 bg-opacity-50 hover:bg-opacity-90 p-4 uppercase text-white text-left text-xs font-semibold opacity-50 select-none" on:click={handleAddNotification}>
        <svg class="w-4 h-4" xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><line x1="12" y1="5" x2="12" y2="19"></line><line x1="5" y1="12" x2="19" y2="12"></line></svg>
        <span>New notification</span>
      </button>

    </div>

  </div>

</div>
