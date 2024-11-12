<template>
  <div class="product-list">
    <h1 class="page-title">Lista de Produtos</h1>

    <!-- Barra de busca -->
    <div class="search-bar">
      <input
        v-model="searchQuery"
        @input="debouncedSearch"
        type="text"
        placeholder="Buscar produtos..."
        class="search-input"
      >
      <MagnifyingGlassIcon class="search-icon" />
    </div>

    <!-- Tabela de Produtos -->
    <div class="product-table-container">
      <table class="product-table">
        <thead>
          <tr>
            <th>Nome</th>
            <th>Preço</th>
            <th>Estoque</th>
            <th>Descrição</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="product in products" :key="product.id">
            <td>{{ product.name }}</td>
            <td>R$ {{ product.price.toFixed(2) }}</td>
            <td>{{ product.stock }}</td>
            <td>
              <p class="product-description">{{ product.description }}</p>
            </td>
          </tr>
        </tbody>
      </table>
    </div>

    <!-- Paginação -->
    <div class="pagination">
      <button
        @click="prevPage"
        :disabled="currentPage === 1"
        class="pagination-button"
      >
        <ChevronLeftIcon class="pagination-icon" />
        Anterior
      </button>
      <span class="pagination-info">
        Página {{ currentPage }} de {{ totalPages }}
      </span>
      <button
        @click="nextPage"
        :disabled="currentPage === totalPages"
        class="pagination-button"
      >
        Próxima
        <ChevronRightIcon class="pagination-icon" />
      </button>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, computed } from 'vue';
import axios from 'axios';
import debounce from 'lodash/debounce';
import { MagnifyingGlassIcon, ChevronLeftIcon, ChevronRightIcon } from '@heroicons/vue/24/outline';

const API_URL = 'https://api.example.com/products'; // Substitua pela URL real da sua API
const products = ref([]);
const currentPage = ref(1);
const totalPages = ref(1);
const itemsPerPage = 10; // Ajuste conforme necessário
const searchQuery = ref('');

const fetchProducts = async (page = 1, search = '') => {
  try {
    const response = await axios.get(API_URL, {
      params: {
        page,
        limit: itemsPerPage,
        search
      }
    });
    products.value = response.data.products;
    totalPages.value = response.data.totalPages;
    currentPage.value = page;
  } catch (error) {
    console.error('Erro ao buscar produtos:', error);
    // Aqui você pode adicionar uma notificação de erro para o usuário
  }
};

const debouncedSearch = debounce(() => {
  currentPage.value = 1; // Reset para a primeira página ao buscar
  fetchProducts(1, searchQuery.value);
}, 300);

const prevPage = () => {
  if (currentPage.value > 1) {
    fetchProducts(currentPage.value - 1, searchQuery.value);
  }
};

const nextPage = () => {
  if (currentPage.value < totalPages.value) {
    fetchProducts(currentPage.value + 1, searchQuery.value);
  }
};

onMounted(() => {
  fetchProducts();
});
</script>

<style scoped>
.product-list {
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

.search-bar {
  position: relative;
  margin-bottom: 20px;
}

.search-input {
  width: 100%;
  padding: 10px 40px 10px 15px;
  border: 1px solid #ced4da;
  border-radius: 4px;
  font-size: 16px;
}

.search-input:focus {
  outline: none;
  border-color: #80bdff;
  box-shadow: 0 0 0 0.2rem rgba(0,123,255,.25);
}

.search-icon {
  position: absolute;
  right: 15px;
  top: 50%;
  transform: translateY(-50%);
  width: 20px;
  height: 20px;
  color: #6c757d;
}

.product-table-container {
  overflow-x: auto;
}

.product-table {
  width: 100%;
  border-collapse: collapse;
  margin-bottom: 20px;
}

.product-table th,
.product-table td {
  border: 1px solid #dee2e6;
  padding: 12px;
  text-align: left;
}

.product-table th {
  background-color: #f8f9fa;
  font-weight: 600;
  color: #495057;
}

.product-table tr:nth-child(even) {
  background-color: #f8f9fa;
}

.product-table tr:hover {
  background-color: #e9ecef;
}

.product-description {
  max-width: 300px;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.pagination {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-top: 20px;
}

.pagination-button {
  display: flex;
  align-items: center;
  padding: 8px 16px;
  border: 1px solid #ced4da;
  border-radius: 4px;
  background-color: #fff;
  color: #495057;
  cursor: pointer;
  font-size: 14px;
  transition: background-color 0.3s ease;
}

.pagination-button:hover:not(:disabled) {
  background-color: #e9ecef;
}

.pagination-button:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}

.pagination-icon {
  width: 16px;
  height: 16px;
}

.pagination-info {
  font-size: 14px;
  color: #6c757d;
}

@media (max-width: 768px) {
  .product-table th,
  .product-table td {
    padding: 8px;
  }

  .pagination {
    flex-direction: column;
    align-items: stretch;
    gap: 10px;
  }

  .pagination-button {
    justify-content: center;
  }
}
</style>