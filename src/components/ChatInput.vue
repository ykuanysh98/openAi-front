<!-- ChatInput.vue -->
<template>
  <textarea
    :value="modelValue"
    @input="$emit('update:modelValue', $event.target.value)"
    placeholder="Ask whatever you want"
    class="h-[50px] bg-transparent border border-gray-700 rounded-lg pt-3 pr-10 pb-2 pl-10 w-full focus:outline-none focus:ring-2 focus:ring-blue-500 transition resize-none scrollbar-hide"
    :class="{
      'h-[50px]': !modelValue.length <= 60,
      'h-auto': modelValue.length > 60
    }"
    :rows="modelValue.length > 60 ? 5 : 2"
  />
</template>

<script setup>
defineProps(['modelValue'])
defineEmits(['update:modelValue'])
</script>

<style>
textarea::-webkit-scrollbar {
  display: none;
}
textarea {
  -ms-overflow-style: none; /* IE & Edge */
  scrollbar-width: none;    /* Firefox */
}
</style>