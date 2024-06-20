<template>
  <div class="container">
    <h1>Risk / Reward Graph</h1>
    <canvas :id="canvasId"></canvas>

    <div class="select">
      <div v-for="(option, index) in optionSelected" :key="index">
        <input :id="'option-' + index" type="checkbox" v-model="optionSelected[index]" />
        <label :for="'option-' + index">
          {{ optionLabel(optionsData[index]) }}
        </label>
      </div>
    </div>

    <div v-if="maxProfit !== null && maxLoss !== null && breakEvenPoints.length">
      <p>Max Profit: {{ maxProfit }}</p>
      <p>Max Loss: {{ maxLoss }}</p>
      <p>Break Even Points: {{ breakEvenPoints.join(', ') }}</p>
    </div>
  </div>
</template>

<script>
import Chart from 'chart.js/auto';
/*
const findXAxisIntersection = (x1, y1, x2, y2) => {
  const m = (y2 - y1) / (x2 - x1);
  const c = y1 - m * x1;
  return -c / m;
}
*/

export default {
  name: 'CodingChallenge',
  props: {
    optionsData: {
      type: Array,
      required: true
    }
  },
  data() {
    return {
      canvasId: 'pl-graph-' + Math.ceil(Math.random() * 10000),
      prices: [],
      profits: [],
      maxProfit: null,
      maxLoss: null,
      breakEvenPoints: [],
      chart: null,
      optionSelected: new Array(this.optionsData.length).fill(true)
    }
  },
  computed: {
    selectedOptions() {
      return this.optionsData.filter((option, index) => this.optionSelected[index])
    }
  },
  methods: {
    optionLabel(option) {
      return `${option.long_short[0].toUpperCase()}${option.long_short.slice(1)} ${option.type} - ${option.strike_price}`
    },
    calculatePayOff() {
      const minPrice = 0;
      const maxPrice = Math.max(...this.selectedOptions.map(opt => opt.strike_price)) * 1.5;
      const prices = [minPrice, maxPrice];
      const profits = [];

      for (const option of this.selectedOptions) {
        prices.push(option.strike_price)
        if (option.type === 'Call') {
          prices.push(option.strike_price + (option.bid + option.ask) / 2)
        } else {
          prices.push(option.strike_price - (option.bid + option.ask) / 2)
        }
      }
      prices.sort((a, b) => a - b);

      for (const price of prices) {
        profits.push(this.selectedOptions.reduce((acc, { strike_price, type, long_short, bid, ask }) => {
          const cost = (bid + ask) / 2;

          if (type === 'Call') {
            if (long_short === 'long') {
              return acc + Math.max(0, price - strike_price) - cost;
            } else {
              return acc + cost - Math.max(0, price - strike_price);
            }
          } else if (type === 'Put') {
            if (long_short === 'long') {
              return acc + Math.max(0, strike_price - price) - cost;
            } else {
              return acc + cost - Math.max(0, strike_price - price);
            }
          }
        }, 0));
      }

      this.prices = prices;
      this.profits = profits;
      this.maxProfit = Math.max(...profits);
      this.maxLoss = Math.min(...profits);
      this.breakEvenPoints = prices.filter((price, index) => profits[index] === 0);
    },
    drawChart() {
      if (this.chart) {
        this.chart.destroy();
      }

      this.chart = new Chart(document.getElementById(this.canvasId), {
        type: 'line',
        data: {
          labels: this.prices,
          datasets: [{
            label: 'Loss/Profit',
            data: this.profits,
            borderColor: 'rgba(175, 192, 192, 1)',
            borderWidth: 2,
            fill: false
          }]
        },
        options: {
          scales: {
            x: {
              title: {
                display: true,
                text: 'Stock Price'
              },
            },
            y: {
              title: {
                display: true,
                text: 'Loss/Profit'
              }
            }
          }
        }
      });
    }
  },
  mounted() {
    this.calculatePayOff()
    this.drawChart()
  },
  watch: {
    optionSelected() {
      this.calculatePayOff()
      this.drawChart()
    }
  }
};
</script>

<style scoped>
canvas {
  max-height: 480px;
}

.select {
  margin-top: 24px;
  display: flex;
  justify-content: center;
  flex-wrap: wrap;
  gap: 12px;
}
</style>