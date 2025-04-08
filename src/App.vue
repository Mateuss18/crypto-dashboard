<script setup>
import { onMounted, ref } from 'vue'
import HeatmapItem from './components/HeatmapItem.vue'
import axios from 'axios'

const loading = ref(null)
const coins = ref([])
const selectedPeriod = ref('24h')

const availablePeriods = [
  { value: '1h', label: '1 hora' },
  { value: '24h', label: '24 horas' },
  { value: '7d', label: '7 dias' },
  { value: '30d', label: '30 dias' }
]

async function getHeatmapData() {
  loading.value = true

  try {
    const response = await axios.get(
      'https://api.coingecko.com/api/v3/coins/markets', {
        params: {
          vs_currency: 'usd',
          order: 'market_cap_desc',
          per_page: 50,
          page: 1,
          price_change_percentage: '1h,24h,7d,30d' 
        }
      }
    )

    coins.value = processData(response.data)
    
  } catch (error) {
    console.error('Ah não manooo, deu erro :/');
  } finally {
    loading.value = false
  }
}

function processData(data) {
  return data.map(coin => ({
    id: coin.id,
    name: coin.name,
    symbol: coin.symbol,
    currentPrice: coin.current_price, 
    changes: {
      '1h': coin.price_change_percentage_1h_in_currency,
      '24h': coin.price_change_percentage_24h_in_currency,
      '7d': coin.price_change_percentage_7d_in_currency,
      '30d': coin.price_change_percentage_30d_in_currency
    }
  }))
}

onMounted(() => {
  getHeatmapData()
})
</script>

<template>
  <div>
    <h1>Crypto Heatmap</h1>
    
    <div class="period-selector">
      <label for="period-select">Selecionar período: </label>
      <select id="period-select" v-model="selectedPeriod">
        <option v-for="period in availablePeriods" :key="period.value" :value="period.value">
          {{ period.label }}
        </option>
      </select>
    </div>
    
    <div v-if="loading" class="loading">Carregando dados...</div>
    <div v-else class="heatmap-grid">
      <HeatmapItem 
        v-for="coin in coins" 
        :key="coin.id" 
        :coin="coin" 
        :selectedPeriod="selectedPeriod"
      />
    </div>
  </div>
</template>

<style scoped>
h1 {
  text-align: center;
  margin-top: 12px;
  font-weight: 600;
  font-size: 32px;
  color: #FFF;
}
.heatmap-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
  gap: 16px;
  padding: 20px;
}

.loading {
  text-align: center;
  padding: 40px;
  font-size: 1.2rem;
  color: #666;
}

.period-selector {
  margin: 20px;
  text-align: center;
}

select {
  padding: 8px 12px;
  border-radius: 4px;
  border: 1px solid #ccc;
  background-color: white;
  font-size: 16px;
  margin-left: 8px;
}
</style>