<script setup>
import { ref, onMounted } from 'vue'
import FeedbackModal from './components/FeedbackModal.vue'
import ToastNotification from './components/ToastNotification.vue'

// Имитация заявки, которую мы открыли (обычно id берется из URL, например /application/123)
const currentApplication = ref({
  id: 123,
  eventName: 'Конференция ИТ-2025',
  date: '10 марта 2025',
  status: 'finished' // Мероприятие завершено
})

const isModalOpen = ref(false)
const toastMessage = ref('')
const isFeedbackSent = ref(false)

// Проверяем, есть ли уже отзыв для этой заявки в "базе данных" (localStorage)
onMounted(() => {
  const savedFeedback = localStorage.getItem(`feedback_${currentApplication.value.id}`)
  if (savedFeedback) {
    isFeedbackSent.value = true
  }
})

// Функция сохранения отзыва
const handleFeedbackSubmit = (data) => {
  // Формируем объект отзыва, привязывая его к ID заявки
  const feedbackData = {
    applicationId: currentApplication.value.id,
    eventName: currentApplication.value.eventName,
    rating: data.rating,
    comment: data.comment,
    createdAt: new Date().toISOString()
  }

  // СОХРАНЕНИЕ В БАЗУ ДАННЫХ (имитация через localStorage)
  localStorage.setItem(`feedback_${currentApplication.value.id}`, JSON.stringify(feedbackData))
  
  // Обновляем интерфейс
  isModalOpen.value = false
  toastMessage.value = 'Спасибо за отзыв!'
  isFeedbackSent.value = true
}
</script>

<template>
  <div class="page-container">
    <div class="event-card">
      <h2 class="event-title">{{ currentApplication.eventName }}</h2>
      <p class="event-info">Дата: {{ currentApplication.date }}</p>
      <p class="event-info">
        Статус: 
        <strong v-if="currentApplication.status === 'finished'" class="status-finished">Завершено</strong>
      </p>
      
      <!-- Кнопка отзыва показывается ТОЛЬКО если мероприятие завершено -->
      <div v-if="currentApplication.status === 'finished'" class="feedback-action">
        
        <!-- Если отзыв еще не оставлен -->
        <div v-if="!isFeedbackSent">
          <button @click="isModalOpen = true" class="open-modal-btn">Оставить отзыв</button>
        </div>
        
        <!-- Если отзыв уже оставлен -->
        <div v-else class="feedback-sent-msg">
          ✓ Ваш отзыв отправлен
        </div>

      </div>
      <div v-else class="feedback-action">
        <p class="event-info">Отзыв можно будет оставить после завершения мероприятия.</p>
      </div>
    </div>

    <!-- Модальное окно -->
    <FeedbackModal 
      :is-open="isModalOpen" 
      @close="isModalOpen = false" 
      @submit="handleFeedbackSubmit" 
    />
    
    <!-- Всплывающее уведомление -->
    <ToastNotification 
      :message="toastMessage" 
      @close="toastMessage = ''" 
    />
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
  box-shadow: 0 4px 12px rgba(0,0,0,0.05);
}

.event-title {
  margin-top: 0;
  color: #333;
}

.event-info {
  color: #555;
  margin: 8px 0;
}

.status-finished {
  color: #4CAF50;
}

.feedback-action {
  margin-top: 20px;
  padding-top: 20px;
  border-top: 1px solid #eee;
}

.open-modal-btn {
  background: #2196F3;
  color: white;
  border: none;
  padding: 12px 24px;
  border-radius: 8px;
  cursor: pointer;
  font-size: 16px;
  transition: background 0.2s;
}

.open-modal-btn:hover {
  background: #1976D2;
}

.feedback-sent-msg {
  color: #4CAF50;
  font-weight: bold;
  font-size: 16px;
  display: flex;
  align-items: center;
  gap: 8px;
}
</style>