<template>
  <button
    @click="toggleRecognition"
    class="w-full flex justify-center items-center space-x-2 border border-gray-300 p-2 rounded hover:bg-gray-200"
  >
    <span>{{ isListening ? '🛑 Тоқтату' : '🎤 Бастау' }}</span>
    <span>{{ isListening ? 'Тыңдап тұр...' : 'Голоспен енгізу' }}</span>
  </button>
</template>

<script setup>
import { ref } from 'vue'

const emit = defineEmits(['voiceInput'])
const isListening = ref(false)
let recognition = null
let isManuallyStopped = false

const toggleRecognition = () => {
  if (isListening.value) {
    // Қолмен тоқтату
    isManuallyStopped = true
    recognition.stop()
    isListening.value = false
    recognition = null
    return
  }

  // Қайтадан жаңа экземпляр құру
  const SpeechRecognition = window.SpeechRecognition || window.webkitSpeechRecognition
  if (!SpeechRecognition) {
    alert('Браузеріңіз Web Speech API қолдамайды.')
    return
  }

  recognition = new SpeechRecognition()
  recognition.lang = 'ru-RU'
  recognition.interimResults = false
  recognition.maxAlternatives = 1

  // Мәтінді capture ету
  recognition.onresult = (event) => {
    const result = event.results[0][0].transcript
    emit('voiceInput', result)
  }

  recognition.onerror = (event) => {
    console.error('Speech error:', event.error)
  }

  recognition.onend = () => {
    if (!isManuallyStopped) {
      // Авто-тоқтағанда қайтадан бастаймыз
      recognition.start()
    }
  }

  // Бастау
  isManuallyStopped = false
  recognition.start()
  isListening.value = true
}
</script>
