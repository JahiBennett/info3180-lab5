<template>
  <div class="movie-form">
    <h2>Add a New Movie</h2>
    <form id="movieForm" @submit.prevent="saveMovie" enctype="multipart/form-data">
    <form @submit.prevent="saveMovie" enctype="multipart/form-data">
      <div class="form-group mb-3">
        <label for="title" class="form-label">Movie Title</label>
        <input type="text" name="title" class="form-control" v-model="title" required />
      </div>

      <div class="form-group mb-3">
        <label for="description" class="form-label">Description</label>
        <textarea name="description" class="form-control" v-model="description" required></textarea>
      </div>

      <div class="form-group mb-3">
        <label for="poster" class="form-label">Movie Poster</label>
        <input type="file" name="poster" class="form-control" @change="handleFileUpload" required />
      </div>

      <button type="submit" class="btn btn-primary">Submit</button>
    </form>
  </div>
</template>

<script setup>
import { ref, onMounted } from "vue"

const title = ref("")
const description = ref("")
const poster = ref(null)
const csrf_token = ref("")

function handleFileUpload(event) {
  poster.value = event.target.files[0]
}

function getCsrfToken() {
  fetch("/api/v1/csrf-token")
    .then((response) => response.json())
    .then((data) => {
      csrf_token.value = data.csrf_token
    })
    .catch((error) => console.log("Failed to get CSRF token:", error))
}

function saveMovie() {
  let movieForm = document.getElementById('movieForm')
  let form_data = new FormData(movieForm)

  fetch("/api/v1/movies", {
    method: 'POST',
    body: form_data,
    headers: {
      'X-CSRFToken': csrf_token.value
    }
  })
    .then(response => response.json())
    .then(data => {
      console.log(data)
    })
    .catch(error => {
      console.log("Upload failed:", error)
    })
}

onMounted(() => {
  getCsrfToken()
})
</script>

