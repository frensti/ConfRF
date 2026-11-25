<script setup>
import { ref, onMounted } from 'vue'
import FeedbackModal from './components/FeedbackModal.vue'
import ToastNotification from './components/ToastNotification.vue'

const isModalOpen = ref(false)
const toastMessage = ref('')
const isFeedbackSent = ref(false)

onMounted(() => {
  const savedFeedback = localStorage.getItem('event-feedback')
  if (savedFeedback) {
    isFeedbackSent.value = true
  }
})

const handleFeedbackSubmit = (data) => {
  localStorage.setItem('event-feedback', JSON.stringify(data))
  
  isModalOpen.value = false
  toastMessage.value = 'Спасибо за отзыв!'
  isFeedbackSent.value = true
}
</script>

<template>
  <div class="page-container">
    <div class="event-card">
      <h2 class="event-title">Конференция ИТ-2025</h2>
      <p class="event-info">Дата: 10 марта 2025</p>
      <p class="event-info">Статус: <strong class="status-finished">Завершено</strong></p>
      
      <div class="feedback-action">
        <div v-if="!isFeedbackSent">
          <button @click="isModalOpen = true" class="open-modal-btn">Оставить отзыв</button>
        </div>
        <div v-else class="feedback-sent-msg">
          ✓ Ваш отзыв отправлен
        </div>
      </div>
    </div>

    <FeedbackModal :is-open="isModalOpen" @close="isModalOpen = false" @submit="handleFeedbackSubmit" />
    <ToastNotification :message="toastMessage" @close="toastMessage = ''" />
  </div>
</template>

<style scoped>
.page-container {
  max-width: 600px;
  margin: 40px auto;
  padding: 0 20px;
  font-family: sans-serif;
}

.event-card {
  border: 1px solid #ddd;
  padding: 20px;
  border-radius: 12px;
  background: #fff;
}

.event-title {
  margin-top: 0;
}

.event-info {
  color: #555;
}

.status-finished {
  color: #4CAF50;
}

.open-modal-btn {
  background: #2196F3;
  color: white;
  border: none;
  padding: 10px 20px;
  border-radius: 8px;
  cursor: pointer;
  font-size: 16px;
  margin-top: 15px;
  transition: background 0.2s;
}

.open-modal-btn:hover {
  background: #1976D2;
}

.feedback-sent-msg {
  color: #4CAF50;
  font-weight: bold;
  margin-top: 15px;
}
</style>