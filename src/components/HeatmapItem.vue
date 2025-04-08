<script setup>
const props = defineProps({
  coin: {
    type: Object,
    required: true
  },
  selectedPeriod: {
    type: String,
    default: '24h'
  }
})

function formatPercentage(value) {
  if (value === null || value === undefined) return 'N/A';
  return `${value >= 0 ? '+' : ''}${value.toFixed(2)}%`;
}

function formatCurrency(value) {
  return `$${value.toLocaleString('en-US', { 
    minimumFractionDigits: 2,
    maximumFractionDigits: 2
  })}`;
}

function getValueClass(value) {
  if (value === null || value === undefined) return 'neutral';
  return value > 0 ? 'positive' : value < 0 ? 'negative' : 'neutral';
}
</script>

<template>
  <div class="coin-item">
    <div class="coin-info">
      <h3>{{ coin.symbol.toUpperCase() }}</h3>
      <p class="current-price">{{ formatCurrency(coin.currentPrice) }}</p>
    </div>
    
    <div class="percentage-container" :class="getValueClass(coin.changes[selectedPeriod])">
      <div class="period-indicator">{{ selectedPeriod }}</div>
      <div class="percentage-value">
        {{ formatPercentage(coin.changes[selectedPeriod]) }}
      </div>
    </div>
  </div>
</template>

<style scoped>
.coin-item {
  background-color: #000000;
  border-radius: 8px;
  padding: 16px;
  box-shadow: 0 2px 4px rgba(0,0,0,0.05);
  transition: transform 0.2s;
}

.coin-item:hover {
  transform: translateY(-2px);
  box-shadow: 0 4px 8px rgba(0,0,0,0.1);
}

.coin-info {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 12px;
  color: #dcdcdc;
}

.coin-info h3 {
  color: #dcdcdc;
  margin: 0;
  font-size: 1.1rem;
}

.current-price {
  font-weight: bold;
  font-size: 1.2rem;
  margin: 0;
}

.percentage-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 12px;
  border-radius: 6px;
  text-align: center;
}

.period-indicator {
  font-size: 0.85rem;
  margin-bottom: 4px;
  color: inherit;
  opacity: 0.8;
}

.percentage-value {
  font-size: 1.5rem;
  font-weight: bold;
}

.positive {
  background-color: rgba(40, 167, 69, 0.1);
  color: #28a745;
}

.negative {
  background-color: rgba(220, 53, 69, 0.1);
  color: #dc3545;
}

.neutral {
  background-color: rgba(108, 117, 125, 0.1);
  color: #6c757d;
}
</style>