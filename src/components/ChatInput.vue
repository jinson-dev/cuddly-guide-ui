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
  padding: 1rem 1.5rem;
  background: #fff;
  border-top: 1px solid #ececec;
}
.chat-input input {
  flex: 1;
  padding: 0.7rem 1rem;
  border: 1px solid #ddd;
  border-radius: 1.2rem;
  font-size: 1rem;
  outline: none;
  margin-right: 0.7rem;
  background: #f7f7f8;
  color: #222;
}
.chat-input button {
  padding: 0.7rem 1.5rem;
  border: none;
  border-radius: 1.2rem;
  background: #19c37d;
  color: #fff;
  font-weight: 600;
  font-size: 1rem;
  cursor: pointer;
  transition: background 0.2s;
}
.chat-input button:hover {
  background: #13a06b;
}
</style> 