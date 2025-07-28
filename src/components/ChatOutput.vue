<template>
  <div class="bg-gray-100 p-4 rounded border whitespace-pre-wrap">
    {{ message }}
  </div>
</template>

<script setup>
defineProps(['message'])
</script>
