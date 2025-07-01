<template>
  <div :class="['chat-message', message.role]">
    <div class="avatar">
      <img :src="message.role === 'user' ? userAvatar : assistantAvatar" :alt="message.role" />
    </div>
    <div class="bubble">
      <span>{{ message.content }}</span>
    </div>
  </div>
</template>

<script setup>
defineProps({
  message: {
    type: Object,
    required: true
  }
})

const userAvatar = 'https://ui-avatars.com/api/?name=U&background=19c37d&color=fff&rounded=true&size=48'
const assistantAvatar = 'https://ui-avatars.com/api/?name=G&background=343541&color=fff&rounded=true&size=48'
</script>

<style scoped>
.chat-message {
  display: flex;
  align-items: flex-end;
  margin-bottom: 1.2rem;
}
.chat-message.user {
  flex-direction: row-reverse;
}
.avatar {
  width: 40px;
  height: 40px;
  margin: 0 0.7rem;
  display: flex;
  align-items: center;
  justify-content: center;
}
.avatar img {
  width: 40px;
  height: 40px;
  border-radius: 50%;
  border: 2px solid #fff;
  box-shadow: 0 1px 4px rgba(0,0,0,0.08);
}
.bubble {
  max-width: 70vw;
  padding: 1rem 1.3rem;
  border-radius: 1.2rem;
  font-size: 1.05rem;
  line-height: 1.6;
  background: #fff;
  box-shadow: 0 1px 4px rgba(0,0,0,0.04);
  word-break: break-word;
  position: relative;
}
.chat-message.user .bubble {
  background: #19c37d;
  color: #fff;
  border-bottom-right-radius: 0.3rem;
  border-bottom-left-radius: 1.2rem;
}
.chat-message.assistant .bubble {
  background: #f0f0f0;
  color: #222;
  border-bottom-left-radius: 0.3rem;
  border-bottom-right-radius: 1.2rem;
}
@media (max-width: 800px) {
  .bubble { max-width: 90vw; font-size: 0.95rem; }
  .avatar { width: 32px; height: 32px; }
  .avatar img { width: 32px; height: 32px; }
}
</style> 