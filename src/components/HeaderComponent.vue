<template>
  <div class="bg-zinc-700 flex items-center justify-between h-16">
    <router-link to="/">
      <div class="flex items-center">
        <svg
          xmlns="http://www.w3.org/2000/svg"
          fill="none"
          viewBox="0 0 24 24"
          stroke-width="1.5"
          stroke="currentColor"
          class="w-10 h-10 ml-2 text-white"
        >
          <path
            stroke-linecap="round"
            stroke-linejoin="round"
            d="M3.75 3v11.25A2.25 2.25 0 006 16.5h2.25M3.75 3h-1.5m1.5 0h16.5m0 0h1.5m-1.5 0v11.25A2.25 2.25 0 0118 16.5h-2.25m-7.5 0h7.5m-7.5 0l-1 3m8.5-3l1 3m0 0l.5 1.5m-.5-1.5h-9.5m0 0l-.5 1.5m.75-9l3-3 2.148 2.148A12.061 12.061 0 0116.5 7.605"
          />
        </svg>

        <div class="ml-2 font-bold">
          <div class="text-gray-100 text-xl">MyStocks</div>
        </div>
      </div>
    </router-link>

    <SearchBar class="z-50 relative"/>
    <div></div>
  </div>
</template>

<script>
import SearchBar from './SearchBar.vue';
export default {
  name: 'HeaderComponent',
  components: { SearchBar },
};
</script>

<style scoped></style>
