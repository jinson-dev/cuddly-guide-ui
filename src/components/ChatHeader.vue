<template>
  <header class="chat-header">
    <h1>Chat</h1>
  </header>
</template>

<script setup>
</script>

<style scoped>
.chat-header {
  text-align: center;
  font-weight: 500;
  font-size: 1rem;
  letter-spacing: 1.2px;
  border-radius: 2rem 2rem 0 0;
  position: sticky;
  top: 0;
  z-index: 20;
  color: #3a4266;
}
@media (max-width: 800px) {
  .chat-header {
    padding: 1rem 0.5rem 0.8rem 0.5rem;
    font-size: 1.1rem;
    border-radius: 0;
  }
}
</style> 