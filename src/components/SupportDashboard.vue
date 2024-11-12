<template>
  <div class="support-dashboard">
    <h1 class="dashboard-title">Dashboard de Atendimento e Faturamento</h1>

    <!-- Filtros de data com intervalo -->
    <div class="filters">
      <div class="filter-group">
        <label for="startDate" class="filter-label">Data Inicial</label>
        <input
          type="date"
          id="startDate"
          v-model="filters.startDate"
          class="filter-input"
        >
      </div>
      <div class="filter-group">
        <label for="endDate" class="filter-label">Data Final</label>
        <input
          type="date"
          id="endDate"
          v-model="filters.endDate"
          class="filter-input"
        >
      </div>
      <button @click="applyFilters" class="search-button">
        <MagnifyingGlassIcon class="search-icon" />
        Filtrar
      </button>
    </div>

    <!-- Gráficos -->
    <div class="charts-container">
      <!-- Gráfico de atendimento (Pizza) -->
      <div class="chart-card">
        <h2 class="chart-title">Atendimento via WhatsApp</h2>
        <div class="chart-wrapper">
          <canvas ref="supportChartRef"></canvas>
        </div>
      </div>

      <!-- Gráfico de faturamento (Barra) -->
      <div class="chart-card">
        <h2 class="chart-title">Faturamento de Vendas</h2>
        <div class="chart-wrapper">
          <canvas ref="salesChartRef"></canvas>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted, reactive } from 'vue';
import Chart from 'chart.js/auto';
import { MagnifyingGlassIcon } from '@heroicons/vue/24/outline';
import io from 'socket.io-client';
import axios from 'axios';

const API_URL = 'https://api.example.com/dashboard-data';
const WEBSOCKET_URL = 'wss://api.example.com';

const supportChartRef = ref(null);
const salesChartRef = ref(null);
let supportChart = null;
let salesChart = null;
let socket = null;
let intervalId = null;

const filters = reactive({
  startDate: new Date(new Date().setDate(new Date().getDate() - 30)).toISOString().split('T')[0], // 30 dias atrás
  endDate: new Date().toISOString().split('T')[0] // Hoje
});

const supportChartData = reactive({
  labels: ['Recebidas', 'Atendidas', 'Finalizadas', 'Não Concluídas'],
  datasets: [{
    data: [300, 250, 200, 50],
    backgroundColor: [
      'rgba(255, 99, 132, 0.8)',
      'rgba(54, 162, 235, 0.8)',
      'rgba(75, 192, 192, 0.8)',
      'rgba(255, 206, 86, 0.8)'
    ],
    borderColor: [
      'rgba(255, 99, 132, 1)',
      'rgba(54, 162, 235, 1)',
      'rgba(75, 192, 192, 1)',
      'rgba(255, 206, 86, 1)'
    ],
    borderWidth: 1
  }]
});

const salesChartData = reactive({
  labels: ['Jan', 'Fev', 'Mar', 'Abr', 'Mai', 'Jun'],
  datasets: [{
    label: 'Faturamento (R$)',
    data: [12000, 19000, 15000, 25000, 22000, 30000],
    backgroundColor: 'rgba(75, 192, 192, 0.6)',
    borderColor: 'rgba(75, 192, 192, 1)',
    borderWidth: 1
  }]
});

const createCharts = () => {
  const supportCtx = supportChartRef.value.getContext('2d');
  supportChart = new Chart(supportCtx, {
    type: 'pie',
    data: supportChartData,
    options: {
      responsive: true,
      maintainAspectRatio: false,
      plugins: {
        legend: {
          position: 'bottom',
        },
        tooltip: {
          callbacks: {
            label: function(context) {
              let label = context.label || '';
              if (label) {
                label += ': ';
              }
              if (context.parsed !== null) {
                label += context.parsed + ' atendimentos';
              }
              return label;
            }
          }
        }
      },
      onClick: (event, elements) => {
        if (elements.length > 0) {
          const index = elements[0].index;
          alert(`Detalhes para ${supportChartData.labels[index]}: ${supportChartData.datasets[0].data[index]} atendimentos`);
        }
      },
    }
  });

  const salesCtx = salesChartRef.value.getContext('2d');
  salesChart = new Chart(salesCtx, {
    type: 'bar',
    data: salesChartData,
    options: {
      responsive: true,
      maintainAspectRatio: false,
      plugins: {
        legend: {
          display: false,
        },
        tooltip: {
          callbacks: {
            label: function(context) {
              let label = context.dataset.label || '';
              if (label) {
                label += ': ';
              }
              if (context.parsed.y !== null) {
                label += new Intl.NumberFormat('pt-BR', { style: 'currency', currency: 'BRL' }).format(context.parsed.y);
              }
              return label;
            }
          }
        }
      },
      scales: {
        y: {
          beginAtZero: true,
          ticks: {
            callback: function(value, index, values) {
              return new Intl.NumberFormat('pt-BR', { style: 'currency', currency: 'BRL' }).format(value);
            }
          }
        }
      },
      onClick: (event, elements) => {
        if (elements.length > 0) {
          const index = elements[0].index;
          alert(`Faturamento em ${salesChartData.labels[index]}: ${new Intl.NumberFormat('pt-BR', { style: 'currency', currency: 'BRL' }).format(salesChartData.datasets[0].data[index])}`);
        }
      },
    }
  });
};

const updateCharts = (newData) => {
  supportChartData.datasets[0].data = newData.support;
  supportChart.update();

  salesChartData.labels = newData.sales.labels;
  salesChartData.datasets[0].data = newData.sales.data;
  salesChart.update();
};

const fetchData = async () => {
  try {
    const response = await axios.get(API_URL, { params: filters });
    updateCharts(response.data);
  } catch (error) {
    console.error('Erro ao buscar dados do dashboard:', error);
    alert('Erro ao carregar os dados. Por favor, tente novamente mais tarde.');
  }
};

const setupWebSocket = () => {
  socket = io(WEBSOCKET_URL);

  socket.on('connect', () => {
    console.log('Conectado ao WebSocket');
  });

  socket.on('dashboardDataUpdate', (newData) => {
    updateCharts(newData);
  });

  socket.on('disconnect', () => {
    console.log('Desconectado do WebSocket');
  });
};

const setupPolling = () => {
  intervalId = setInterval(fetchData, 30000); // Atualiza a cada 30 segundos
};

const applyFilters = () => {
  fetchData();
  if (socket && socket.connected) {
    socket.emit('updateFilters', filters);
  }
};

onMounted(() => {
  createCharts();
  fetchData();
  
  // Tenta configurar WebSocket primeiro
  setupWebSocket();
  
  // Se o WebSocket falhar, configura polling como fallback
  if (!socket || !socket.connected) {
    console.log('WebSocket não disponível, usando polling como fallback');
    setupPolling();
  }
});

onUnmounted(() => {
  if (supportChart) {
    supportChart.destroy();
  }
  if (salesChart) {
    salesChart.destroy();
  }
  if (socket) {
    socket.disconnect();
  }
  if (intervalId) {
    clearInterval(intervalId);
  }
});
</script>

<style scoped>
.support-dashboard {
  max-width: 1200px;
  margin: 0 auto;
  padding: 20px;
  font-family: Arial, sans-serif;
}

.dashboard-title {
  font-size: 28px;
  font-weight: bold;
  margin-bottom: 20px;
  color: #333;
  text-align: center;
}

.filters {
  display: flex;
  flex-wrap: wrap;
  gap: 15px;
  margin-bottom: 20px;
  background-color: #f5f5f5;
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.filter-group {
  display: flex;
  flex-direction: column;
  flex: 1;
  min-width: 150px;
}

.filter-label {
  font-size: 14px;
  margin-bottom: 5px;
  color: #555;
  font-weight: bold;
}

.filter-input {
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 4px;
  font-size: 14px;
  transition: border-color 0.3s ease;
}

.filter-input:focus {
  outline: none;
  border-color: #4a90e2;
  box-shadow: 0 0 0 2px rgba(74, 144, 226, 0.2);
}

.search-button {
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 10px 20px;
  background-color: #4a90e2;
  color: white;
  border: none;
  border-radius: 4px;
  font-size: 16px;
  font-weight: bold;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.search-button:hover {
  background-color: #3a7bc8;
}

.search-icon {
  width: 20px;
  height: 20px;
  margin-right: 8px;
}

.charts-container {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
  gap: 20px;
}

.chart-card {
  background-color: #fff;
  border-radius: 8px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  padding: 20px;
  transition: transform 0.3s ease, box-shadow 0.3s ease;
}

.chart-card:hover {
  transform: translateY(-5px);
  box-shadow: 0 6px 12px rgba(0, 0, 0, 0.15);
}

.chart-title {
  font-size: 20px;
  font-weight: bold;
  margin-bottom: 15px;
  color: #333;
  text-align: center;
}

.chart-wrapper {
  height: 300px; /* Ajuste este valor conforme necessário */
  position: relative;
}

@media (max-width: 768px) {
  .filters {
    flex-direction: column;
  }

  .filter-group {
    width: 100%;
  }

  .search-button {
    width: 100%;
    margin-top: 10px;
  }

  .chart-wrapper {
    height: 250px; /* Altura menor para dispositivos móveis */
  }
}
</style>