<template lang="">
  <div class="w-96">
        <div class="relative">
          <input
            type="number"
            v-model="predictorValue"
            class="block w-full p-4 pl-10 text-sm border rounded-lg focus:ring-blue-500 focus:border-blue-500 bg-gray-700 border-gray-600 placeholder-gray-400 text-white outline-0"
            placeholder="Prädiktorwert"
            required
          />
        </div>
    <div v-if="stockName && sharedPredictor" class="flex justify-start text-white pt-3 text-lg font-semibold">
      Korrelationskoeffizient: {{sharedPredictor.korrelationskoeffizient?.toFixed(2)}}
    </div>
    <div v-if="stockName && sharedPredictor" class="flex justify-start text-white pt-3 text-lg font-semibold">
      Bestimmtheitsmaß: {{sharedPredictor.bestimmtheitsmass?.toFixed(2)}}
    </div>
    <div v-if="stockName && sharedPredictor && predictorValue" class="flex justify-start text-white pt-3 text-lg font-semibold">
      <div>
        Prognose {{stockName}}:&nbsp;
      </div>
      <div>
        {{forcastValue.toFixed(2)}}
      </div>
    </div>
  </div>
</template>

<script>
import { inject, ref } from 'vue';

export default {
  name: 'ForecastPredictor',

  setup() {
    const sharedStocks = inject('stocks');
    const sharedPredictor = inject('predict');
    const predictorValue = ref(null);

    return {
      sharedStocks,
      sharedPredictor,
      predictorValue
    };
  },
  computed:{
    stockName(){
      return this.sharedStocks.stockName?.['2. name']
    },
    forcastValue(){
      if(!this.predictorValue) return null

      // m = sxy / sxx
      const m = this.sharedPredictor.steigung;

      // x = Kriteriumswert
      const x = this.predictorValue;

      // b = y-Achsenabschnitt
      const b = this.sharedPredictor.yAchsenAbschnitt;

      // y= m * x + b bzw. y = α + β x 
      return m * x + b
    },
  }
}

</script>