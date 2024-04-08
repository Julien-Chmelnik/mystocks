<template>
  <div ref="searchBar" class="z-50">
    <form class="w-[500px]" @submit.prevent="searchStocks">
      <label
        for="stock-search"
        class="mb-2 text-sm font-medium sr-only text-white"
        >Search</label
      >
      <div @click="showList = true" class="relative">
        <div
          class="absolute inset-y-0 left-0 flex items-center pl-3 pointer-events-none"
        >
          <svg
            aria-hidden="true"
            class="w-5 h-5 text-gray-500"
            fill="none"
            stroke="currentColor"
            viewBox="0 0 24 24"
            xmlns="http://www.w3.org/2000/svg"
          >
            <path
              stroke-linecap="round"
              stroke-linejoin="round"
              stroke-width="2"
              d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z"
            ></path>
          </svg>
        </div>
        <input
          type="search"
          id="stock-search"
          v-model="searchKeyword"
          class="block w-full p-4 pl-10 text-sm border rounded-lg focus:ring-blue-500 focus:border-blue-500 bg-gray-700 border-gray-600 placeholder-gray-400 text-white outline-0"
          placeholder="Aktie suchen..."
          autocomplete="off"
          required
        />
        <!-- <button
          type="submit"
          @click="searchStocks()"
          class="text-white absolute right-2.5 bottom-2.5 bg-blue-600 hover:bg-blue-700 focus:ring-1 focus:outline-none font-medium rounded-lg text-sm px-4 py-2"
        >
          Search
        </button> -->
      </div>
    </form>
    <div class="mt-1 absolute" v-if="showList">
      <div
        @click="
          showList = false;
          selectedStock = company['1. symbol'];
          this.updateStockName(company);
          getStocks();
        "
        v-for="(company, index) in searchResults.bestMatches"
        :key="index"
        :class="`text-white py-3 mt-[1px] bg-gray-700 hover:bg-gray-800 w-[500px] mx-auto border-b hover:border-x border-gray-500 ${
          index === 0
            ? 'rounded-t-lg hover:border-t hover:mt-[0px]'
            : index === searchResults.bestMatches.length - 1
            ? 'rounded-b-lg border-b-0 hover:border-b hover:mb-[-1px]'
            : ''
        }`"
      >
        {{
          company['2. name'] +
          ' [' +
          company['1. symbol'] +
          '], ' +
          company['4. region']
        }}
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';
import { inject } from 'vue';

export default {
  name: 'SearchBar',
  inject: ['stocks'],
  setup() {
    const sharedStocks = inject('stocks');

    const updateStocks = (newStockData) => {
      sharedStocks.stockData = newStockData;
    };
    const updateStockName = (newStockName) => {
      sharedStocks.stockName = newStockName;
    };
    return { updateStocks, updateStockName };
  },
  data() {
    return {
      stocks: [],
      searchResults: [],
      searchKeyword: '',
      selectedStock: '',
      showList: true,
    };
  },
  mounted() {
    // Eventlistener hinzufügen, um zu überprüfen, ob außerhalb der Suchleiste geklickt wird
    document.addEventListener('click', this.handleClickOutside);
  },
  beforeUnmount() {
    // Eventlistener entfernen, um Memory Leaks zu vermeiden
    document.removeEventListener('click', this.handleClickOutside);
  },
  methods: {
    getStocks() {
      axios
        .get(
          `https://www.alphavantage.co/query?function=TIME_SERIES_MONTHLY_ADJUSTED&symbol=${this.selectedStock}&apikey=ZIYCPWBHDW4VQ4OA`
        )
        .then((response) => {
          this.stocks = response.data;
          this.updateStocks(this.stocks);
          // console.log('stocks', this.stocks);
          if (response.data.Note) {
            throw new Error('API Limit reached. Please try again in a minute.');
          }
        })
        .catch((error) => {
          console.log(error);
          alert(error);
        });
    },
    searchStocks() {
      axios
        .get(
          `https://www.alphavantage.co/query?function=SYMBOL_SEARCH&keywords=${this.searchKeyword}&apikey=ZIYCPWBHDW4VQ4OA`
        )
        .then((response) => {
          //console.log('seaarch', response.data);
          this.searchResults = response.data;
          if (response.data.Note) {
            throw new Error('API Limit reached. Please try again in a minute.');
          }
        })
        .catch((error) => {
          console.log(error);
          alert(error);
        });
    },
    handleClickOutside(event) {
      const searchBar = this.$refs.searchBar;

      // Überprüfen, ob das geklickte Element ein Kind der Suchleiste ist
      if (searchBar && !searchBar.contains(event.target)) {
        this.showList = false;
      }
    },
  },
  watch: {
    searchKeyword() {
      this.searchStocks();
      //console.log(this.selectedStock);
    },
  },
};
</script>

<style scoped></style>
