<template>
  <div class="product-management">
    <h1 class="page-title">Gerenciamento de Produtos</h1>

    <!-- Formulário de Produto -->
    <form @submit.prevent="handleSubmit" class="product-form">
      <div class="form-group">
        <label for="name" class="form-label">Nome do Produto</label>
        <input
          id="name"
          v-model="productForm.name"
          type="text"
          required
          class="form-input"
        >
      </div>
      <div class="form-group">
        <label for="price" class="form-label">Preço</label>
        <input
          id="price"
          v-model="productForm.price"
          type="number"
          step="0.01"
          required
          class="form-input"
        >
      </div>
      <div class="form-group">
        <label for="description" class="form-label">Descrição</label>
        <textarea
          id="description"
          v-model="productForm.description"
          rows="3"
          class="form-input"
        ></textarea>
      </div>
      <div class="form-actions">
        <button type="submit" class="btn btn-primary">
          {{ isEditing ? 'Atualizar Produto' : 'Adicionar Produto' }}
        </button>
        <button v-if="isEditing" type="button" @click="cancelEdit" class="btn btn-secondary">
          Cancelar Edição
        </button>
      </div>
    </form>

    <!-- Tabela de Produtos -->
    <div class="product-table-container">
      <table class="product-table">
        <thead>
          <tr>
            <th>Nome</th>
            <th>Preço</th>
            <th>Descrição</th>
            <th>Ações</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="product in products" :key="product.id">
            <td>{{ product.name }}</td>
            <td>R$ {{ product.price.toFixed(2) }}</td>
            <td>{{ product.description }}</td>
            <td>
              <button @click="editProduct(product)" class="btn btn-edit">
                <PencilIcon class="btn-icon" /> Editar
              </button>
              <button @click="deleteProduct(product.id)" class="btn btn-delete">
                <TrashIcon class="btn-icon" /> Excluir
              </button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script setup>
import { ref, reactive, onMounted } from 'vue';
import axios from 'axios';
import { PencilIcon, TrashIcon } from '@heroicons/vue/24/outline';

const products = ref([]);
const isEditing = ref(false);
const editingProductId = ref(null);

const productForm = reactive({
  name: '',
  price: '',
  description: ''
});

const API_URL = 'https://api.example.com/products'; // Substitua pela URL real da sua API

onMounted(async () => {
  await fetchProducts();
});

const fetchProducts = async () => {
  try {
    const response = await axios.get(API_URL);
    products.value = response.data;
  } catch (error) {
    console.error('Erro ao buscar produtos:', error);
    // Aqui você pode adicionar uma notificação de erro para o usuário
  }
};

const handleSubmit = async () => {
  try {
    if (isEditing.value) {
      await axios.put(`${API_URL}/${editingProductId.value}`, productForm);
    } else {
      await axios.post(API_URL, productForm);
    }
    await fetchProducts();
    resetForm();
    // Aqui você pode adicionar uma notificação de sucesso para o usuário
  } catch (error) {
    console.error('Erro ao salvar produto:', error);
    // Aqui você pode adicionar uma notificação de erro para o usuário
  }
};

const editProduct = (product) => {
  productForm.name = product.name;
  productForm.price = product.price;
  productForm.description = product.description;
  isEditing.value = true;
  editingProductId.value = product.id;
};

const cancelEdit = () => {
  resetForm();
};

const deleteProduct = async (productId) => {
  if (confirm('Tem certeza que deseja excluir este produto?')) {
    try {
      await axios.delete(`${API_URL}/${productId}`);
      await fetchProducts();
      // Aqui você pode adicionar uma notificação de sucesso para o usuário
    } catch (error) {
      console.error('Erro ao excluir produto:', error);
      // Aqui você pode adicionar uma notificação de erro para o usuário
    }
  }
};

const resetForm = () => {
  productForm.name = '';
  productForm.price = '';
  productForm.description = '';
  isEditing.value = false;
  editingProductId.value = null;
};
</script>

<style scoped>
.product-management {
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

.product-form {
  background-color: #f8f9fa;
  border: 1px solid #e9ecef;
  border-radius: 8px;
  padding: 20px;
  margin-bottom: 30px;
}

.form-group {
  margin-bottom: 15px;
}

.form-label {
  display: block;
  margin-bottom: 5px;
  font-weight: 600;
  color: #495057;
}

.form-input {
  width: 100%;
  padding: 8px 12px;
  border: 1px solid #ced4da;
  border-radius: 4px;
  font-size: 16px;
}

.form-input:focus {
  outline: none;
  border-color: #80bdff;
  box-shadow: 0 0 0 0.2rem rgba(0,123,255,.25);
}

.form-actions {
  display: flex;
  gap: 10px;
  justify-content: flex-start;
}

.btn {
  padding: 8px 16px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 14px;
  font-weight: 600;
  display: flex;
  align-items: center;
  gap: 5px;
}

.btn-primary {
  background-color: #007bff;
  color: white;
}

.btn-secondary {
  background-color: #6c757d;
  color: white;
}

.btn-edit {
  background-color: #ffc107;
  color: #212529;
}

.btn-delete {
  background-color: #dc3545;
  color: white;
}

.btn:hover {
  opacity: 0.9;
}

.btn-icon {
  width: 16px;
  height: 16px;
}

.product-table-container {
  overflow-x: auto;
}

.product-table {
  width: 100%;
  border-collapse: collapse;
  margin-top: 20px;
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

@media (max-width: 768px) {
  .form-actions {
    flex-direction: column;
  }

  .btn {
    width: 100%;
  }

  .product-table th,
  .product-table td {
    padding: 8px;
  }
}
</style>