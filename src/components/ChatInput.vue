<template>
  <form class="chat-input" @submit.prevent="onSend">
    <textarea
      v-model="input"
      placeholder="Type your message..."
      @keydown.enter.exact.prevent="onSend"
      autocomplete="off"
      class="chat-textarea"
      rows="3"
      style="resize: none; overflow-y: auto;"
    />
    <button type="submit" :disabled="props.disabled">Send</button>
  </form>
</template>

<script setup>
import { ref } from 'vue'
const props = defineProps({
  disabled: {
    type: Boolean,
    default: false
  }
})
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
  align-items: center;
  justify-content: center;
  padding: 0.5rem 0.5rem 0.5rem 0.5rem;
  position: sticky;
  bottom: 0;
  z-index: 10;
}
.chat-textarea {
  flex: 1;
  padding: 1rem 1.2rem;
  border: 1.5px solid rgba(33, 135, 219, 0.28);
  border-radius: 0.5rem;
  font-size: 0.8rem;
  font-family: 'Courier New', Courier, monospace;
  outline: none;
  margin-right: 0.9rem;
  background: rgba(255,255,255,0.18);
  color: #222;
  box-shadow: 0 1px 4px rgba(52,53,65,0.04);
  transition: border 0.2s;
  backdrop-filter: blur(8px) saturate(160%);
  -webkit-backdrop-filter: blur(8px) saturate(160%);
  height: 60px;
  max-height: 120px;
  resize: none;
  overflow-y: auto;
  word-break: break-word;
  white-space: pre-wrap;
  padding-right: 2.2rem;
  /* Hide scrollbar for Chrome, Safari and Opera */
  scrollbar-width: none; /* Firefox */
  -ms-overflow-style: none;  /* IE and Edge */
  text-align: justify; /* Note: Most browsers do not support justify in textarea */
}
.chat-textarea:focus {
  border: 1.5px solid #7f9cf5;
}
.chat-input button {
  width: 64px;
  height: 40px;
  min-width: 64px;
  min-height: 40px;
  max-width: 64px;
  max-height: 40px;
  padding: 0;
  border: none;
  border-radius: 0.2rem;
  background: linear-gradient(135deg, #7f9cf5 0%, #19c37d 100%);
  color: #fff;
  font-weight: 500;
  font-size: 0.85rem;
  cursor: pointer;
  transition: background 0.18s, box-shadow 0.18s, transform 0.12s;
  box-shadow: 0 2px 8px rgba(31,38,135,0.10);
  border: 1.2px solid #7f9cf5;
  display: flex;
  align-items: center;
  justify-content: center;
  letter-spacing: 0.02em;
  outline: none;
}
.chat-input button:hover {
  background: linear-gradient(135deg, #19c37d 0%, #7f9cf5 100%);
  box-shadow: 0 4px 16px rgba(31,38,135,0.18);
  transform: translateY(-2px) scale(1.05);
}
.chat-input button:active {
  background: linear-gradient(135deg, #7f9cf5 0%, #19c37d 100%);
  box-shadow: 0 2px 8px rgba(31,38,135,0.10);
  transform: scale(0.98);
}
.chat-input button:disabled {
  background: #e0e0e0;
  color: #aaa;
  cursor: not-allowed;
  border: 1.2px solid #eee;
}
@media (max-width: 800px) {
  .chat-input {
    padding: 0.7rem 0.5rem 0.8rem 0.5rem;
    border-radius: 0;
  }
  .chat-textarea {
    font-size: 1rem;
    padding: 0.8rem 1rem;
    margin-right: 0.5rem;
  }
  .chat-input button {
    font-size: 1rem;
    padding: 0.8rem 1.2rem;
  }
}
/* Custom scrollbar for Webkit browsers */
.chat-textarea::-webkit-scrollbar {
  display: none;
}
.chat-textarea::-webkit-scrollbar-thumb {
  background: #c0c0c0;
  border-radius: 8px;
}
.chat-textarea::-webkit-scrollbar-track {
  background: transparent;
  border-radius: 8px;
}
</style> 