<template>
  <div class="chatgpt-app">
    
    <main class="main-chat">
      <div class="chat-container">
        <ChatHeader />
        <div
          class="chat-messages"
          ref="messagesContainer"
          :class="{ 'electric-border': electric }"
        >
          <ChatMessage
            v-for="(msg, idx) in messages"
            :key="idx"
            :message="msg"
          />
          <div v-if="loading" class="loading-message">
            <span class="dot"></span><span class="dot"></span><span class="dot"></span>
          </div>
        </div>
        <ChatInput @send="handleSend" :disabled="loading" />
      </div>
    </main>
  </div>
</template>

<script setup>
import { ref, nextTick, watch ,reactive} from 'vue'
import { fetchEventSource } from '@microsoft/fetch-event-source'
import ChatHeader from './components/ChatHeader.vue'
import ChatMessage from './components/ChatMessage.vue'
import ChatInput from './components/ChatInput.vue'

const messages = ref([
  { role: 'assistant', content: 'Hello! How can I help you today?' }
])
const loading = ref(false)
const messagesContainer = ref(null)
const electric = ref(false)

async function handleSend(text) {
  if (!text.trim()) return
  messages.value.push({ role: 'user', content: text })
  loading.value = true

  // Trigger electric border animation
  electric.value = false
  await nextTick()
  electric.value = true
  setTimeout(() => { electric.value = false }, 1200) // duration matches animation

  // Push a placeholder message we'll fill during streaming
  const assistantMsg = reactive({ role: 'assistant', content: '' })
  messages.value.push(assistantMsg)

  try {
    await fetchEventSource('https://fictional-garbanzo-production-4e96.up.railway.app/prompt', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    'Accept': 'text/event-stream'
  },
  body: JSON.stringify({
    messages:  messages.value.slice(-5),
    model: 'deepseek-ai/DeepSeek-R1-Distill-Llama-70B-free'
  }),
  async onmessage(msg) {
    try {
      const clean = msg.data.trim().replace(/^data: /, '')
    if (clean === '[DONE]') {
      loading.value = false
      if (assistantMsg.content.trim() === '') {
        assistantMsg.content = '⚠️ (No response received)'
      }
      return
    }
    const json = JSON.parse(clean)
      const delta = json.choices?.[0]?.delta?.content || ''
      assistantMsg.content += delta
      await nextTick()
      scrollToBottom()
    } catch (err) {
      console.error('Invalid stream chunk', msg.data)
    }
  },
  onclose() {
    loading.value = false
  },
  onerror(err) {
    console.error('Stream error:', err)
    loading.value = false
    assistantMsg.content += '\n\n⚠️ Something went wrong.'
  }
})

  } catch (e) {
    console.error('Fatal error:', e)
    loading.value = false
    assistantMsg.content += '\n\n❌ Failed to reach server.'
  }
}
function scrollToBottom() {
  nextTick(() => {
    const el = messagesContainer.value
    el?.scrollTo({ top: el.scrollHeight, behavior: 'smooth' })
  })
}
watch(
  () => messages.value.length,
  () => {
    scrollToBottom()
  }
)
</script>


<style scoped>
.chatgpt-app {
  display: flex;
  height: 100vh;
  font-family: 'Courier New', Courier, monospace;
}

.main-chat {
  flex: 1;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: flex-start;
  background: transparent;
}

.chat-container {
  width: 100%;
  max-width: 500px;
  margin: 0 auto;
  display: flex;
  flex-direction: column;
  height: 100vh;
  box-shadow: 0 8px 32px 0 rgba(31,38,135,0.18);
  border-radius: 2rem;
  box-sizing: border-box;
  padding: 0 0 0.5rem 0;
  position: relative;
  transition: box-shadow 0.2s;
  border: 1.5px solid rgba(255,255,255,0.28);
}

.chat-messages {
  flex: 1;
  overflow-y: auto;
  padding: 2rem 1.2rem 1rem 1.2rem;
  background: lightgrey;
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
  scroll-behavior: smooth;
  transition: box-shadow 0.3s;
  /* Hide scrollbar for Chrome, Safari and Opera */
  scrollbar-width: none; /* Firefox */
  -ms-overflow-style: none;  /* IE and Edge */
}
.chat-messages::-webkit-scrollbar {
  display: none;
}

.loading-message {
  display: flex;
  align-items: center;
  justify-content: flex-start;
  margin: 0.5rem 0 1.5rem 0;
  height: 32px;
  gap: 0.3rem;
}
.dot {
  width: 10px;
  height: 10px;
  margin: 0 4px;
  background: #7f9cf5;
  border-radius: 50%;
  display: inline-block;
  animation: blink 1.4s infinite both;
  box-shadow: 0 2px 8px rgba(127,156,245,0.12);
}
.dot:nth-child(2) { animation-delay: 0.2s; }
.dot:nth-child(3) { animation-delay: 0.4s; }
@keyframes blink {
  0%, 80%, 100% { opacity: 0.2; }
  40% { opacity: 1; }
}

.electric-border {
  position: relative;
  z-index: 1;
  animation: electric-glow 1.2s linear;
  box-shadow: 0 0 16px 4px #00f0ff, 0 0 32px 8px #00f0ff inset;
}
@keyframes electric-glow {
  0% {
    box-shadow: 0 0 0 0 #00f0ff, 0 0 0 0 #00f0ff inset;
  }
  20% {
    box-shadow: 0 0 8px 2px #00f0ff, 0 0 16px 4px #00f0ff inset;
  }
  50% {
    box-shadow: 0 0 24px 6px #00f0ff, 0 0 48px 12px #00f0ff inset;
  }
  80% {
    box-shadow: 0 0 8px 2px #00f0ff, 0 0 16px 4px #00f0ff inset;
  }
  100% {
    box-shadow: 0 0 0 0 #00f0ff, 0 0 0 0 #00f0ff inset;
  }
}

@media (max-width: 800px) {
  .chat-container {
    max-width: 100vw;
    border-radius: 0;
    box-shadow: none;
    padding: 0;
    border-left: none;
    border-right: none;
  }
  .chat-messages {
    padding: 1.2rem 0.3rem 0.7rem 0.3rem;
  }
}
</style>
