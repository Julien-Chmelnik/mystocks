<template>
  <div
    class="main-content min-h-screen bg-zinc-800 pt-6"
    @click="showList = false"
  >
    <div class="flex justify-center">
      <div class="w-[850px] mr-8">
        <ChartStocks />
        <div class="flex justify-between">
          <ChartPredictor />
          <ChartRegression />
        </div>
      </div>
      <div class="">
        <SelectCriterion class="mb-3" />
        <PredictorData class="mb-3 z-30 relative" />
        <ForecastPredictor class="mb-3 z-0"/>
      </div>
    </div>
  </div>
</template>

<script>
import ChartStocks from './ChartStocks.vue';
import SelectCriterion from './SelectCriterion.vue';
import PredictorData from './PredictorData.vue';
import ChartPredictor from './ChartPredictor.vue';
import ChartRegression from './ChartRegression.vue';
import ForecastPredictor from './ForecastPredictor.vue'
export default {
  name: 'ContentComponent',
  components: {
    ChartStocks,
    SelectCriterion,
    PredictorData,
    ChartPredictor,
    ChartRegression,
    ForecastPredictor
  },
};
</script>

<style scoped></style>
