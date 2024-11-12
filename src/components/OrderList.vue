<template>
  <div class="order-list">
    <h1 class="page-title">Pedidos em Andamento</h1>

    <div class="order-table-container">
      <table class="order-table">
        <thead>
          <tr>
            <th>Nº do Pedido</th>
            <th>Cliente</th>
            <th>Itens</th>
            <th>Status</th>
            <th>Última Atualização</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="order in orders" :key="order.id">
            <td>{{ order.id }}</td>
            <td>{{ order.customerName }}</td>
            <td>
              <ul class="order-items">
                <li v-for="item in order.items" :key="item.id">
                  {{ item.name }} ({{ item.quantity }})
                </li>
              </ul>
            </td>
            <td>
              <span :class="['order-status', getStatusClass(order.status)]">
                {{ order.status }}
              </span>
            </td>
            <td>{{ formatDate(order.updatedAt) }}</td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from 'vue';
import axios from 'axios';
import io from 'socket.io-client';

const API_URL = 'https://api.example.com/orders';
const WEBSOCKET_URL = 'wss://api.example.com';

const orders = ref([]);
let socket = null;
let intervalId = null;

const fetchOrders = async () => {
  try {
    const response = await axios.get(API_URL);
    orders.value = response.data;
  } catch (error) {
    console.error('Erro ao buscar pedidos:', error);
    // Aqui você pode adicionar uma notificação de erro para o usuário
  }
};

const setupWebSocket = () => {
  socket = io(WEBSOCKET_URL);

  socket.on('connect', () => {
    console.log('Conectado ao WebSocket');
  });

  socket.on('orderUpdate', (updatedOrder) => {
    const index = orders.value.findIndex(order => order.id === updatedOrder.id);
    if (index !== -1) {
      orders.value[index] = updatedOrder;
    } else {
      orders.value.push(updatedOrder);
    }
  });

  socket.on('disconnect', () => {
    console.log('Desconectado do WebSocket');
  });
};

const setupPolling = () => {
  intervalId = setInterval(fetchOrders, 30000); // Atualiza a cada 30 segundos
};

const getStatusClass = (status) => {
  const classes = {
    'Pedido recebido': 'status-received',
    'Sendo preparado': 'status-preparing',
    'Finalizado': 'status-finished',
    'Saiu para entrega': 'status-delivering'
  };
  return classes[status] || 'status-unknown';
};

const formatDate = (dateString) => {
  return new Date(dateString).toLocaleString();
};

onMounted(() => {
  fetchOrders();
  
  // Tenta configurar WebSocket primeiro
  setupWebSocket();
  
  // Se o WebSocket falhar, configura polling como fallback
  if (!socket || !socket.connected) {
    console.log('WebSocket não disponível, usando polling como fallback');
    setupPolling();
  }
});

onUnmounted(() => {
  if (socket) {
    socket.disconnect();
  }
  if (intervalId) {
    clearInterval(intervalId);
  }
});
</script>

<style scoped>
.order-list {
  max-width: 1200px;
  margin: 0 auto;
  padding: 20px;
}

.page-title {
  font-size: 24px;
  font-weight: bold;
  margin-bottom: 20px;
  color: #333;
}

.order-table-container {
  overflow-x: auto;
}

.order-table {
  width: 100%;
  border-collapse: collapse;
  margin-bottom: 20px;
}

.order-table th,
.order-table td {
  border: 1px solid #dee2e6;
  padding: 12px;
  text-align: left;
}

.order-table th {
  background-color: #f8f9fa;
  font-weight: 600;
  color: #495057;
}

.order-table tr:nth-child(even) {
  background-color: #f8f9fa;
}

.order-table tr:hover {
  background-color: #e9ecef;
}

.order-items {
  list-style-type: none;
  padding: 0;
  margin: 0;
}

.order-items li {
  margin-bottom: 5px;
}

.order-status {
  display: inline-block;
  padding: 5px 10px;
  border-radius: 20px;
  font-size: 14px;
  font-weight: 600;
}

.status-received {
  background-color: #cce5ff;
  color: #004085;
}

.status-preparing {
  background-color: #fff3cd;
  color: #856404;
}

.status-finished {
  background-color: #d4edda;
  color: #155724;
}

.status-delivering {
  background-color: #e8eaff;
  color: #2c3e50;
}

.status-unknown {
  background-color: #f8d7da;
  color: #721c24;
}

@media (max-width: 768px) {
  .order-table th,
  .order-table td {
    padding: 8px;
  }

  .order-status {
    padding: 3px 6px;
    font-size: 12px;
  }
}
</style>