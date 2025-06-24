<script setup>
import { ref, computed } from 'vue'

const files = ref([])
const isDragging = ref(false)
const uploadStatus = ref('idle') // 'idle', 'uploading', 'success', 'error'
const errorMessage = ref('')

const previews = computed(() => {
  return files.value.map((file) => ({
    name: file.name,
    url: URL.createObjectURL(file),
  }))
})

function handleFileSelect(event) {
  const selectedFiles = Array.from(event.target.files)
  addFiles(selectedFiles)
}

function handleDrop(event) {
  event.preventDefault()
  isDragging.value = false

  if (event.dataTransfer.files) {
    const droppedFiles = Array.from(event.dataTransfer.files)
    addFiles(droppedFiles)
  }
}

function addFiles(newFiles) {
  // Filter for only image files
  const imageFiles = newFiles.filter((file) => file.type.startsWith('image/'))
  files.value = [...files.value, ...imageFiles]
}

function handleDragOver(event) {
  event.preventDefault()
  isDragging.value = true
}

function handleDragLeave() {
  isDragging.value = false
}

function removeFile(index) {
  const newFiles = [...files.value]
  newFiles.splice(index, 1)
  files.value = newFiles
}

async function uploadFiles() {
  if (files.value.length === 0) return

  uploadStatus.value = 'uploading'

  try {
    // Simulated upload - replace with actual upload logic
    await new Promise((resolve) => setTimeout(resolve, 1500))
    uploadStatus.value = 'success'

    // Clear files after successful upload
    setTimeout(() => {
      files.value = []
      uploadStatus.value = 'idle'
    }, 2000)
  } catch (error) {
    uploadStatus.value = 'error'
    errorMessage.value = error.message || 'Upload failed'
  }
}
</script>

<template>
  <div class="home-container">
    <h1>Upload Images</h1>

    <div
      class="upload-area"
      :class="{ dragging: isDragging, 'has-files': files.length > 0 }"
      @dragover="handleDragOver"
      @dragleave="handleDragLeave"
      @drop="handleDrop"
    >
      <div class="upload-prompt" v-if="files.length === 0">
        <div class="upload-icon">
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
            <path d="M21 15v4a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2v-4"></path>
            <polyline points="17 8 12 3 7 8"></polyline>
            <line x1="12" y1="3" x2="12" y2="15"></line>
          </svg>
        </div>
        <p>
          Drag images here or <label for="file-input" class="browse-link">browse</label> to upload
        </p>
        <input
          type="file"
          id="file-input"
          multiple
          accept="image/*"
          @change="handleFileSelect"
          class="file-input"
        />
      </div>

      <div v-else class="preview-container">
        <div v-for="(preview, index) in previews" :key="index" class="preview-item">
          <div class="preview-image" :style="{ backgroundImage: `url(${preview.url})` }">
            <button class="remove-button" @click.stop="removeFile(index)">×</button>
          </div>
          <div class="file-name">{{ preview.name }}</div>
        </div>

        <label class="add-more" for="file-input">
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
            <circle cx="12" cy="12" r="10"></circle>
            <line x1="12" y1="8" x2="12" y2="16"></line>
            <line x1="8" y1="12" x2="16" y2="12"></line>
          </svg>
          Add More
        </label>
      </div>
    </div>

    <div class="upload-controls" v-if="files.length > 0">
      <button
        @click="uploadFiles"
        :disabled="uploadStatus === 'uploading'"
        class="upload-button"
        :class="uploadStatus"
      >
        <span v-if="uploadStatus === 'idle'"
          >Upload {{ files.length }} {{ files.length > 1 ? 'Images' : 'Image' }}</span
        >
        <span v-else-if="uploadStatus === 'uploading'">
          <svg class="spinner" viewBox="0 0 50 50">
            <circle cx="25" cy="25" r="20" fill="none" stroke-width="5"></circle>
          </svg>
          Uploading...
        </span>
        <span v-else-if="uploadStatus === 'success'">Upload Successful!</span>
        <span v-else-if="uploadStatus === 'error'">{{ errorMessage }}</span>
      </button>
    </div>
  </div>
</template>

<style scoped>
.home-container {
  max-width: 1000px;
  margin: 0 auto;
  padding: 2rem;
  background-color: #fafafa;
  border-radius: 20px;
  box-shadow: 0 8px 30px rgba(0, 0, 0, 0.06);
}

h1 {
  font-size: 2.5rem;
  font-weight: 700;
  margin-bottom: 2rem;
  text-align: center;
  background: linear-gradient(135deg, #4a8eff, #8063e1);
  -webkit-background-clip: text;
  background-clip: text;
  color: transparent;
  letter-spacing: -0.5px;
}

p {
  color: #555;
  font-size: 1.1rem;
}

.upload-area {
  border: 2px dashed #d0d0d0;
  border-radius: 20px;
  padding: 3rem;
  text-align: center;
  background-color: #ffffff;
  transition: all 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275);
  cursor: pointer;
  margin-bottom: 2rem;
  min-height: 200px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.03);
}

.upload-area.dragging {
  background-color: #f0f7ff;
  border-color: #4a8eff;
  transform: scale(1.02);
  box-shadow: 0 12px 40px rgba(74, 142, 255, 0.15);
}

.upload-area.has-files {
  background-color: #fff;
  border-style: solid;
  border-color: #e8e8e8;
}

.upload-prompt {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}

.upload-icon {
  color: #8e8e8e;
  margin-bottom: 1.2rem;
  transition: all 0.4s ease;
}

.upload-area:hover .upload-icon {
  color: #4a8eff;
  transform: translateY(-8px) scale(1.1);
}

.browse-link {
  color: #4a8eff;
  text-decoration: underline;
  cursor: pointer;
  font-weight: 500;
  transition: all 0.2s ease;
}

.browse-link:hover {
  color: #3a7eef;
  text-decoration: none;
}

.file-input {
  display: none;
}

.preview-container {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(160px, 1fr));
  gap: 20px;
}

.preview-item {
  position: relative;
  overflow: hidden;
  border-radius: 12px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.08);
  transition: all 0.35s cubic-bezier(0.165, 0.84, 0.44, 1);
}

.preview-item:hover {
  transform: translateY(-4px);
  box-shadow: 0 8px 16px rgba(0, 0, 0, 0.12);
}

.preview-image {
  width: 100%;
  height: 160px;
  background-size: cover;
  background-position: center;
  position: relative;
}

.remove-button {
  position: absolute;
  top: 8px;
  right: 8px;
  width: 28px;
  height: 28px;
  border-radius: 50%;
  background-color: rgba(255, 255, 255, 0.9);
  border: none;
  color: #ff4757;
  font-size: 18px;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.25s ease;
  opacity: 0;
  transform: scale(0.8);
}

.preview-item:hover .remove-button {
  opacity: 1;
  transform: scale(1);
}

.remove-button:hover {
  background-color: #ff4757;
  color: white;
  transform: scale(1.1);
}

.file-name {
  padding: 12px;
  font-size: 0.85rem;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
  background-color: white;
  font-weight: 500;
  color: #444;
  border-top: 1px solid #f0f0f0;
}

.add-more {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  border: 2px dashed #e0e0e0;
  border-radius: 12px;
  height: 160px;
  cursor: pointer;
  color: #555;
  transition: all 0.3s ease;
  font-weight: 500;
}

.add-more:hover {
  border-color: #4a8eff;
  color: #4a8eff;
  background-color: #f5f9ff;
  transform: translateY(-2px);
}

.add-more svg {
  margin-bottom: 10px;
  transition: all 0.3s ease;
}

.add-more:hover svg {
  transform: rotate(90deg) scale(1.1);
}

.upload-controls {
  display: flex;
  justify-content: center;
  margin-top: 2.5rem;
}

.upload-button {
  background: linear-gradient(135deg, #4a8eff, #6a72e4);
  color: white;
  border: none;
  border-radius: 30px;
  padding: 14px 36px;
  font-size: 1.1rem;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s ease;
  display: flex;
  align-items: center;
  justify-content: center;
  min-width: 220px;
  box-shadow: 0 6px 15px rgba(74, 142, 255, 0.25);
}

.upload-button:hover:not(:disabled) {
  background: linear-gradient(135deg, #3a7eef, #5a62d4);
  transform: translateY(-3px);
  box-shadow: 0 8px 25px rgba(74, 142, 255, 0.35);
}

.upload-button:active:not(:disabled) {
  transform: translateY(-1px);
  box-shadow: 0 4px 10px rgba(74, 142, 255, 0.3);
}

.upload-button:disabled {
  background: linear-gradient(135deg, #c5c5c5, #d5d5d5);
  cursor: not-allowed;
  box-shadow: none;
}

.upload-button.uploading {
  background: linear-gradient(135deg, #4a8eff, #6a72e4);
}

.upload-button.success {
  background: linear-gradient(135deg, #32c682, #4caf50);
  box-shadow: 0 6px 15px rgba(76, 175, 80, 0.25);
}

.upload-button.error {
  background: linear-gradient(135deg, #ff4757, #ff6b81);
  box-shadow: 0 6px 15px rgba(255, 71, 87, 0.25);
}

.spinner {
  animation: rotate 2s linear infinite;
  width: 22px;
  height: 22px;
  margin-right: 10px;
}

.spinner circle {
  stroke: white;
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
  .home-container {
    padding: 1.5rem;
    margin: 0 1rem;
  }

  .upload-area {
    padding: 2rem 1rem;
    border-radius: 16px;
  }

  h1 {
    font-size: 2rem;
    margin-bottom: 1.5rem;
  }

  .preview-container {
    grid-template-columns: repeat(auto-fill, minmax(130px, 1fr));
    gap: 15px;
  }

  .preview-image {
    height: 130px;
  }

  .add-more {
    height: 130px;
  }
}

@media (max-width: 480px) {
  h1 {
    font-size: 1.7rem;
  }

  .upload-button {
    font-size: 1rem;
    padding: 12px 24px;
    min-width: 180px;
  }
}
</style>
