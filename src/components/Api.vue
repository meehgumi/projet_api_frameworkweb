<script setup>
import { ref, onMounted } from 'vue'

const data = ref(null)
const loading = ref(true)

onMounted(async () => {
  const response = await fetch('https://ton-api.com/endpoint', {
    headers: {
      'Authorization': `Bearer ${import.meta.env.VITE_API_KEY}`
    }
  })
  data.value = await response.json()
  loading.value = false
})
</script>

<template>
  <div v-if="loading">Chargement...</div>
  <div v-else>
    {{ data }}
  </div>
</template>