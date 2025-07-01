<template>
  <header class="chat-header">
    <h1>ChatGPT UI</h1>
  </header>
</template>

<script setup>
</script>

<style scoped>
.chat-header {
  background: rgba(255,255,255,0.22);
  border-bottom: 1.5px solid rgba(255,255,255,0.28);
  padding: 1.3rem 2.2rem 1.1rem 2.2rem;
  text-align: center;
  font-weight: 700;
  font-size: 1.45rem;
  letter-spacing: 1.2px;
  box-shadow: 0 2px 16px rgba(52,53,65,0.08);
  border-radius: 2rem 2rem 0 0;
  position: sticky;
  top: 0;
  z-index: 20;
  backdrop-filter: blur(14px) saturate(160%);
  -webkit-backdrop-filter: blur(14px) saturate(160%);
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