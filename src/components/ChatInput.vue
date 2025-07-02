<template>
  <form class="chat-input" @submit.prevent="onSend">
    <input
      v-model="input"
      type="text"
      placeholder="Type your message..."
      @keydown.enter.exact.prevent="onSend"
      autocomplete="off"
    />
    <button type="submit">Send</button>
  </form>
</template>

<script setup>
import { ref } from 'vue'
const input = ref('')
const emit = defineEmits(['send'])

function onSend() {
  if (!input.value.trim()) return
  emit('send', input.value)
  input.value = ''
}
</script>

<style scoped>
.chat-input {
  display: flex;
  padding: 0.5rem 0.5rem 0.5rem 0.5rem;
  position: sticky;
  bottom: 0;
  z-index: 10;
}
.chat-input input {
  flex: 1;
  padding: 1rem 1.2rem;
  border: 1.5px solid rgba(33, 135, 219, 0.28);
  border-radius: 1.5rem;
  font-size: 1.08rem;
  outline: none;
  margin-right: 0.9rem;
  background: rgba(255,255,255,0.18);
  color: #222;
  box-shadow: 0 1px 4px rgba(52,53,65,0.04);
  transition: border 0.2s;
  backdrop-filter: blur(8px) saturate(160%);
  -webkit-backdrop-filter: blur(8px) saturate(160%);
}
.chat-input input:focus {
  border: 1.5px solid #7f9cf5;
}
.chat-input button {
  padding: 1rem 2rem;
  border: none;
  border-radius: 1.5rem;
  background: rgba(127,156,245,0.22);
  color: #3a4266;
  font-weight: 600;
  font-size: 1.08rem;
  cursor: pointer;
  transition: background 0.2s, box-shadow 0.2s;
  box-shadow: 0 2px 8px rgba(127,156,245,0.10);
  border: 1.2px solid rgba(127,156,245,0.28);
  backdrop-filter: blur(8px) saturate(160%);
  -webkit-backdrop-filter: blur(8px) saturate(160%);
}
.chat-input button:hover {
  background: rgba(127,156,245,0.32);
}
@media (max-width: 800px) {
  .chat-input {
    padding: 0.7rem 0.5rem 0.8rem 0.5rem;
    border-radius: 0;
  }
  .chat-input input {
    font-size: 1rem;
    padding: 0.8rem 1rem;
    margin-right: 0.5rem;
  }
  .chat-input button {
    font-size: 1rem;
    padding: 0.8rem 1.2rem;
  }
}
</style> 