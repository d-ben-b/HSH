<script setup>
import { useRouter } from 'vue-router'

const router = useRouter()

function navigateTo(path) {
  router.push(path)
}

function logout() {
  // Add actual logout logic here if needed
  // For now, just navigate to home page
  router.push('/')
}
</script>

<template>
  <nav class="navbar">
    <div class="nav-logo">圖片管理器</div>
    <div class="nav-links">
      <button @click="navigateTo('/')" class="nav-button">首頁</button>
      <button @click="navigateTo('/gallery')" class="nav-button">圖庫</button>
      <button @click="logout()" class="nav-button logout">退出登錄</button>
    </div>
  </nav>
</template>

<style scoped>
.navbar {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 1rem 2rem;
  background: var(--color-deep-teal);
  color: white;
  border-radius: 12px;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.15);
  margin-bottom: 2rem;
  position: relative;
  overflow: hidden;
}

.navbar::after {
  content: '';
  position: absolute;
  top: -10px;
  right: -10px;
  width: 20px;
  height: 20px;
  border-radius: 50%;
  background-color: var(--color-yellow);
  opacity: 0.4;
  box-shadow: 0 0 10px 5px var(--color-yellow);
}

.navbar::before {
  content: '';
  position: absolute;
  bottom: -5px;
  left: 20%;
  width: 10px;
  height: 10px;
  border-radius: 50%;
  background-color: var(--color-yellow);
  opacity: 0.3;
  box-shadow: 0 0 10px 3px var(--color-yellow);
}

.nav-logo {
  font-size: 1.5rem;
  font-weight: 700;
  letter-spacing: -0.5px;
  color: var(--color-beige);
}

.nav-links {
  display: flex;
  gap: 1rem;
}

.nav-button {
  background: rgba(255, 255, 255, 0.1);
  color: var(--color-beige);
  border: none;
  padding: 0.6rem 1.2rem;
  border-radius: 20px;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s ease;
}

.nav-button:hover {
  background: var(--color-orange);
  transform: translateY(-2px);
}

.logout {
  background: rgba(225, 123, 79, 0.3);
}

.logout:hover {
  background: var(--color-orange);
}

@media (max-width: 768px) {
  .navbar {
    flex-direction: column;
    gap: 1rem;
    padding: 1rem;
  }

  .nav-links {
    width: 100%;
    justify-content: center;
  }
}

@media (max-width: 480px) {
  .nav-button {
    padding: 0.5rem 0.8rem;
    font-size: 0.9rem;
  }
}
</style>
