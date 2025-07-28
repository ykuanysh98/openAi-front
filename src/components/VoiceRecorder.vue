<!-- VoiceRecorder.vue -->
<template>
  <div class="space-y-2 absolute mt-0 top-[12px] left-[10px] cursor-pointer z-10" @click="isRecording ? stopRecording() : startRecording()" >
    {{ isRecording ? '🛑' : '🎙️' }}
  </div>
</template>


<script setup>
import { ref } from 'vue'
import axios from 'axios'

const audioBlob2 = ref()
const isRecording = ref(false)
const mediaRecorder = ref(null)
const audioChunks = ref([])
const transcript = ref('')

const emit = defineEmits(['voiceInput']) // 👈

let stream = null

const startRecording = async () => {
  stream = await navigator.mediaDevices.getUserMedia({ audio: true })
  mediaRecorder.value = new MediaRecorder(stream)
  audioChunks.value = []

  mediaRecorder.value.ondataavailable = (event) => {
    if (event.data.size > 0) {
      audioChunks.value.push(event.data)
    }
  }

  mediaRecorder.value.onstop = async () => {
    const audioBlob = new Blob(audioChunks.value, { type: 'audio/webm' })
    audioBlob2.value = URL.createObjectURL(audioBlob) // 👈 string URL жасау
    
    const formData = new FormData()
    formData.append('audio', audioBlob, 'recording.webm')

    try {
      const response = await axios.post(`${import.meta.env.VITE_BASE_API}/transcribe`, formData, {
        headers: { 'Content-Type': 'multipart/form-data' },
      })
      transcript.value = response.data.text
      emit('voiceInput', transcript.value) // 👈 ата-анаға жіберу
    } catch (error) {
      console.error('Ошибка транскрипции:', error)
    }

    stream.getTracks().forEach(track => track.stop())
  }

  mediaRecorder.value.start()
  isRecording.value = true
}

const stopRecording = () => {
  if (mediaRecorder.value && mediaRecorder.value.state !== 'inactive') {
    mediaRecorder.value.stop()
    isRecording.value = false
  }
}
</script>
