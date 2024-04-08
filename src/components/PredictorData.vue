<template>
  <div ref="searchBar" class="flex justify-center w-96 z-40">
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
          :placeholder="getPlaceholderText()"
          autocomplete="off"
          required
        />
      </div>
    </form>
    <!-- Liste für FIAT, Krypto, Ökonom. KPI's, Rohstoffe -->
    <div class="mt-16 absolute" v-if="showList && sharedPredictor.predictorName !== 'aktie'">
      <div
        @click="
          showList = false;
          selectedPredictorItem = selectedPredictorCategory.code;
          this.updatePredictorItem(selectedPredictorItem);
          this.updatePredictorName(
            selectedPredictorCategory.name +
              ' [' +
              selectedPredictorCategory.code +
              ']'
          );
          fetchPredictor();
        "
        v-for="(selectedPredictorCategory, index) in searchResults"
        :key="index"
        :class="`text-white py-3 mt-[1px] w-96 bg-gray-700 hover:bg-gray-800 mx-auto border-b hover:border-x border-gray-500 ${
          index === 0
            ? 'rounded-t-lg hover:border-t hover:mt-[0px]'
            : index === searchResults.length - 1
            ? 'rounded-b-lg border-b-0 hover:border-b hover:mb-[-1px]'
            : ''
        }`"
      >
        {{
          selectedPredictorCategory.name +
          ' [' +
          selectedPredictorCategory.code +
          ']'
        }}
      </div>
    </div>
    <!-- Liste für Aktien Kriterium -->
    <div class="mt-16 absolute" v-if="showList && sharedPredictor.predictorName === 'aktie'">
      <div
        @click="
          showList = false;
          selectedPredictorItem = selectedPredictorCategory['1. symbol'];
          this.updatePredictorItem(selectedPredictorItem);
          this.updatePredictorName(
            selectedPredictorCategory['2. name'] +
              ' [' +
              selectedPredictorCategory['1. symbol'] +
              ']'
          );
          fetchPredictor();
        "
        v-for="(selectedPredictorCategory, index) in searchResults"
        :key="index"
        :class="`text-white py-3 mt-[1px] bg-gray-700 hover:bg-gray-800 mx-auto border-b hover:border-x border-gray-500 ${
          index === 0
            ? 'rounded-t-lg hover:border-t hover:mt-[0px]'
            : index === searchResults.length - 1
            ? 'rounded-b-lg border-b-0 hover:border-b hover:mb-[-1px]'
            : ''
        }`"
      >
        {{
          selectedPredictorCategory['2. name'] +
          ' [' +
          selectedPredictorCategory['1. symbol'] +
          '], ' +
          selectedPredictorCategory['4. region']
        }}
      </div>
    </div>
  </div>
</template>

<script>
import { inject } from 'vue';
import axios from 'axios';
import fiatCsv from '@/assets/fiat.csv';
import cryptoCsv from '@/assets/crypto.csv';
import kpiCsv from '@/assets/kpi.csv';
import commoditiesCsv from '@/assets/commodities.csv';

export default {
  name: 'PredictorData',
  setup() {
    const sharedPredictor = inject('predict');

    const updatePredictorItem = (selectedPredictorItem) => {
      sharedPredictor.selectedPredictor = selectedPredictorItem;
    };

    const updatePredictorData = (predictorData) => {
      sharedPredictor.predictorData = predictorData;
    };

    const updatePredictorName = (predictorName) => {
      sharedPredictor.selectedPredictorName = predictorName;
    };
    return {
      sharedPredictor,
      updatePredictorItem,
      updatePredictorData,
      updatePredictorName,
    };
  },
  data() {
    return {
      searchKeyword: '',
      searchResults: [],
      fetchedData: {},
      showList: false,
    };
  },
  mounted() {
    document.addEventListener('click', this.handleClickOutside);
  },
  beforeUnmount() {
    document.removeEventListener('click', this.handleClickOutside);
  },
  methods: {
    getPlaceholderText() {
      if (this.sharedPredictor.predictorName === 'fiat') {
        return 'Währung';
      } else if (this.sharedPredictor.predictorName === 'crypto') {
        return 'Kryto-Währung';
      } else if (this.sharedPredictor.predictorName === 'kpi') {
        return 'KPI Name';
      } else if (this.sharedPredictor.predictorName === 'commodities') {
        return 'Rohstoff';
      } else if (this.sharedPredictor.predictorName === 'aktie') {
        return 'Aktien Name';
      } else {
        return '';
      }
    },
    getCsvFile(selectedFile) {
      if (selectedFile === 'fiat') {
        return fiatCsv;
      } else if (selectedFile === 'crypto') {
        return cryptoCsv;
      } else if (selectedFile === 'kpi') {
        return kpiCsv;
      } else if (selectedFile === 'commodities') {
        return commoditiesCsv;
      } else {
        return '';
      }
    },
    fetchPredictor() {
      if (this.sharedPredictor.predictorName === 'fiat') {
        axios
          .get(
            `https://www.alphavantage.co/query?function=FX_MONTHLY&from_symbol=${this.selectedPredictorItem}&to_symbol=USD&apikey=ZIYCPWBHDW4VQ4OA`
          )
          .then((response) => {
            this.updatePredictorData(response.data);
            if (response.data.Note) {
              throw new Error(
                'API Limit reached. Please try again in a minute.'
              );
            }
          })
          .catch((error) => {
            console.log(error);
            alert(error);
          });
      } else if (this.sharedPredictor.predictorName === 'crypto') {
        axios

          // &market=EUR sollte USD sein, aber USD ist buggy.
          .get(
            `https://www.alphavantage.co/query?function=DIGITAL_CURRENCY_MONTHLY&symbol=${this.selectedPredictorItem}&market=USD&apikey=ZIYCPWBHDW4VQ4OA`
          )
          .then((response) => {
            this.updatePredictorData(response.data);
            if (response.data.Note) {
              throw new Error(
                'API Limit reached. Please try again in a minute.'
              );
            }
          })
          .catch((error) => {
            console.log(error);
            alert(error);
          });
      } else if (this.sharedPredictor.predictorName === 'kpi') {
        axios
          .get(
            `https://www.alphavantage.co/query?function=${this.selectedPredictorItem}&interval=monthly&apikey=ZIYCPWBHDW4VQ4OA
            `
          )
          .then((response) => {
            this.updatePredictorData(response.data);
            console.log(response.data);
            if (response.data.Note) {
              throw new Error(
                'API Limit reached. Please try again in a minute.'
              );
            }
          })
          .catch((error) => {
            console.log(error);
            alert(error);
          });
      } else if (this.sharedPredictor.predictorName === 'commodities') {
        axios
          .get(
            `https://www.alphavantage.co/query?function=${this.selectedPredictorItem}&interval=monthly&apikey=ZIYCPWBHDW4VQ4OA
            `
          )
          .then((response) => {
            this.updatePredictorData(response.data);
            if (response.data.Note) {
              throw new Error(
                'API Limit reached. Please try again in a minute.'
              );
            }
          })
          .catch((error) => {
            console.log(error);
            alert(error);
          });
      } else if (this.sharedPredictor.predictorName === 'aktie') {
        axios
          .get(
          `https://www.alphavantage.co/query?function=TIME_SERIES_MONTHLY_ADJUSTED&symbol=${this.selectedPredictorItem}&apikey=ZIYCPWBHDW4VQ4OA`
          )
          .then((response) => {
            this.updatePredictorData(response.data);
            if (response.data.Note) {
              throw new Error(
                'API Limit reached. Please try again in a minute.'
              );
            }
          })
          .catch((error) => {
            console.log(error);
            alert(error);
          });
      }
    },
    async searchPredictor() {
      if(this.sharedPredictor.predictorName === 'aktie'){
        this.searchResults = [];
        try {
          await axios.get(
            `https://www.alphavantage.co/query?function=SYMBOL_SEARCH&keywords=${this.searchKeyword}&apikey=ZIYCPWBHDW4VQ4OA`
          )
          .then((response) => {
          this.searchResults = response.data.bestMatches.splice(0, 7);
          if (response.data.Note) {
            throw new Error('API Limit reached. Please try again in a minute.');
          }
        })
        } catch (error) {
          console.error('Fehler beim Laden der Aktien Werte:', error);
        }
      }else{
        try {
          const results = this.getCsvFile(
            this.sharedPredictor.predictorName
          ).filter(
            (word) =>
              word.code.includes(this.searchKeyword) ||
              word.name.includes(this.searchKeyword)
          );
          this.searchResults = results.splice(0, 7);
        } catch (error) {
          console.error('Fehler beim Laden der CSV-Daten:', error);
        }
      }
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
      this.searchPredictor();
    },
  },
};
</script>

<style scoped></style>
