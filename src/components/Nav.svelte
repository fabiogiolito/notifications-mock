<script>
  import { page } from "$app/stores";
</script>

<div class="bg-gray-900 text-white p-4 flex items-center justify-center space-x-8">
  <a href="/" class:opacity-50={$page.url.pathname != '/'}>iOS 16</a>
  <a href="/ios-15" class:opacity-50={$page.url.pathname != '/ios-15'}>iOS 15</a>
</div>
