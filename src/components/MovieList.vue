<script setup>
import { ref, onMounted } from 'vue'

const movies = ref([])
const loading = ref(true)

const API_KEY = import.meta.env.VITE_API_KEY

onMounted(async () => {
  try {
    const response = await fetch(
      `https://api.themoviedb.org/3/movie/popular?api_key=${API_KEY}&language=fr-FR`
    )
    const data = await response.json()
    movies.value = data.results
  } catch (error) {
    console.error('Erreur:', error)
  } finally {
    loading.value = false
  }
})
</script>

<template>
  <div class="container">
    <h1>Films Populaires</h1>
    
    <div v-if="loading" class="loader">
      <div class="spinner"></div>
    </div>
    
    <div v-else class="movie-list">
      <div v-for="movie in movies" :key="movie.id" class="movie-card">
        <div class="backdrop" :style="`background-image: url(https://image.tmdb.org/t/p/w780${movie.backdrop_path})`"></div>
        <div class="content">
          <div class="info">
            <h2>{{ movie.title }}</h2>
            <p class="overview">{{ movie.overview }}</p>
            <div class="meta">
              <span class="rating">{{ movie.vote_average.toFixed(1) }}</span>
              <span class="date">{{ movie.release_date }}</span>
            </div>
          </div>
          <img 
            :src="`https://image.tmdb.org/t/p/w300${movie.poster_path}`" 
            :alt="movie.title"
            class="poster"
          />
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.container {
  width: 100vw;
  max-width: 100%;
  padding: 40px 80px;
  background: #0a0a0a;
  min-height: 100vh;
  box-sizing: border-box;
}

.content {
  position: relative;
  display: flex;
  flex-direction: row-reverse;
  align-items: center;
  gap: 50px;
  padding: 40px 60px;
}

.poster {
  width: 220px;
  flex-shrink: 0;
  border-radius: 12px;
  box-shadow: 0 5px 20px rgba(0, 0, 0, 0.5);
}

.info {
  flex: 1;
  display: flex;
  flex-direction: column;
  justify-content: center;
}

.info h2 {
  margin: 0 0 20px 0;
  font-size: 2rem;
  color: #fff;
}

.overview {
  color: #aaa;
  line-height: 1.8;
  margin-bottom: 25px;
  font-size: 1.15rem;
  max-width: none;
}
</style>