<!-- App.vue -->
<template>
  <div class="min-h-screen bg-[#0B2C66] flex items-center justify-center text-white">
    <div class="w-full h-full max-w-2xl p-8 space-y-6 flex flex-col gap-20 items-between">
      <div class="flex flex-col gap-4">
        <img class="w-[50px]" src="./assets/message.png" alt="">
        <h1 class="text-2xl md:text-3xl font-semibold">Hi there!</h1>
        <h2 class="text-xl md:text-4xl font-bold">What would you like to know?</h2>
        <p class="text-gray-300">Use one of the most common prompts below or ask your own question</p>
      </div>

      <div class="mt-4 relative">
        <!-- Chat Input --> 
        <ChatInput v-model="userMessage" />

        <!-- Voice Recorder -->
        <VoiceRecorder @voiceInput="setVoiceInput" />

        <!-- Send button -->
        <button
          @click="sendToApi"
          class="w-[50px] h-[48px] absolute top-[1px] right-[1px] bg-[#1e4e9b] text-white font-semibold rounded-lg flex items-center justify-center transition hover:bg-[#1e4e9b]/90"
        >
          <span v-if="isLoading">Sending...</span>
          <img v-else class="w-[18px]" src="./assets/arrow.png" alt="">
        </button>
      </div>

      <!-- Output -->
      <ChatOutput :message="response" v-if="response" />

      <!-- Error -->
      <div v-if="error" class="text-red-400">{{ error }}</div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import axios from 'axios'
import ChatInput from './components/ChatInput.vue'
import ChatOutput from './components/ChatOutput.vue'
import VoiceRecorder from './components/VoiceRecorder.vue'

const userMessage = ref('')
const response = ref('')
const isLoading = ref(false)
const error = ref('')

const sendToApi = async () => {
  if(isLoading.value || !userMessage.value.trim()){
    return;
  }

  error.value = ''
  response.value = ''
  isLoading.value = true

  try {
    const res = await axios.post(`${import.meta.env.VITE_BASE_API}/chat`, {
      message: userMessage.value,
    })
    response.value = res.data.reply
  } catch (e) {
    error.value = 'Ошибка при обращении к API.'
  } finally {
    isLoading.value = false
  }
}

const setVoiceInput = (text) => {
  userMessage.value = text
}
</script>
