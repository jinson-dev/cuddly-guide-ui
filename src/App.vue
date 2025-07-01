<template>
  <div class="chatgpt-app">
    <aside class="sidebar">
      <div class="sidebar-header">
        <button class="new-chat">+ New Chat</button>
      </div>
      <div class="sidebar-history">
        <p class="sidebar-title">History</p>
        <ul>
          <li class="sidebar-item">Chat 1</li>
          <li class="sidebar-item">Chat 2</li>
        </ul>
      </div>
      <div class="sidebar-footer">
        <button class="settings">⚙️ Settings</button>
      </div>
    </aside>
    <main class="main-chat">
      <ChatHeader />
      <div class="chat-messages" ref="messagesContainer">
        <ChatMessage
          v-for="(msg, idx) in messages"
          :key="idx"
          :message="msg"
        />
        <div v-if="loading" class="loading-message">
          <span class="dot"></span><span class="dot"></span><span class="dot"></span>
        </div>
      </div>
      <ChatInput @send="handleSend" />
    </main>
  </div>
</template>

<script setup>
import { ref, nextTick, watch } from 'vue'
import axios from 'axios'
import ChatHeader from './components/ChatHeader.vue'
import ChatMessage from './components/ChatMessage.vue'
import ChatInput from './components/ChatInput.vue'

const messages = ref([
  { role: 'assistant', content: 'Hello! How can I help you today?' }
])
const loading = ref(false)
const messagesContainer = ref(null)

async function handleSend(text) {
  if (!text.trim()) return
  messages.value.push({ role: 'user', content: text })
  loading.value = true
  try {
    const res = await axios.post('https://fictional-garbanzo-production-4e96.up.railway.app/prompt', {
      prompt: text
    })
    messages.value.push({ role: 'assistant', content: res.data.choices[0].text || 'No response.' })
  } catch (e) {
    messages.value.push({ role: 'assistant', content: 'Error: Unable to get response from Together AI.' })
  } finally {
    loading.value = false
  }
}

watch(messages, async () => {
  await nextTick()
  if (messagesContainer.value) {
    messagesContainer.value.scrollTop = messagesContainer.value.scrollHeight
  }
})
</script>

<style scoped>
.chatgpt-app {
  display: flex;
  height: 100vh;
  background: #f7f7f8;
}
.sidebar {
  width: 260px;
  background: #202123;
  color: #fff;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  padding: 1rem 0;
}
.sidebar-header {
  padding: 0 1.5rem 1rem 1.5rem;
}
.new-chat {
  width: 100%;
  padding: 0.7rem 0;
  background: #343541;
  color: #fff;
  border: none;
  border-radius: 0.5rem;
  font-size: 1rem;
  cursor: pointer;
  margin-bottom: 1rem;
}
.sidebar-title {
  font-size: 0.95rem;
  color: #b4bcd0;
  margin: 0 0 0.5rem 0.5rem;
}
.sidebar-history {
  flex: 1;
  padding: 0 1.5rem;
}
.sidebar-item {
  padding: 0.5rem 0.7rem;
  border-radius: 0.4rem;
  margin-bottom: 0.3rem;
  cursor: pointer;
  color: #ececf1;
  transition: background 0.2s;
}
.sidebar-item:hover {
  background: #343541;
}
.sidebar-footer {
  padding: 1rem 1.5rem 0 1.5rem;
}
.settings {
  width: 100%;
  padding: 0.6rem 0;
  background: #343541;
  color: #fff;
  border: none;
  border-radius: 0.5rem;
  font-size: 1rem;
  cursor: pointer;
}
.main-chat {
  flex: 1;
  display: flex;
  flex-direction: column;
  background: #f7f7f8;
}
.chat-messages {
  flex: 1;
  overflow-y: auto;
  padding: 1.5rem;
  background: #f7f7f8;
  display: flex;
  flex-direction: column;
}
.loading-message {
  display: flex;
  align-items: center;
  margin: 0.5rem 0 1.5rem 0;
  height: 32px;
}
.dot {
  width: 8px;
  height: 8px;
  margin: 0 3px;
  background: #b4bcd0;
  border-radius: 50%;
  display: inline-block;
  animation: blink 1.4s infinite both;
}
.dot:nth-child(2) { animation-delay: 0.2s; }
.dot:nth-child(3) { animation-delay: 0.4s; }
@keyframes blink {
  0%, 80%, 100% { opacity: 0.2; }
  40% { opacity: 1; }
}
@media (max-width: 800px) {
  .sidebar { width: 60px; padding: 0; }
  .sidebar-header, .sidebar-history, .sidebar-footer { padding: 0.5rem; }
  .sidebar-title, .sidebar-item, .settings, .new-chat { font-size: 0.8rem; }
  .main-chat { padding: 0; }
}
</style>
