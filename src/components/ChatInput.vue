<!-- ChatInput.vue -->
<template>
  <input
    :value="modelValue"
    @input="$emit('update:modelValue', $event.target.value)"
    placeholder="Ask whatever you want"
    class="h-[50px] bg-transparent border border-gray-700 rounded-lg pl-10 w-full focus:outline-none focus:ring-2 focus:ring-blue-500 transition"
    rows="4"
  />
</template>

<script setup>
defineProps(['modelValue'])
defineEmits(['update:modelValue'])
</script>
