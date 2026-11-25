<script setup>
import { watch, ref } from 'vue'

const props = defineProps({
  message: String,
  duration: { type: Number, default: 3000 }
})

const emit = defineEmits(['close'])
const isVisible = ref(false)

watch(() => props.message, (newVal) => {
  if (newVal) {
    isVisible.value = true
    setTimeout(() => {
      isVisible.value = false
      emit('close')
    }, props.duration)
  }
})
</script>

<template>
  <transition name="toast">
    <div v-if="isVisible" class="toast-notification">
      <span class="icon">✓</span>
      {{ message }}
    </div>
  </transition>
</template>

<style scoped>
.toast-notification {
  position: fixed;
  top: 20px;
  right: 20px;
  background: #4CAF50;
  color: white;
  padding: 14px 24px;
  border-radius: 8px;
  box-shadow: 0 4px 12px rgba(0,0,0,0.15);
  display: flex;
  align-items: center;
  gap: 10px;
  font-weight: 500;
  z-index: 1000;
}

.icon {
  font-weight: bold;
  font-size: 18px;
}

.toast-enter-active {
  transition: all 0.3s ease;
}

.toast-leave-active {
  transition: all 0.3s ease;
}

.toast-enter-from {
  opacity: 0;
  transform: translateY(-20px);
}

.toast-leave-to {
  opacity: 0;
  transform: translateY(-20px);
}

@media (max-width: 480px) {
  .toast-notification {
    top: auto;
    bottom: 20px;
    right: 20px;
    left: 20px;
    justify-content: center;
  }
}
</style>