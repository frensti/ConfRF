<script setup>
import { ref, watch, onMounted, onUnmounted } from 'vue'

const props = defineProps({
  isOpen: Boolean
})

const emit = defineEmits(['close', 'submit'])

const rating = ref(0)
const comment = ref('')
const errors = ref({ rating: '', comment: '' })
const isSubmitting = ref(false)

watch(() => props.isOpen, (newVal) => {
  if (newVal) {
    rating.value = 0
    comment.value = ''
    errors.value = { rating: '', comment: '' }
    document.body.style.overflow = 'hidden'
  } else {
    document.body.style.overflow = ''
  }
})

const validateForm = () => {
  let isValid = true
  errors.value = { rating: '', comment: '' }

  if (rating.value === 0) {
    errors.value.rating = 'Пожалуйста, поставьте оценку'
    isValid = false
  }

  if (comment.value.length > 500) {
    errors.value.comment = 'Комментарий не должен превышать 500 символов'
    isValid = false
  }

  return isValid
}

const handleSubmit = () => {
  if (!validateForm()) return
  
  isSubmitting.value = true
  setTimeout(() => {
    emit('submit', { rating: rating.value, comment: comment.value })
    isSubmitting.value = false
  }, 500)
}

const closeOnOverlay = (e) => {
  if (e.target.classList.contains('modal-overlay')) {
    emit('close')
  }
}

const handleEsc = (e) => {
  if (e.key === 'Escape' && props.isOpen) {
    emit('close')
  }
}

onMounted(() => document.addEventListener('keydown', handleEsc))
onUnmounted(() => {
  document.removeEventListener('keydown', handleEsc)
  document.body.style.overflow = ''
})
</script>

<template>
  <teleport to="body">
    <transition name="fade">
      <div v-if="isOpen" class="modal-overlay" @click="closeOnOverlay">
        <div class="modal-content" @click.stop>
          
          <button class="close-btn" @click="emit('close')" title="Закрыть">✕</button>
          
          <h2 class="modal-title">Обратная связь</h2>
          <p class="subtitle">Поделитесь впечатлениями о мероприятии</p>

          <!-- Оценка -->
          <div class="form-group" :class="{ 'has-error-group': errors.rating }">
            <label class="field-label">Оценка мероприятия <span class="required">*</span></label>
            <div class="stars">
              <button 
                v-for="star in 5" 
                :key="star"
                type="button"
                @click="rating = star"
                :class="{ active: star <= rating }"
                class="star-btn"
              >★</button>
            </div>
            <span v-if="errors.rating" class="error-text">{{ errors.rating }}</span>
          </div>

          <!-- Комментарий -->
          <div class="form-group">
            <label class="field-label">Комментарий</label>
            <textarea 
              v-model="comment" 
              placeholder="Что вам понравилось? Что можно улучшить?"
              rows="4"
              :class="{ 'has-error': errors.comment }"
              class="comment-textarea"
            ></textarea>
            <div class="char-count" :class="{ 'limit': comment.length > 500 }">
              {{ comment.length }} / 500
            </div>
            <span v-if="errors.comment" class="error-text">{{ errors.comment }}</span>
          </div>

          <button class="submit-btn" @click="handleSubmit" :disabled="isSubmitting">
            {{ isSubmitting ? 'Отправка...' : 'Отправить отзыв' }}
          </button>

        </div>
      </div>
    </transition>
  </teleport>
</template>

<style scoped>
.modal-overlay {
  position: fixed;
  inset: 0;
  background: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 999;
  padding: 20px;
}

.modal-content {
  background: white;
  width: 100%;
  max-width: 450px;
  border-radius: 16px;
  padding: 32px;
  position: relative;
  max-height: 90vh;
  overflow-y: auto;
  box-shadow: 0 10px 30px rgba(0,0,0,0.2);
}

.close-btn {
  position: absolute;
  top: 16px;
  right: 16px;
  background: #f0f0f0;
  border: none;
  width: 32px;
  height: 32px;
  border-radius: 50%;
  font-size: 16px;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: background 0.2s;
}

.close-btn:hover {
  background: #e0e0e0;
}

.modal-title {
  margin: 0 0 8px 0;
  color: #333;
}

.subtitle {
  color: #777;
  font-size: 14px;
  margin-bottom: 24px;
}

.form-group {
  margin-bottom: 20px;
}

.field-label {
  display: block;
  font-weight: 500;
  margin-bottom: 8px;
  color: #444;
}

.required {
  color: #ff5252;
}

.stars {
  display: flex;
  gap: 8px;
}

.star-btn {
  background: none;
  border: none;
  font-size: 36px;
  color: #e0e0e0;
  cursor: pointer;
  padding: 0;
  line-height: 1;
  transition: color 0.2s, transform 0.1s;
}

.star-btn:hover {
  color: #FFC107;
}

.star-btn.active {
  color: #FFC107;
}

.star-btn:active {
  transform: scale(1.2);
}

.comment-textarea {
  width: 100%;
  padding: 12px;
  border: 1px solid #dcdcdc;
  border-radius: 8px;
  font-family: inherit;
  font-size: 15px;
  resize: vertical;
  box-sizing: border-box;
}

.comment-textarea:focus {
  outline: none;
  border-color: #4CAF50;
}

.comment-textarea.has-error {
  border-color: #ff5252;
  background: #fff5f5;
}

.has-error-group .field-label {
  color: #ff5252;
}

.char-count {
  text-align: right;
  font-size: 12px;
  color: #999;
  margin-top: 4px;
}

.char-count.limit {
  color: #ff5252;
  font-weight: bold;
}

.error-text {
  color: #ff5252;
  font-size: 13px;
  margin-top: 6px;
  display: block;
}

.submit-btn {
  width: 100%;
  padding: 14px;
  background: #4CAF50;
  color: white;
  border: none;
  border-radius: 8px;
  font-size: 16px;
  font-weight: 600;
  cursor: pointer;
  transition: background 0.2s;
}

.submit-btn:hover {
  background: #43a047;
}

.submit-btn:disabled {
  background: #a5d6a7;
  cursor: not-allowed;
}

.fade-enter-active {
  transition: opacity 0.3s ease;
}

.fade-leave-active {
  transition: opacity 0.3s ease;
}

.fade-enter-from {
  opacity: 0;
}

.fade-leave-to {
  opacity: 0;
}

@media (max-width: 480px) {
  .modal-overlay {
    padding: 0;
    align-items: flex-end;
  }

  .modal-content {
    max-width: 100%;
    border-radius: 20px 20px 0 0;
    padding: 24px;
    max-height: 85vh;
  }

  .star-btn {
    font-size: 42px;
    gap: 12px;
  }

  .comment-textarea {
    font-size: 16px;
    min-height: 120px;
  }

  .submit-btn {
    padding: 16px;
    font-size: 18px;
  }
}
</style>