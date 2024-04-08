<template>
  <div class="mt-8 flex flex-col">
    <div class="bg-white flex justify-center items-center w-[409px] h-[350px]">
      <Line
        id="my-chart-id"
        :options="chartOptions"
        :data="chartData"
      />
    </div>
    <div v-if="yearsLength" class="text-white text-sm flex justify-end">
      Graph eingrenzen:
      <button
        :class="
          'font-semibold ml-1 mr-2 ' +
          (selectTime.timeFilter === 'month' && 'text-red-500')
        "
        @click="updateTimeFilter('month')"
      >
        Letzten 12 Monate
      </button>
      <button
        :class="
          'font-semibold ml-1 mr-2 ' +
          (selectTime.timeFilter === 'year' && 'text-red-500')
        "
        @click="updateTimeFilter('year')"
      >
        Letzten {{ yearsLength }} Jahre
      </button>
    </div>
  </div>
</template>

<script>
import { Line } from 'vue-chartjs';
import { inject } from 'vue';
import {
  Chart as ChartJS,
  CategoryScale,
  LinearScale,
  PointElement,
  LineElement,
  Title,
  Tooltip,
  Legend,
} from 'chart.js';

ChartJS.register(
  CategoryScale,
  LinearScale,
  PointElement,
  LineElement,
  Title,
  Tooltip,
  Legend
);

export default {
  name: 'ChartPredictor',
  components: { Line },
  setup() {
    const predictor = inject('predict');
    const regression = inject('regression');
    const selectTime = inject('selectTime');

    const updateRegressionPredictor = (newRegressionPredictor) => {
      console.log("newRegressionPredictor ", newRegressionPredictor)
      regression.predictorRegressionData = newRegressionPredictor;
    };

    const updateTimeFilter = (newTimeFilter) => {
      selectTime.timeFilter = newTimeFilter;
    };

    const updateLastMonth = (newLastMonth) => {
      predictor.lastMonth = newLastMonth;
    };

    return {
      predictor,
      regression,
      selectTime,
      updateRegressionPredictor,
      updateTimeFilter,
      updateLastMonth
    };
  },
  computed: {
    chartData() {
      if (!this.predictor.predictorData || !this.predictor.predictorName
      ) {
        return {
          labels: [],
          amountYears: 0,
          minY: 0,
          maxY: 100,
          datasets: [],
        };
      }

      const dataArray = Object.values(this.predictor.predictorData);
      let getValues = dataArray[1];
      if (this.predictor.predictorName === 'fiat') {
        getValues = dataArray[1];
      } else if (this.predictor.predictorName === 'crypto') {
        getValues = dataArray[1];
      } else if (this.predictor.predictorName === 'kpi') {
        getValues = dataArray[3];
      } else if (this.predictor.predictorName === 'commodities') {
        getValues = dataArray[3];
      } else if (this.predictor.predictorName === 'aktie') {
        getValues = dataArray[1];
      } else {
        getValues = dataArray[1];
      }

      const monthlyTimeSeries = this.predictor.predictorData ? getValues : null;

      let labelsYears = [];
      let labelsMonth = [];

      if (
        this.predictor.predictorName === 'kpi' ||
        this.predictor.predictorName === 'commodities'
      ) {
        labelsYears = monthlyTimeSeries
          ? monthlyTimeSeries
              .map((item) => new Date(item.date).getFullYear().toString())
              .filter((value, index, self) => self.indexOf(value) === index)
              .slice(0, 10)
              .reverse()
          : [];
        labelsMonth = monthlyTimeSeries
          ? monthlyTimeSeries
              .map((item) => new Date(item.date).toISOString().slice(0, 7))
              .filter((value, index, self) => self.indexOf(value) === index)
              .slice(0, 12)
              .reverse()
          : [];
      } else {
        labelsYears = monthlyTimeSeries
          ? Object.keys(monthlyTimeSeries)
              .map((date) => date.slice(0, 4))
              .filter((value, index, self) => self.indexOf(value) === index)
              .slice(0, 10)
              .reverse()
          : [];
        labelsMonth = monthlyTimeSeries
          ? Object.keys(monthlyTimeSeries)
              .map((date) => date.slice(0, 7))
              .filter((value, index, self) => self.indexOf(value) === index)
              .slice(0, 12)
              .reverse()
          : [];
      }

      this.updateLastMonth(labelsMonth.slice(-1));

      const predictorData = monthlyTimeSeries
        ? this.selectTime.timeFilter === 'month'
          ? Object.values(monthlyTimeSeries)
              .map((stock) => {
                if (this.predictor.predictorName === 'fiat') {
                  return parseFloat(stock['4. close']).toFixed(2);
                } else if (this.predictor.predictorName === 'crypto') {
                  return parseFloat(stock['4b. close (USD)']).toFixed(2);
                }else if (this.predictor.predictorName === 'aktie') {
                  return parseFloat(stock['5. adjusted close']).toFixed(2);
                }else if (
                  this.predictor.predictorName === 'kpi' ||
                  this.predictor.predictorName === 'commodities'
                ) {
                  return parseFloat(stock['value']).toFixed(2);
                } else {
                  return [];
                }
              })
              .slice(0, 12)
              .reverse()
          : labelsYears.map((year) => {
              let yearlyValues = [];
              if (
                this.predictor.predictorName === 'fiat' ||
                this.predictor.predictorName === 'crypto' ||
                this.predictor.predictorName === 'aktie'
              ) {
                yearlyValues = Object.keys(monthlyTimeSeries).map((stock) => {
                  if (year === stock.slice(0, 4)) {
                    if (this.predictor.predictorName === 'fiat') {
                      return parseFloat(
                        monthlyTimeSeries[stock]['4. close']
                      ).toFixed(2);
                    } else if (this.predictor.predictorName === 'crypto') {
                      return parseFloat(
                        monthlyTimeSeries[stock]['4b. close (USD)']
                      ).toFixed(2);
                    } else if (this.predictor.predictorName === 'aktie') {
                      return parseFloat(
                        monthlyTimeSeries[stock]['5. adjusted close']
                      ).toFixed(2);
                    } else {
                      return [];
                    }
                  }
                });
              } else if (
                this.predictor.predictorName === 'kpi' ||
                this.predictor.predictorName === 'commodities'
              ) {
                yearlyValues = monthlyTimeSeries.map((stock) => {
                  if (year === stock.date.slice(0, 4)) {
                    if (this.predictor.predictorName === 'kpi') {
                      return parseFloat(stock.value).toFixed(2);
                    } else if (this.predictor.predictorName === 'commodities') {
                      return parseFloat(stock.value).toFixed(2);
                    } else {
                      return [];
                    }
                  }
                });
              }

              const filteredYearlyValues = yearlyValues.filter((value) => {
                return !isNaN(value);
              });

              const sum = filteredYearlyValues.reduce((accumulator, value) => {
                return accumulator + parseFloat(value);
              }, 0);

              return (sum / filteredYearlyValues.length).toFixed(2);
            })
        : [];

      this.updateRegressionPredictor(predictorData);

      return {
        labels:
          this.selectTime.timeFilter === 'month' ? labelsMonth : labelsYears,
        amountYears: labelsYears ?? 0,
        datasets: [
          {
            label: this.predictor.selectedPredictorName ?? '',
            data: predictorData ?? [],
            backgroundColor: '#f87979',
            borderColor: '#f87979',
          },
        ],
      };
    },
    chartOptions() {
      return {
        responsive: true,
        aspectRatio: 1.075,
      };
    },
    yearsLength() {
      const amountYears = this.chartData.amountYears;
      return amountYears?.length ?? 0;
    },
  },
};
</script>
