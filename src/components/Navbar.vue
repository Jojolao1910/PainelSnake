<template>
  <nav class="navbar">
    <div class="navbar-container">
      <div class="navbar-logo">
        <img src="/project/workspace/src/icons/logoS.png" alt="Logo" class="logo-image">
      </div>
      <button class="navbar-toggle" @click="toggleMobileMenu" aria-label="Toggle menu">
        <span class="navbar-toggle-icon"></span>
      </button>
      <div class="navbar-menu" :class="{ 'is-active': isMobileMenuOpen }">
        <router-link to="/dashboard" class="navbar-item" title="Dashboard">
          <HomeIcon class="navbar-icon" />
        </router-link>
        <router-link to="/produtos/gerenciar" class="navbar-item" title="Gerenciar Produtos">
          <CubeIcon class="navbar-icon" />
        </router-link>
        <router-link to="/produtos/lista" class="navbar-item" title="Lista de Produtos">
          <ClipboardDocumentListIcon class="navbar-icon" />
        </router-link>
        <router-link to="/pedidos/andamento" class="navbar-item" title="Pedidos em Andamento">
          <ShoppingCartIcon class="navbar-icon" />
        </router-link>
        <router-link to="/atendimento" class="navbar-item" title="Dashboard de Atendimento">
          <UserGroupIcon class="navbar-icon" />
        </router-link>
      </div>
      <div class="navbar-buttons">
        
        <button @click="logout" class="navbar-button logout-button">
          <ArrowRightOnRectangleIcon class="navbar-icon" />
          <span class="navbar-button-text">Sair</span>
        </button>
      </div>
    </div>
  </nav>
</template>

<script setup>
import { ref } from 'vue'
import { useRouter } from 'vue-router'
import { useAuthStore } from '@/stores/auth'
import { 
  HomeIcon, 
  CubeIcon, 
  ClipboardDocumentListIcon, 
  ShoppingCartIcon, 
  UserGroupIcon,
  ArrowRightOnRectangleIcon
} from '@heroicons/vue/24/outline'

const router = useRouter()
const authStore = useAuthStore()

const isMobileMenuOpen = ref(false)

const toggleMobileMenu = () => {
  isMobileMenuOpen.value = !isMobileMenuOpen.value
}

const logout = () => {
  authStore.logout()
  router.push('/login')
}
</script>

<style scoped>
.navbar {
  background-color: #2c3e50;
  padding: 1rem;
}

.navbar-container {
  display: flex;
  justify-content: space-between;
  align-items: center;
  max-width: 1200px;
  margin: 0 auto;
}

.navbar-logo {
  display: flex;
  align-items: center;
}

.logo-image {
  height: 80px;
  width: auto;
}

.navbar-toggle {
  display: none;
  background: none;
  border: none;
  cursor: pointer;
}

.navbar-toggle-icon {
  display: block;
  width: 25px;
  height: 3px;
  background-color: white;
  position: relative;
}

.navbar-toggle-icon::before,
.navbar-toggle-icon::after {
  content: '';
  position: absolute;
  width: 100%;
  height: 3px;
  background-color: white;
}

.navbar-toggle-icon::before {
  top: -8px;
}

.navbar-toggle-icon::after {
  bottom: -8px;
}

.navbar-menu {
  display: flex;
  gap: 1rem;
}

.navbar-item {
  color: white;
  text-decoration: none;
  padding: 0.5rem;
  border-radius: 4px;
  transition: background-color 0.3s ease;
}

.navbar-item:hover,
.navbar-item.router-link-active {
  background-color: #34495e;
}

.navbar-buttons {
  display: flex;
  gap: 1rem;
}

.navbar-button {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  padding: 0.5rem 1rem;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  transition: background-color 0.3s ease;
  text-decoration: none;
}

.home-button {
  background-color: #3498db;
  color: white;
}

.home-button:hover {
  background-color: #2980b9;
}

.logout-button {
  background-color: #e74c3c;
  color: white;
}

.logout-button:hover {
  background-color: #c0392b;
}

.navbar-icon {
  width: 24px;
  height: 24px;
}

@media (max-width: 768px) {
  .navbar-toggle {
    display: block;
  }

  .navbar-menu {
    display: none;
    flex-direction: column;
    position: absolute;
    top: 100%;
    left: 0;
    right: 0;
    background-color: #2c3e50;
    padding: 1rem;
  }

  .navbar-menu.is-active {
    display: flex;
  }

  .navbar-buttons {
    display: none;
  }

  .navbar-menu.is-active .navbar-buttons {
    display: flex;
    flex-direction: column;
    margin-top: 1rem;
  }
}
</style>