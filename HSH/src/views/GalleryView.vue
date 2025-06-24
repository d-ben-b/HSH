<script setup>
import { ref, onMounted, computed } from 'vue'
import ToastNotification from '@/components/ToastNotification.vue'

const pastImages = ref([])
const isLoading = ref(false)
const selectedImages = ref([])
const showSelectionError = ref(false)
const errorMessage = ref('')
const showToast = ref(false)
const toastMessage = ref('')
const toastType = ref('success')

// 計算還需要選擇多少張照片
const remainingSelections = computed(() => {
  return 4 - selectedImages.value.length
})

// 檢查是否可以列印（是否已選擇 4 張照片）
const canPrint = computed(() => {
  return selectedImages.value.length === 4
})

async function fetchPastImages() {
  isLoading.value = true

  try {
    await new Promise((resolve) => setTimeout(resolve, 1000))

    if (pastImages.value.length === 0) {
      pastImages.value = [
        {
          id: '1',
          name: 'sample1.jpg',
          url: 'https://picsum.photos/id/237/500/500',
          date: '2023/10/15',
        },
        {
          id: '2',
          name: 'sample2.jpg',
          url: 'https://picsum.photos/id/238/500/500',
          date: '2023/10/16',
        },
        {
          id: '3',
          name: 'sample3.jpg',
          url: 'https://picsum.photos/id/239/500/500',
          date: '2023/10/17',
        },
        {
          id: '4',
          name: 'sample4.jpg',
          url: 'https://picsum.photos/id/240/500/500',
          date: '2023/10/18',
        },
        {
          id: '5',
          name: 'sample5.jpg',
          url: 'https://picsum.photos/id/241/500/500',
          date: '2023/10/19',
        },
        {
          id: '6',
          name: 'sample6.jpg',
          url: 'https://picsum.photos/id/242/500/500',
          date: '2023/10/20',
        },
        {
          id: '7',
          name: 'sample7.jpg',
          url: 'https://picsum.photos/id/243/500/500',
          date: '2023/10/21',
        },
        {
          id: '8',
          name: 'sample8.jpg',
          url: 'https://picsum.photos/id/244/500/500',
          date: '2023/10/22',
        },
      ]
    }
  } catch (error) {
    console.error('獲取圖片失敗:', error)
  } finally {
    isLoading.value = false
  }
}

function toggleImageSelection(image) {
  const index = selectedImages.value.findIndex((img) => img.id === image.id)

  if (index !== -1) {
    selectedImages.value.splice(index, 1)
    showSelectionError.value = false
    return
  }

  if (selectedImages.value.length >= 4) {
    errorMessage.value = '最多只能選擇 4 張照片！'
    showSelectionError.value = true
    setTimeout(() => {
      showSelectionError.value = false
    }, 3000)
    return
  }

  selectedImages.value.push(image)
}

function isImageSelected(image) {
  return selectedImages.value.some((img) => img.id === image.id)
}

function printSelectedImages() {
  if (selectedImages.value.length !== 4) {
    errorMessage.value = '請選擇剛好 4 張照片！'
    showSelectionError.value = true
    setTimeout(() => {
      showSelectionError.value = false
    }, 3000)
    return
  }

  console.log('列印照片：', selectedImages.value)

  // 顯示 Toast 成功通知
  toastMessage.value = '照片已送至拍貼機列印！'
  toastType.value = 'success'
  showToast.value = true
}

function closeToast() {
  showToast.value = false
}

onMounted(() => {
  fetchPastImages()
})
</script>

<template>
  <div class="gallery-view-container">
    <h1>拍貼機選照</h1>

    <div class="selection-guide" v-if="!showSelectionError && pastImages.length > 0">
      <div class="guide-icon" :class="{ complete: canPrint }">
        <svg
          v-if="!canPrint"
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
          <line x1="12" y1="8" x2="12" y2="16"></line>
          <line x1="8" y1="12" x2="16" y2="12"></line>
        </svg>
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
          <path d="M22 11.08V12a10 10 0 1 1-5.93-9.14"></path>
          <polyline points="22 4 12 14.01 9 11.01"></polyline>
        </svg>
      </div>
      <div class="guide-text">
        {{
          remainingSelections > 0
            ? `請再選擇 ${remainingSelections} 張照片`
            : '已選擇 4 張照片，可以列印了！'
        }}
      </div>
    </div>

    <div v-if="showSelectionError" class="error-message">
      <svg
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
        <line x1="12" y1="8" x2="12" y2="12"></line>
        <line x1="12" y1="16" x2="12.01" y2="16"></line>
      </svg>
      {{ errorMessage }}
    </div>

    <div class="gallery-container">
      <div v-if="isLoading" class="loading-indicator">
        <svg class="spinner" viewBox="0 0 50 50">
          <circle cx="25" cy="25" r="20" fill="none" stroke-width="5"></circle>
        </svg>
        <p>載入圖片中...</p>
      </div>

      <div v-else-if="pastImages.length === 0" class="empty-gallery">
        <svg
          xmlns="http://www.w3.org/2000/svg"
          width="64"
          height="64"
          viewBox="0 0 24 24"
          fill="none"
          stroke="currentColor"
          stroke-width="2"
          stroke-linecap="round"
          stroke-linejoin="round"
        >
          <rect x="3" y="3" width="18" height="18" rx="2" ry="2"></rect>
          <circle cx="8.5" cy="8.5" r="1.5"></circle>
          <polyline points="21 15 16 10 5 21"></polyline>
        </svg>
        <p>沒有找到圖片。上傳一些圖片以在此處查看！</p>
        <button @click="$router.push('/')" class="upload-now-btn">立即上傳圖片</button>
      </div>

      <div v-else>
        <div class="gallery-grid">
          <div
            v-for="image in pastImages"
            :key="image.id"
            class="gallery-item"
            :class="{ selected: isImageSelected(image) }"
            @click="toggleImageSelection(image)"
          >
            <div class="gallery-image" :style="{ backgroundImage: `url(${image.url})` }">
              <div class="selection-indicator" v-if="isImageSelected(image)">
                <svg
                  xmlns="http://www.w3.org/2000/svg"
                  width="24"
                  height="24"
                  viewBox="0 0 24 24"
                  fill="none"
                  stroke="currentColor"
                  stroke-width="2"
                  stroke-linecap="round"
                  stroke-linejoin="round"
                >
                  <polyline points="9 11 12 14 22 4"></polyline>
                  <path d="M21 12v7a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2h11"></path>
                </svg>
              </div>
            </div>
            <div class="gallery-details">
              <div class="gallery-name">{{ image.name }}</div>
              <div class="gallery-date">{{ image.date }}</div>
            </div>
          </div>
        </div>

        <div class="print-controls">
          <button
            @click="printSelectedImages"
            class="print-button"
            :class="{ enabled: canPrint }"
            :disabled="!canPrint"
          >
            <svg
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
              <polyline points="6 9 6 2 18 2 18 9"></polyline>
              <path
                d="M6 18H4a2 2 0 0 1-2-2v-5a2 2 0 0 1 2-2h16a2 2 0 0 1 2 2v5a2 2 0 0 1-2 2h-2"
              ></path>
              <rect x="6" y="14" width="12" height="8"></rect>
            </svg>
            拍貼機列印
          </button>
        </div>
      </div>
    </div>

    <!-- Toast 通知 -->
    <ToastNotification
      :message="toastMessage"
      :type="toastType"
      :visible="showToast"
      :duration="3000"
      @close="closeToast"
    />
  </div>
</template>

<style scoped>
.gallery-view-container {
  max-width: 1000px;
  margin: 0 auto;
  padding: 2rem;
  background-color: white;
  border-radius: 20px;
  box-shadow: 0 8px 30px rgba(14, 58, 69, 0.12);
  position: relative;
}

.gallery-view-container::before {
  content: '';
  position: absolute;
  top: 15px;
  right: 15px;
  width: 6px;
  height: 6px;
  border-radius: 50%;
  background-color: var(--color-yellow);
  box-shadow: 0 0 8px 4px rgba(249, 216, 108, 0.6);
}

.gallery-view-container::after {
  content: '';
  position: absolute;
  bottom: 20px;
  left: 25px;
  width: 4px;
  height: 4px;
  border-radius: 50%;
  background-color: var(--color-yellow);
  box-shadow: 0 0 6px 3px rgba(249, 216, 108, 0.5);
}

h1 {
  font-size: 2.5rem;
  font-weight: 700;
  margin-bottom: 2rem;
  text-align: center;
  background: linear-gradient(135deg, var(--color-teal), var(--color-deep-teal));
  -webkit-background-clip: text;
  background-clip: text;
  color: transparent;
  letter-spacing: -0.5px;
}

.gallery-container {
  background-color: #ffffff;
  border-radius: 20px;
  padding: 2rem;
  min-height: 400px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.03);
  border: 1px solid rgba(95, 138, 149, 0.2);
}

.gallery-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
  gap: 24px;
}

.gallery-item {
  border-radius: 12px;
  overflow: hidden;
  box-shadow: 0 4px 12px rgba(14, 58, 69, 0.08);
  transition:
    transform 0.3s ease,
    box-shadow 0.3s ease;
  border: 1px solid rgba(95, 138, 149, 0.1);
  cursor: pointer;
  position: relative;
}

.gallery-item:hover {
  transform: translateY(-5px);
  box-shadow: 0 8px 20px rgba(14, 58, 69, 0.15);
}

.gallery-item.selected {
  border: 3px solid var(--color-orange);
  transform: translateY(-5px);
  box-shadow: 0 8px 20px rgba(225, 123, 79, 0.25);
}

.gallery-image {
  height: 180px;
  background-size: cover;
  background-position: center;
  border-bottom: 1px solid #f0f0f0;
  position: relative;
}

.selection-indicator {
  position: absolute;
  top: 8px;
  right: 8px;
  width: 36px;
  height: 36px;
  border-radius: 50%;
  background-color: var(--color-orange);
  display: flex;
  align-items: center;
  justify-content: center;
  color: white;
  box-shadow: 0 2px 8px rgba(225, 123, 79, 0.6);
}

.selection-guide {
  display: flex;
  align-items: center;
  justify-content: center;
  margin-bottom: 1.5rem;
  padding: 0.8rem;
  border-radius: 12px;
  background-color: rgba(30, 78, 95, 0.05);
}

.guide-icon {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 32px;
  height: 32px;
  border-radius: 50%;
  background: var(--color-light-teal);
  color: white;
  margin-right: 12px;
}

.guide-icon.complete {
  background: var(--color-orange);
}

.guide-text {
  color: var(--color-teal);
  font-weight: 500;
}

.print-controls {
  display: flex;
  justify-content: center;
  margin-top: 2.5rem;
}

.print-button {
  display: flex;
  align-items: center;
  justify-content: center;
  background: linear-gradient(135deg, #aaa, #888);
  color: white;
  border: none;
  border-radius: 30px;
  padding: 14px 36px;
  font-size: 1.1rem;
  font-weight: 600;
  cursor: not-allowed;
  transition: all 0.3s ease;
  min-width: 220px;
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
}

.print-button svg {
  margin-right: 10px;
}

.print-button.enabled {
  background: linear-gradient(135deg, var(--color-teal), var(--color-deep-teal));
  cursor: pointer;
  box-shadow: 0 6px 15px rgba(14, 58, 69, 0.25);
}

.print-button.enabled:hover {
  background: linear-gradient(135deg, var(--color-orange), #eb9470);
  transform: translateY(-3px);
  box-shadow: 0 8px 25px rgba(225, 123, 79, 0.35);
}

.error-message,
.success-message {
  display: flex;
  align-items: center;
  justify-content: center;
  margin-bottom: 1.5rem;
  padding: 1rem;
  border-radius: 12px;
  font-weight: 500;
}

.error-message {
  background-color: rgba(243, 94, 62, 0.1);
  color: #f35e3e;
}

.success-message {
  background-color: rgba(50, 166, 130, 0.1);
  color: #32a682;
}

.error-message svg,
.success-message svg {
  margin-right: 8px;
}

.empty-gallery {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  height: 400px;
  color: #888;
  text-align: center;
}

.empty-gallery svg {
  opacity: 0.5;
  margin-bottom: 1.5rem;
}

.upload-now-btn {
  margin-top: 1.5rem;
  background: linear-gradient(135deg, var(--color-teal), var(--color-deep-teal));
  color: white;
  border: none;
  border-radius: 30px;
  padding: 12px 24px;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s ease;
  box-shadow: 0 4px 12px rgba(14, 58, 69, 0.2);
}

.upload-now-btn:hover {
  background: linear-gradient(135deg, var(--color-orange), #eb9470);
  transform: translateY(-2px);
  box-shadow: 0 6px 15px rgba(225, 123, 79, 0.3);
}

.loading-indicator {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  height: 400px;
}

.loading-indicator .spinner {
  width: 50px;
  height: 50px;
  margin-bottom: 1.5rem;
  animation: rotate 2s linear infinite;
}

.loading-indicator .spinner circle {
  stroke: var(--color-teal);
  stroke-dasharray: 150, 200;
  stroke-dashoffset: -10;
  animation: dash 1.5s ease-in-out infinite;
}

@keyframes rotate {
  100% {
    transform: rotate(360deg);
  }
}

@keyframes dash {
  0% {
    stroke-dasharray: 1, 200;
    stroke-dashoffset: 0;
  }
  50% {
    stroke-dasharray: 89, 200;
    stroke-dashoffset: -35;
  }
  100% {
    stroke-dasharray: 89, 200;
    stroke-dashoffset: -124;
  }
}

@media (max-width: 768px) {
  .gallery-view-container {
    padding: 1.5rem;
    margin: 0 1rem;
  }

  h1 {
    font-size: 2rem;
    margin-bottom: 1.5rem;
  }

  .gallery-grid {
    grid-template-columns: repeat(auto-fill, minmax(150px, 1fr));
    gap: 16px;
  }

  .gallery-image {
    height: 150px;
  }

  .gallery-container {
    padding: 1rem;
  }
}

@media (max-width: 480px) {
  h1 {
    font-size: 1.7rem;
  }

  .gallery-grid {
    grid-template-columns: repeat(auto-fill, minmax(120px, 1fr));
    gap: 12px;
  }

  .gallery-image {
    height: 120px;
  }
}
</style>
