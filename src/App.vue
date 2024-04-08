<template>
  <div>
    <HeaderComponent class="z-50" />
    <ContentComponent class="z-40" />
    <FooterComponent />
  </div>
</template>

<script>
import HeaderComponent from './components/HeaderComponent.vue';
import ContentComponent from './components/ContentComponent.vue';
import FooterComponent from './components/FooterComponent.vue';
import { provide, reactive } from 'vue';

export default {
  name: 'App',
  components: {
    HeaderComponent,
    ContentComponent,
    FooterComponent,
  },
  setup() {
    const sharedStocks = reactive({
      stockData: null,
      stockName: null,
    });

    const predictor = reactive({
      predictorName: 'fiat',
      selectedPredictorName: '',
      selectedPredictor: null,
      lastMonth: null,
      predictorData: null,
      bestimmtheitsmass: null,
      korrelationskoeffizient: null,
      steigung: null,
      yAchsenAbschnitt: null,
    });

    const regressionData = reactive({
      stockRegressionData: null,
      predictorRegressionData: null,
    });

    const selectTime = reactive({
      timeFilter: 'month',
    });

    provide('stocks', sharedStocks);
    provide('predict', predictor);
    provide('regression', regressionData);
    provide('selectTime', selectTime);
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
</style>
