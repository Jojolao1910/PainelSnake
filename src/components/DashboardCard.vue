<template>
  <div class="dashboard-card" @click="$emit('click')">
    <div class="dashboard-card-icon-wrapper">
      <div class="dashboard-card-icon">
        <component :is="iconComponent" />
      </div>
    </div>
    <div class="dashboard-card-content">
      <h2>{{ title }}</h2>
      <p>Adicinar Descrição</p>
    </div>
  </div>
</template>

<script setup>
import { computed } from 'vue';
import { 
  CubeIcon, 
  ClipboardDocumentListIcon, 
  ShoppingCartIcon, 
  UserGroupIcon 
} from '@heroicons/vue/24/outline';

const props = defineProps({
  title: {
    type: String,
    required: true
  },
  icon: {
    type: String,
    required: true
  }
});

const iconComponents = {
  CubeIcon,
  ClipboardDocumentListIcon,
  ShoppingCartIcon,
  UserGroupIcon
};

const iconComponent = computed(() => {
  return iconComponents[props.icon] || CubeIcon;
});
</script>

<style scoped>
.dashboard-card {
  background-color: #ffffff;
  border-radius: 8px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  padding: 20px;
  cursor: pointer;
  transition: transform 0.3s ease, box-shadow 0.3s ease;
  display: flex;
  flex-direction: column;
  align-items: center;
  text-align: center;
}

.dashboard-card:hover {
  transform: translateY(-5px);
  box-shadow: 0 6px 12px rgba(0, 0, 0, 0.15);
}

.dashboard-card-icon-wrapper {
  width: 100%;
  display: flex;
  justify-content: center;
  margin-bottom: 15px;
}

.dashboard-card-icon {
  background-color: #3b82f6;
  color: #ffffff;
  width: 60px;
  height: 60px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
}

.dashboard-card-icon svg {
  width: 30px;
  height: 30px;
}

.dashboard-card-content {
  width: 100%;
}

.dashboard-card-content h2 {
  font-size: 18px;
  font-weight: 600;
  color: #1f2937;
  margin-bottom: 5px;
}

.dashboard-card-content p {
  font-size: 14px;
  color: #6b7280;
}
</style>