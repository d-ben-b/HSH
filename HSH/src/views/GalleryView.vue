<script setup>
import { ref, onMounted } from 'vue'

const pastImages = ref([])
const isLoading = ref(false)

async function fetchPastImages() {
  // In a real app, this would be an API call to fetch previously uploaded images
  isLoading.value = true

  try {
    // Simulating API delay
    await new Promise((resolve) => setTimeout(resolve, 1000))

    // If there are no simulated past images and the array is empty, add some demo images
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
      ]
    }
  } catch (error) {
    console.error('獲取圖片失敗:', error)
  } finally {
    isLoading.value = false
  }
}

onMounted(() => {
  fetchPastImages()
})
</script>

<template>
  <div class="gallery-view-container">
    <h1>拍貼機選照</h1>

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

      <div v-else class="gallery-grid">
        <div v-for="image in pastImages" :key="image.id" class="gallery-item">
          <div class="gallery-image" :style="{ backgroundImage: `url(${image.url})` }"></div>
          <div class="gallery-details">
            <div class="gallery-name">{{ image.name }}</div>
            <div class="gallery-date">{{ image.date }}</div>
          </div>
        </div>
      </div>
    </div>
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
}

.gallery-item:hover {
  transform: translateY(-5px);
  box-shadow: 0 8px 20px rgba(14, 58, 69, 0.15);
}

.gallery-image {
  height: 180px;
  background-size: cover;
  background-position: center;
  border-bottom: 1px solid #f0f0f0;
}

.gallery-details {
  padding: 12px;
  background-color: white;
}

.gallery-name {
  font-weight: 500;
  margin-bottom: 4px;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.gallery-date {
  font-size: 0.8rem;
  color: #888;
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
