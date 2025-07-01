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
  margin-bottom: 1.2rem;
}
.chat-message.user {
  flex-direction: row-reverse;
  justify-content: flex-end;
}
.chat-message.assistant {
  justify-content: flex-start;
}
.avatar {
  width: 40px;
  height: 40px;
  margin: 0 0.7rem;
  flex-shrink: 0;
}
.avatar img {
  width: 100%;
  height: 100%;
  border-radius: 50%;
  border: 2px solid #fff;
  box-shadow: 0 1px 4px rgba(0,0,0,0.08);
}
.bubble {
  word-break: break-word;
  overflow-wrap: anywhere;
  white-space: pre-wrap;
  max-width: 80%;
  min-width: 0;
  padding: 1rem 1.3rem;
  border-radius: 1.2rem;
  font-size: 1.05rem;
  line-height: 1.6;
  background: #fff;
  box-shadow: 0 1px 4px rgba(0,0,0,0.04);
  word-break: break-word;
  overflow-wrap: break-word;
  position: relative;
  transition: background 0.2s, color 0.2s;
  border: 1px solid #ececec;
}
.chat-message.user .bubble {
  background: #19c37d;
  color: #fff;
  border-bottom-right-radius: 0.3rem;
  border-bottom-left-radius: 1.2rem;
  border: none;
}
.chat-message.assistant .bubble {
  background: #f4f6fa;
  color: #222;
  border-bottom-left-radius: 0.3rem;
  border-bottom-right-radius: 1.2rem;
  border: 1px solid #ececec;
}
:deep(pre) {
  margin: 0;
  overflow-x: auto;
  border-radius: 0.6rem;
  background: #f6f8fa;
  max-width: 100%;
}

:deep(pre code.hljs) {
  display: block;
  white-space: pre;
  padding: 1rem;
  overflow-x: auto;
  font-size: 0.95rem;
  line-height: 1.5;
  max-width: 100%;
  box-sizing: border-box;
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
    max-width: 100%;
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
