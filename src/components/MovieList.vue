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
  <div class="page">
    <header class="page-header">
      <h1 class="page-title">Films populaires</h1>
    </header>

    <div v-if="loading" class="loader" aria-label="Chargement">
      <div class="spinner"></div>
      <p class="loader-text">Chargement des films…</p>
    </div>

    <ul v-else class="movie-list" role="list">
      <li v-for="movie in movies" :key="movie.id" class="movie-card">
        <div
          class="card-backdrop"
          :style="movie.backdrop_path ? `background-image: url(https://image.tmdb.org/t/p/w780${movie.backdrop_path})` : ''"
        ></div>
        <div class="card-content">
          <img
            v-if="movie.poster_path"
            :src="`https://image.tmdb.org/t/p/w300${movie.poster_path}`"
            :alt="movie.title"
            class="card-poster"
            loading="lazy"
          />
          <div class="card-body">
            <h2 class="card-title">{{ movie.title }}</h2>
            <p class="card-overview">{{ movie.overview }}</p>
            <div class="card-meta">
              <span class="card-rating" :title="`Note : ${(movie.vote_average ?? 0).toFixed(1)}/10`">
                {{ (movie.vote_average ?? 0).toFixed(1) }}
              </span>
              <span class="card-date">{{ movie.release_date || '—' }}</span>
            </div>
          </div>
        </div>
      </li>
    </ul>
  </div>
</template>

<style scoped>
.page {
  min-height: 100vh;
  background: #0d0d0d;
  color: #e5e5e5;
}

.page-header {
  padding: 2rem 1.5rem 1.5rem;
  max-width: 1100px;
  margin: 0 auto;
}

.page-title {
  font-size: clamp(1.75rem, 4vw, 2.25rem);
  font-weight: 700;
  letter-spacing: -0.02em;
  margin: 0;
  color: #fff;
}

/* Loader */
.loader {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: 1rem;
  min-height: 40vh;
  padding: 2rem;
}

.spinner {
  width: 48px;
  height: 48px;
  border: 3px solid rgba(255, 255, 255, 0.15);
  border-top-color: #fff;
  border-radius: 50%;
  animation: spin 0.9s linear infinite;
}

@keyframes spin {
  to { transform: rotate(360deg); }
}

.loader-text {
  margin: 0;
  font-size: 0.95rem;
  color: #888;
}

/* List */
.movie-list {
  list-style: none;
  margin: 0 auto;
  padding: 0 1.5rem 3rem;
  max-width: 1100px;
  display: flex;
  flex-direction: column;
  gap: 1.5rem;
}

.movie-card {
  position: relative;
  border-radius: 16px;
  overflow: hidden;
  background: #1a1a1a;
  box-shadow: 0 4px 24px rgba(0, 0, 0, 0.4);
}

.card-backdrop {
  position: absolute;
  inset: 0;
  background-size: cover;
  background-position: center;
  background-color: #1a1a1a;
  opacity: 0.35;
}

.card-backdrop::after {
  content: '';
  position: absolute;
  inset: 0;
  background: linear-gradient(to right, #0d0d0d 0%, transparent 50%);
}

.card-content {
  position: relative;
  display: flex;
  align-items: center;
  gap: 2rem;
  padding: 1.5rem 1.5rem 1.5rem 0;
  min-height: 180px;
}

.card-poster {
  width: 120px;
  height: 180px;
  object-fit: cover;
  border-radius: 10px;
  flex-shrink: 0;
  margin-left: 1.5rem;
  box-shadow: 0 8px 24px rgba(0, 0, 0, 0.5);
}

.card-body {
  flex: 1;
  min-width: 0;
}

.card-title {
  font-size: 1.35rem;
  font-weight: 600;
  margin: 0 0 0.5rem;
  color: #fff;
  line-height: 1.3;
}

.card-overview {
  font-size: 0.9rem;
  line-height: 1.55;
  color: #a3a3a3;
  margin: 0 0 0.75rem;
  display: -webkit-box;
  -webkit-line-clamp: 3;
  -webkit-box-orient: vertical;
  overflow: hidden;
}

.card-meta {
  display: flex;
  align-items: center;
  gap: 1rem;
  flex-wrap: wrap;
}

.card-rating {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  min-width: 2.25rem;
  padding: 0.25rem 0.5rem;
  font-size: 0.85rem;
  font-weight: 600;
  color: #0d0d0d;
  background: #fbbf24;
  border-radius: 6px;
}

.card-date {
  font-size: 0.875rem;
  color: #737373;
}

@media (max-width: 640px) {
  .card-content {
    flex-direction: column;
    align-items: flex-start;
    padding: 1.25rem 1.5rem;
  }

  .card-poster {
    width: 100px;
    height: 150px;
    margin-left: 0;
    margin-bottom: 0.5rem;
  }

  .card-backdrop::after {
    background: linear-gradient(to bottom, transparent 0%, #0d0d0d 60%);
  }
}
</style>