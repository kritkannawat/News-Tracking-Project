<template>
  <div class="container">
    <div class="border-bottom mb-4">

      <!-- Search Input -->
      <header class="row align-items-center p-3">
        <div class="col" />
        <a href="/" class="col-12 col-md-4 text-center">
          <img src="/logo.svg" alt="logo" width="96" />
        </a>
        <div class="col">
          <input v-model="searchQuery" @keyup.enter="searchNews" type="text" placeholder="Search"
            class="form-control w-100" />
        </div>
      </header>

      <!-- Category Navigation -->
      <nav class="nav nav-underline justify-content-between custom-nav flex-wrap flex-nowrap overflow-auto">
        <button v-for="category in categories" :key="category" class="nav-item nav-link link-body-emphasis"
          @click="changeCategory(category)">
          {{ capitalize(category) }}
        </button>
      </nav>
    </div>

    <!-- Loading Spinner -->
    <div v-if="loading" class="d-flex justify-content-center my-5">
      <div class="spinner-border" role="status">
        <span class="visually-hidden">Loading...</span>
      </div>
    </div>

    <!-- News Results -->
    <div v-else class="row">
      <a :href="result.link" target="_blank" class="col-12 col-sm-6 col-lg-4 mb-4" v-for="(result, index) in newsData"
        :key="index" style="text-decoration: none;">
        <div class="card shadow-sm">
          <img :src="result.image_url" alt="Thumbnail" class="card-img-top img-fluid"
            style="height: 240px; object-fit: cover;" />
          <div class="card-body">
            <p class="card-text fw-bold">{{ result.title }}</p>
            <div class="d-flex justify-content-between align-items-center">
              <span>
                by <span class="ms-1 card-text fw-semibold">{{ result.source_name }}</span>
              </span>
              <small class="text-muted">{{ result.pubDate }}</small>
            </div>
          </div>
        </div>
      </a>
    </div>
    <footer class="d-flex flex-wrap justify-content-between align-items-center py-3 my-4 border-top">
      <p class="col-md-4 mb-0 text-body-secondary">News Tracking Project</p>
      <a href="https://sites.google.com/hwn.ac.th/std19330" target="_blank">
        <img src="/footer-logo.svg" alt="logo" width="120" />
      </a>
      <ul class="nav col-md-4 justify-content-end">
        <li class="nav-item"><a href="https://vuejs.org/" target="_blank"
            class="nav-link px-2 text-body-secondary">Vue.js</a></li>
        <li class="nav-item"><a href="https://getbootstrap.com/" target="_blank"
            class="nav-link px-2 text-body-secondary">Bootstrap</a>
        </li>
        <li class="nav-item"><a href="https://newsdata.io/" target="_blank"
            class="nav-link px-2 text-body-secondary">Newsdata</a></li>
        <li class="nav-item"><a href="https://github.com/kritkannawat/news-tracking-project" target="_blank" class="nav-link px-2 text-body-secondary">Github</a></li>
      </ul>
    </footer>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import axios from 'axios'

const newsData = ref([])
const loading = ref(false)
const searchQuery = ref('')
const selectedCategory = ref('top')

// Define available categories
const categories = ref([
  'top', 'business', 'crime', 'domestic', 'education', 'entertainment',
  'environment', 'food', 'health', 'lifestyle', 'other', 'politics',
  'science', 'sports', 'technology', 'tourism', 'world'
])

// Fetch news data by category
async function getNewsData(category) {
  loading.value = true
  try {
    const resp = await axios.get(
      `https://newsdata.io/api/1/latest?apikey=[API_KEY]&country=[LANGUAGE]&language=[LANGUAGE]&category=${category}`
    )
    newsData.value = resp.data.results
  } catch (error) {
    console.error('Failed to fetch news:', error)
  } finally {
    loading.value = false
  }
}

// Search news by query
async function searchNews() {
  if (!searchQuery.value.trim()) return

  loading.value = true
  try {
    const resp = await axios.get(
      `https://newsdata.io/api/1/news?apikey=[API_KEY]&country=[LANGUAGE]&language=[LANGUAGE]&q=${encodeURIComponent(searchQuery.value)}`
    )
    newsData.value = resp.data.results
  } catch (error) {
    console.error('Failed to search news:', error)
  } finally {
    loading.value = false
  }
}

// Change category and fetch related news
function changeCategory(category) {
  selectedCategory.value = category
  searchQuery.value = ''
  getNewsData(category)
}

// Capitalize category name for display
function capitalize(str) {
  return str.charAt(0).toUpperCase() + str.slice(1)
}

// Load default category on mount
onMounted(() => {
  getNewsData(selectedCategory.value)
})
</script>
