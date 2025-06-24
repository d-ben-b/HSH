<script setup>
import { ref, onMounted, onUnmounted, watch } from 'vue'

const props = defineProps({
  message: {
    type: String,
    required: true,
  },
  type: {
    type: String,
    default: 'success',
    validator: (value) => ['success', 'error', 'info', 'warning'].includes(value),
  },
  duration: {
    type: Number,
    default: 3000,
  },
  visible: {
    type: Boolean,
    default: false,
  },
})

const emit = defineEmits(['close'])

const isVisible = ref(props.visible)
let timer = null

function startTimer() {
  clearTimeout(timer)
  timer = setTimeout(() => {
    closeToast()
  }, props.duration)
}

function closeToast() {
  isVisible.value = false
  emit('close')
}

watch(
  () => props.visible,
  (newValue) => {
    isVisible.value = newValue
    if (newValue) {
      startTimer()
    }
  },
)

onMounted(() => {
  if (props.visible) {
    startTimer()
  }
})

onUnmounted(() => {
  clearTimeout(timer)
})
</script>

<template>
  <Transition name="toast">
    <div v-if="isVisible" class="toast" :class="type">
      <div class="toast-icon">
        <!-- Success Icon -->
        <svg
          v-if="type === 'success'"
          xmlns="http://www.w3.org/2000/svg"
          width="20"
          height="20"
          viewBox="0 0 24 24"
          fill="none"
          stroke="currentColor"
          stroke-width="2"
          stroke-linecap="round"
          stroke-linejoin="round"
        >
          <path d="M22 11.08V12a10 10 0 1 1-5.93-9.14"></path>
          <polyline points="22 4 12 14.01 9 11.01"></polyline>
        </svg>
        <!-- Error Icon -->
        <svg
          v-else-if="type === 'error'"
          xmlns="http://www.w3.org/2000/svg"
          width="20"
          height="20"
          viewBox="0 0 24 24"
          fill="none"
          stroke="currentColor"
          stroke-width="2"
          stroke-linecap="round"
          stroke-linejoin="round"
        >
          <circle cx="12" cy="12" r="10"></circle>
          <line x1="15" y1="9" x2="9" y2="15"></line>
          <line x1="9" y1="9" x2="15" y2="15"></line>
        </svg>
        <!-- Info Icon -->
        <svg
          v-else
          xmlns="http://www.w3.org/2000/svg"
          width="20"
          height="20"
          viewBox="0 0 24 24"
          fill="none"
          stroke="currentColor"
          stroke-width="2"
          stroke-linecap="round"
          stroke-linejoin="round"
        >
          <circle cx="12" cy="12" r="10"></circle>
          <line x1="12" y1="16" x2="12" y2="12"></line>
          <line x1="12" y1="8" x2="12.01" y2="8"></line>
        </svg>
      </div>
      <div class="toast-content">{{ message }}</div>
      <button class="toast-close" @click="closeToast">×</button>
    </div>
  </Transition>
</template>

<style scoped>
.toast {
  position: fixed;
  top: 20px;
  right: 20px;
  display: flex;
  align-items: center;
  padding: 12px 16px;
  border-radius: 12px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
  z-index: 9999;
  min-width: 280px;
  max-width: 400px;
}

.toast.success {
  background-color: rgba(44, 177, 115, 0.95);
  color: white;
  border-left: 4px solid #25965f;
}

.toast.error {
  background-color: rgba(243, 94, 62, 0.95);
  color: white;
  border-left: 4px solid #d84c37;
}

.toast.info {
  background-color: rgba(30, 78, 95, 0.95);
  color: white;
  border-left: 4px solid var(--color-deep-teal);
}

.toast.warning {
  background-color: rgba(249, 216, 108, 0.95);
  color: #333;
  border-left: 4px solid #e9c23d;
}

.toast-icon {
  margin-right: 12px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.toast-content {
  flex: 1;
  font-weight: 500;
}

.toast-close {
  background: none;
  border: none;
  font-size: 20px;
  color: currentColor;
  cursor: pointer;
  opacity: 0.7;
  transition: opacity 0.2s;
  padding: 0 0 0 10px;
}

.toast-close:hover {
  opacity: 1;
}

.toast-enter-active,
.toast-leave-active {
  transition: all 0.3s ease;
}

.toast-enter-from {
  transform: translateX(100%);
  opacity: 0;
}

.toast-leave-to {
  transform: translateX(100%);
  opacity: 0;
}
</style>
