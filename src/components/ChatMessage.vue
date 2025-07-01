<template>
  <div :class="['chat-message', message.role]">
    <div class="avatar">
      <img :src="message.role === 'user' ? userAvatar : assistantAvatar" :alt="message.role" />
    </div>
    <div class="bubble">
      <span v-if="message.role === 'user'">{{ message.content }}</span>
      <span v-else v-html="renderedMarkdown"></span>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, watch, nextTick } from 'vue'
import { marked } from 'marked'
import hljs from 'highlight.js/lib/core'

// Only import languages you need
import javascript from 'highlight.js/lib/languages/javascript'
import python from 'highlight.js/lib/languages/python'
hljs.registerLanguage('javascript', javascript)
hljs.registerLanguage('python', python)

import 'highlight.js/styles/github.css' // You can change to vs2015, atom-one-dark, etc.

const props = defineProps({
  message: {
    type: Object,
    required: true
  }
})

const userAvatar = 'https://ui-avatars.com/api/?name=U&background=19c37d&color=fff&rounded=true&size=48'
const assistantAvatar = 'https://ui-avatars.com/api/?name=G&background=343541&color=fff&rounded=true&size=48'

const renderedMarkdown = ref('')

function renderMarkdown(text) {
  marked.setOptions({
    highlight: (code, lang) => {
      if (lang && hljs.getLanguage(lang)) {
        return hljs.highlight(code, { language: lang }).value
      }
      return hljs.highlightAuto(code).value
    },
    langPrefix: 'hljs language-'
  })
  return marked.parse(text)
}

watch(
  () => props.message.content,
  async (newVal) => {
    if (props.message.role === 'assistant') {
      renderedMarkdown.value = renderMarkdown(newVal)
      await nextTick()
      attachCopyButtons()
    }
  },
  { immediate: true }
)

onMounted(() => {
  if (props.message.role === 'assistant') {
    renderedMarkdown.value = renderMarkdown(props.message.content)
    nextTick(() => attachCopyButtons())
  }
})

function attachCopyButtons() {
  const blocks = document.querySelectorAll('pre code')
  blocks.forEach((block) => {
    if (block.parentElement.querySelector('.copy-btn')) return
    const btn = document.createElement('button')
    btn.textContent = '📋'
    btn.className = 'copy-btn'
    btn.onclick = () => {
      navigator.clipboard.writeText(block.textContent)
      btn.textContent = '✅'
      setTimeout(() => (btn.textContent = '📋'), 1000)
    }
    block.parentElement.style.position = 'relative'
    block.parentElement.appendChild(btn)
  })
}
</script>

<style scoped>
.chat-message {
  display: flex;
  align-items: flex-end;
  margin-bottom: 1.4rem;
  transition: all 0.2s cubic-bezier(.4,2,.6,1);
}
.chat-message.user {
  flex-direction: row-reverse;
}
.avatar {
  width: 44px;
  height: 44px;
  margin: 0 0.9rem;
  flex-shrink: 0;
}
.avatar img {
  width: 100%;
  height: 100%;
  border-radius: 50%;
  border: 2px solid #fff;
  box-shadow: 0 2px 8px rgba(0,0,0,0.10);
}
.bubble {
  max-width: 75vw;
  padding: 1.1rem 1.5rem;
  border-radius: 1.5rem;
  font-size: 1.08rem;
  line-height: 1.7;
  background: rgba(255,255,255,0.22);
  box-shadow: 0 4px 24px 0 rgba(31,38,135,0.10);
  word-break: break-word;
  position: relative;
  transition: background 0.2s, color 0.2s;
  backdrop-filter: blur(12px) saturate(160%);
  -webkit-backdrop-filter: blur(12px) saturate(160%);
  border: 1.2px solid rgba(255,255,255,0.28);
}
.chat-message.user .bubble {
  background: rgba(127,156,245,0.22);
  color: #fff;
  border-bottom-right-radius: 0.5rem;
  border-bottom-left-radius: 1.5rem;
  box-shadow: 0 2px 12px rgba(127,156,245,0.10);
  border: 1.2px solid rgba(127,156,245,0.28);
}
.chat-message.assistant .bubble {
  background: rgba(255,255,255,0.22);
  color: #222;
  border-bottom-left-radius: 0.5rem;
  border-bottom-right-radius: 1.5rem;
  box-shadow: 0 2px 12px rgba(52,53,65,0.06);
  border: 1.2px solid rgba(255,255,255,0.28);
}
:deep(pre code.hljs) {
  display: block;
  overflow-x: auto;
  padding: 1em;
  border-radius: 0.7rem;
  font-size: 0.98rem;
  background: rgba(255,255,255,0.18);
}
.copy-btn {
  position: absolute;
  top: 8px;
  right: 8px;
  background: #eee;
  border: none;
  border-radius: 5px;
  font-size: 0.8rem;
  padding: 2px 6px;
  cursor: pointer;
  box-shadow: 0 1px 4px rgba(0,0,0,0.04);
}
.copy-btn:hover {
  background: #ccc;
}
@media (max-width: 800px) {
  .bubble {
    max-width: 95vw;
    font-size: 0.97rem;
    padding: 0.9rem 1.1rem;
  }
  .avatar {
    width: 32px;
    height: 32px;
    margin: 0 0.5rem;
  }
  .avatar img {
    width: 32px;
    height: 32px;
  }
}
</style>
