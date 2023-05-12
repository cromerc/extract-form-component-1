<script setup>
import MovieItem from "@/MovieItem.vue";
import MovieForm from "@/MovieForm.vue";
import { computed, reactive, ref } from "vue";

import { items } from "./movies.json";

const movies = ref(items);

function updateRating(id, rating) {
  movies.value = movies.value.map((movie) => {
    if (movie.id === id) {
      movie.rating = rating;
    }
    return movie;
  })
}
function removeMovie(id) {
  movies.value = movies.value.filter((movie) => movie.id !== id);
}
function editMovie(id) {
  const movie = movies.value.filter((movie) => movie.id === id)[0];

  editMovieForm.id = movie.id;
  editMovieForm.name = movie.name;
  editMovieForm.description = movie.description;
  editMovieForm.image = movie.image;
  editMovieForm.inTheaters = movie.inTheaters;
  editMovieForm.genres = movie.genres;

  showForm();
}

const editMovieForm = reactive({
  id: null,
  name: null,
  description: null,
  image: null,
  inTheaters: false,
  genres: null,
});

const showMovieForm = ref(false);

function hideForm() {
  showMovieForm.value = false;
}

function showForm() {
  showMovieForm.value = true;
}

function saveMovie(form) {
  if (form.id) {
    updateMovie(form);
  } else {
    addMovie(form);
  }
}

function updateMovie(form) {
  const movie = {
    id: form.id,
    name: form.name,
    description: form.description,
    image: form.image,
    genres: form.genres,
    inTheaters: form.inTheaters,
    rating: null,
  };

  movies.value = movies.value.map((m) => {
    if (m.id === movie.id) {
      movie.rating = m.rating;
      return movie;
    }
    return m;
  });

  hideForm();
}

function addMovie(form) {
  const movie = {
    id: Number(Date.now()),
    name: form.name,
    description: form.description,
    image: form.image,
    genres: form.genres,
    inTheaters: form.inTheaters,
    rating: null,
  };
  movies.value.push(movie);

  hideForm();
}

const averageRating = computed(() => {
  const avg = movies.value
    .map((movie) => parseInt(movie.rating || 0))
    .reduce((a, b) => a + b, 0);

  return Number(avg / movies.value.length).toFixed(1);
});

const totalMovies = computed(() => {
  return movies.value.length;
});

function removeRatings() {
  movies.value = movies.value.map((movie) => {
    movie.rating = null;
    return movie;
  });
}
</script>

<template>
  <div class="app">
    <MovieForm v-model="editMovieForm" v-if="showMovieForm" @update:modelValue="saveMovie" @cancel="hideForm" />
    <div class="movie-actions-list-wrapper">
      <div class="movie-actions-list-info">
        <span>Total Movies: {{ totalMovies }}</span>
        <span> / </span>
        <span>Average Rating: {{ averageRating }}</span>
      </div>
      <div class="flex-spacer"></div>
      <div class="movie-actions-list-actions">
        <button class="self-end movie-actions-list-action-button button-primary justify-self-end" @click="removeRatings">
          Remove Ratings
        </button>
        <button class="movie-actions-list-action-button" :class="{
            'button-primary': !showMovieForm,
            'button-disabled': showMovieForm,
          }" @click="showForm" :disabled="showMovieForm">
          Add Movie
        </button>
      </div>
    </div>
    <div class="movie-list">
      <MovieItem v-for="movie in movies" :key="movie.id" :movie="movie" @edit="editMovie" @remove="removeMovie"
        @update:rating="updateRating" />
    </div>
  </div>
</template>
