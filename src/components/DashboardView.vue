<template>
  <div class="dashboard">
    <header class="dashboard-header">
      <div class="container">
        <h1>Painel - SnakeAnalitic - Gerenciador</h1>
      </div>
    </header>
    <main class="dashboard-main">
      <div class="container">
        <div class="dashboard-grid">
          <DashboardCard 
            title="Gerenciar Produtos" 
            icon="CubeIcon"
            @click="navigateTo('/produtos/gerenciar')"
          /> 
          <DashboardCard 
            title="Lista de Produtos" 
            icon="ClipboardDocumentListIcon"
            @click="navigateTo('/produtos/lista')"
          />
          <DashboardCard 
            title="Pedidos em Andamento" 
            icon="ShoppingCartIcon"
            @click="navigateTo('/pedidos/andamento')"
          />
          <DashboardCard 
            title="Dashboard de Atendimento" 
            icon="UserGroupIcon"
            @click="navigateTo('/atendimento')"
          />
        </div>
      </div>
    </main>
  </div>
</template>

<script setup>
import { useRouter } from 'vue-router';
import DashboardCard from '@/components/DashboardCard.vue';

const router = useRouter();

const navigateTo = (path) => {
  router.push(path);
};
</script>

<style scoped>
.dashboard {
  min-height: 100vh;
  background-color: #f3f4f6;
}

.container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 20px;
}

.dashboard-header {
  background-color: #ffffff;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  padding: 20px 0;
}

.dashboard-header h1 {
  font-size: 24px;
  font-weight: bold;
  color: #1f2937;
}

.dashboard-main {
  padding: 40px 0;
}

.dashboard-grid {
  display: grid;
  grid-template-columns: repeat(1, 1fr);
  gap: 20px;
}

@media (min-width: 640px) {
  .dashboard-grid {
    grid-template-columns: repeat(2, 1fr);
  }
}

@media (min-width: 1024px) {
  .dashboard-grid {
    grid-template-columns: repeat(4, 1fr);
  }
}
</style>