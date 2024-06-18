<template>
  <div id="app">
    <CodingChallenge :optionsData="sampleData"/>
  </div>
</template>

<script>
import CodingChallenge from './components/CodingChallenge.vue'

export default {
  name: 'App',
  components: {
    CodingChallenge
  },
  data() {
    return {
      sampleData: [
        {
          "strike_price": 100, 
          "type": "Call", 
          "bid": 10.05, 
          "ask": 12.04, 
          "long_short": "long", 
          "expiration_date": "2025-12-17T00:00:00Z"
        },
        {
          "strike_price": 102.50, 
          "type": "Call", 
          "bid": 12.10, 
          "ask": 14, 
          "long_short": "long", 
          "expiration_date": "2025-12-17T00:00:00Z"
        },
        {
          "strike_price": 103, 
          "type": "Put", 
          "bid": 14, 
          "ask": 15.50, 
          "long_short": "short", 
          "expiration_date": "2025-12-17T00:00:00Z"
        },
        {
          "strike_price": 105, 
          "type": "Put", 
          "bid": 16, 
          "ask": 18, 
          "long_short": "long", 
          "expiration_date": "2025-12-17T00:00:00Z"
        }
      ]
    }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
