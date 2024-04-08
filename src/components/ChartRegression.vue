<template>
  <div class="mt-8 flex flex-col">
    <div class="bg-white flex justify-center items-center w-[409px] h-[350px]">
      <Scatter
        id="my-chart-id"
        :options="chartOptions"
        :data="regressionFormula"
        class=""
      />
    </div>
  </div>
</template>

<script>
import { Scatter } from 'vue-chartjs';
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
  // Regression (sxy sxx syy etc.)
  name: 'ChartRegression',
  components: { Scatter },
  setup() {
    const predictor = inject('predict');
    const regression = inject('regression');

    const updateBestimmtheitsmass = (newBestimmtheitsmass) => {
      predictor.bestimmtheitsmass = newBestimmtheitsmass;
    };

    const updateKorrelationskoeffizient = (newKorrelationskoeffizient) => {
      predictor.korrelationskoeffizient = newKorrelationskoeffizient;
    };

    const updateSteigung= (newSteigung) => {
      predictor.steigung = newSteigung;
    };

    const updateYAchsenabschnitt = (newYAchsenabschnitt) => {
      predictor.yAchsenAbschnitt = newYAchsenabschnitt;
    };
    
    return { predictor, regression, updateBestimmtheitsmass, updateKorrelationskoeffizient, updateSteigung, updateYAchsenabschnitt };
  },
  computed: {
    regressionFormula() {
      if (
        !this.regression.stockRegressionData ||
        !this.regression.predictorRegressionData
      ) {
        return {
          label: [],
          amountYears: 0,
          minY: 0,
          maxY: 100,
          datasets: [],
        };
      }
      const sliceMax = Math.min(
        this.regression.stockRegressionData.length,
        this.regression.predictorRegressionData.length
      );

      const stockData = this.regression.stockRegressionData.slice(
        this.regression.stockRegressionData.length - sliceMax,
        this.regression.stockRegressionData.length
      );

      const predictorData = this.regression.predictorRegressionData.slice(
        this.regression.predictorRegressionData.length - sliceMax,
        this.regression.predictorRegressionData.length
      );
      if (!stockData || !predictorData) {
        return console.log('no data');
      }
      const scatterData = stockData.map((stock, index) => {
        return {
          x: predictorData[index],
          y: stock,
        };
      });

      if(predictorData.length === 0 || stockData.length === 0){
        return {
          label: [],
          amountYears: 0,
          minY: 0,
          maxY: 100,
          datasets: [],
        };
      }

      // 1. Mittelwerte von x und y berechnen
      // String in Float umwandeln
      const parsedX = predictorData.map((y) => parseFloat(y));
      const parsedY = stockData.map((x) => parseFloat(x));

      const mittelwertX = parsedX.reduce((sum, curr) => sum + curr, 0) / parsedX.length;
      const mittelwertY = parsedY.reduce((sum, curr) => sum + curr, 0) / parsedY.length;
    
      // 2. Steigung (m) der Regressionsgerade berechnen m = Σ((x - x̄) * (y - ȳ)) / Σ((x - x̄)^2)
      // 2.1 Abweichung der x- und y-Werte vom Mittelwert berechnen

      const standardabweichungX = Math.sqrt(parsedX.map(x => Math.pow(x - mittelwertX, 2))
                                                   .reduce((sum, curr) => sum + curr) / (parsedX.length -1));
                                                   
      const standardabweichungY = Math.sqrt(parsedY.map(y => Math.pow(y - mittelwertY, 2))
                                                   .reduce((sum, curr) => sum + curr) / (parsedY.length -1));

      // 2.2 Varianz von x berechnen
      const varianzX = standardabweichungX ** 2;

      // 2.3 Kovarianz von x und y berechnen
      const kovarianzXY = parsedX.map((x, i) => (x - mittelwertX) * (parsedY[i] - mittelwertY))
                                 .reduce((sum, curr) => sum+ curr, 0) / (parsedX.length -1);

      // 2.4 Steigung der Regressionsgerade berechnen
      const steigung = kovarianzXY / varianzX;

      // 2.5 y-Achsenabschnitt (b) der Regressionsgerade berechnen b = ȳ - m * x̄

      const yAchsenAbschnitt = mittelwertY - steigung * mittelwertX;

      // 2.6 Regressionsgerade aufstellen y = mx + b
      const regressionY = (x) => steigung * x + yAchsenAbschnitt;

      // Abweichung der x-Werte vom Mittelwert quadrieren
     
      // 5. Loop values of the line
      const regressionLine = [];
      for (let i = 0; i < scatterData.length; i++) {
        const xValue = scatterData[i].x;
        const yValueOnRegressionLine = regressionY(xValue);
        regressionLine.push({ x: xValue, y: yValueOnRegressionLine });
      }

      // 3. korrelationskoeffizient berechnen
      const korrelationskoeffizient = kovarianzXY / (standardabweichungX * standardabweichungY);

      // 4. Bestimmtsheitsmaß
      const regressionValuesY = parsedX.map(x => yAchsenAbschnitt + steigung * x);

      // Gesamtquadratsumme
      const gesamtQuadratSumme = parsedY.reduce((sum, y) =>
            sum + Math.pow(y - mittelwertY, 2), 0);
      
      // Methode der kleinsten Quadrate
      const summeQuadriertenResiduen  = parsedY.map((y, i) => 
            Math.pow(y - regressionValuesY[i], 2)).reduce((sum, curr) => sum + curr, 0);

      const bestimmtheitsmass = 1 - summeQuadriertenResiduen / gesamtQuadratSumme;

      this.updateBestimmtheitsmass(bestimmtheitsmass);
      this.updateKorrelationskoeffizient(korrelationskoeffizient);
      this.updateSteigung(steigung);
      this.updateYAchsenabschnitt(yAchsenAbschnitt);



      // console.log('Bestimmtheitsmaß', bestimmtheitsmass);
      // console.log('standardabweichungX', standardabweichungX);
      // console.log('standardabweichungY', standardabweichungY);
      // console.log('varianzX', varianzX);
      // console.log('steigung', steigung);
      // console.log('yAchsenAbschnitt', yAchsenAbschnitt);
      // console.log('y = mx + b', regressionY(1));
      // console.log('regressionLine', regressionLine);

      regressionLine.sort((a, b) => a.x - b.x);

      const numPoints = predictorData.length; // Anzahl der gewünschten Punkte auf der x-Achse
      const minX = Math.min(...predictorData);
      const maxX = Math.max(...predictorData);
      const diff = maxX - minX;

      const stepSize = diff / (numPoints - 1);

      return {
        minX,
        maxX,
        stepSize,
        datasets: [
          {
            type: 'line',
            label: 'Regression Line',
            data: regressionLine ?? [],
            fill: false,
            borderColor: '#f87979',
            borderWidth: 2,
          },
          {
            label: 'Regression Punkte',
            data: scatterData ?? [],
            borderColor: '#f87979',
            backgroundColor: '#000', // Color for the points
            borderWidth: 0,
            type: 'scatter',
          },
        ],
      };
    },
    chartOptions() {
      return {
        responsive: true,
        aspectRatio: 1.075,
        scales: {
          x: {
            min: this.regressionFormula.minX ?? 0,
            max: this.regressionFormula.maxX ?? 100,
            ticks: {
              stepSize: this.regressionFormula.stepSize ?? 10,
            },
          },
          y: {
            min: 0,
          },
        },
      };
    },
  },
};
</script>
