<template>
  <div class="login-container">
    <div class="background-animation">
      <div class="circle"></div>
      <div class="circle"></div>
      <div class="circle"></div>
    </div>
    <div class="login-content">
      <div class="login-form">
        <div class="logo-container">
          <img src="/project/workspace/src/icons/logoS.png" alt="Logo da empresa" class="logo">
        </div>
        <h1 class="login-title">Painel de Gestão</h1>
        <form @submit.prevent="handleLogin">
          <div class="form-group">
            <label for="username"></label>
            <input type="text" id="username" placeholder="Nome de Usuario" v-model="username" required>
          </div>
          <div class="form-group">
            <label for="password"></label>
            <input type="password" id="password" placeholder="Senha" v-model="password" required>
          </div>
          <button type="submit" class="login-button" :disabled="isLoading">
            {{ isLoading ? 'Carregando...' : 'Entrar' }}
          </button>
        </form>
        <p class="forgot-password">
          <a href="#" @click.prevent="handleForgotPassword">Esqueceu a senha?</a>
        </p>
        <div v-if="showAlert" class="alert" :class="{ 'alert-success': isSuccess, 'alert-error': !isSuccess }">
          {{ alertMessage }}
        </div>
      </div>
      <div class="login-info">
        <h2>Bem-vindo ao Painel de Gestão</h2>
        <p>Nosso painel oferece uma visão completa e ferramentas poderosas para gerenciar sua empresa de forma eficiente.</p>
        <h3>Funcionalidades principais:</h3>
        <ul>
          <li>Dashboard interativo com métricas em tempo real</li>
          <li>Gerenciamento de produtos e estoque</li>
          <li>Acompanhamento de pedidos e vendas</li>
          <li>Análise de desempenho e relatórios personalizados</li>
          <li>Gestão de atendimento ao cliente</li>
          <li>Controle financeiro e faturamento</li>
        </ul>
        <p>Com nossa plataforma, você terá todas as informações necessárias para tomar decisões estratégicas e impulsionar o crescimento do seu negócio.</p>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import { useRouter } from 'vue-router'
import { UserIcon, LockClosedIcon } from '@heroicons/vue/24/solid'
import { login } from '@/api/auth'
import { useAuthStore } from '@/stores/auth'

const router = useRouter()
const authStore = useAuthStore()

const username = ref('')
const password = ref('')
const error = ref('')
const isLoading = ref(false)

const handleLogin = async () => {
  error.value = ''
  isLoading.value = true

  try {
    const response = await login(username.value, password.value)
    authStore.setToken(response.token)
    authStore.setUser(response.user)
    router.push('/dashboard')
  } catch (err) {
    error.value = 'Credenciais inválidas. Por favor, tente novamente.'
  } finally {
    isLoading.value = false
  }
}
</script>

<style scoped>
.login-container {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 100vh;
  background-color: #f0f2f5;
  padding: 20px;
  position: relative;
  overflow: hidden;
}

.background-animation {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  z-index: -1;
}

.circle {
  position: absolute;
  border-radius: 50%;
  background: rgba(74, 144, 226, 0.1);
  animation: move 15s infinite;
}

.circle:nth-child(1) {
  width: 200px;
  height: 200px;
  left: -100px;
  top: -100px;
}

.circle:nth-child(2) {
  width: 300px;
  height: 300px;
  right: -150px;
  bottom: -150px;
  animation-delay: 5s;
}

.circle:nth-child(3) {
  width: 150px;
  height: 150px;
  right: 30%;
  top: 30%;
  animation-delay: 10s;
}

@keyframes move {
  0% {
    transform: translate(0, 0);
  }
  50% {
    transform: translate(100px, 100px);
  }
  100% {
    transform: translate(0, 0);
  }
}

.login-content {
  display: flex;
  max-width: 1000px;
  background-color: #ffffff;
  border-radius: 8px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  overflow: hidden;
  position: relative;
  z-index: 1;
}

.login-form {
  flex: 1;
  padding: 40px;
}

.logo-container {
  text-align: center;
  margin-bottom: 20px;
}

.logo {
  max-width: 150px;
  height: auto;
}

.login-info {
  flex: 1;
  padding: 40px;
  background-color: #4a90e2;
  color: #ffffff;
}

.login-title {
  font-size: 24px;
  font-weight: bold;
  margin-bottom: 20px;
  color: #333;
  text-align: center;
}

.form-group {
  margin-bottom: 20px;
}

label {
  display: block;
  margin-bottom: 5px;
  font-weight: bold;
  color: #555;
}

input {
  width: 100%;
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: 4px;
  font-size: 16px;
}

.login-button {
  width: 100%;
  padding: 12px;
  background-color: #4a90e2;
  color: #ffffff;
  border: none;
  border-radius: 4px;
  font-size: 16px;
  font-weight: bold;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.login-button:hover:not(:disabled) {
  background-color: #3a7bc8;
}

.login-button:disabled {
  background-color: #cccccc;
  cursor: not-allowed;
}

.forgot-password {
  text-align: center;
  margin-top: 15px;
}

.forgot-password a {
  color: #4a90e2;
  text-decoration: none;
}

.forgot-password a:hover {
  text-decoration: underline;
}

.login-info h2 {
  font-size: 22px;
  margin-bottom: 15px;
}

.login-info h3 {
  font-size: 18px;
  margin-top: 20px;
  margin-bottom: 10px;
}

.login-info p {
  margin-bottom: 15px;
  line-height: 1.5;
}

.login-info ul {
  list-style-type: disc;
  padding-left: 20px;
  margin-bottom: 15px;
}

.login-info li {
  margin-bottom: 8px;
}

.alert {
  padding: 10px;
  border-radius: 4px;
  margin-top: 20px;
  text-align: center;
  animation: fadeIn 0.3s ease-in-out;
}

.alert-success {
  background-color: #d4edda;
  color: #155724;
}

.alert-error {
  background-color: #f8d7da;
  color: #721c24;
}

@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translateY(-10px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

@media (max-width: 768px) {
  .login-content {
    flex-direction: column;
  }

  .login-form,
  .login-info {
    padding: 30px;
  }
}
</style>