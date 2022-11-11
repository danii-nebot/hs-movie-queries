<script setup>
import { ref, computed, watch } from 'vue';
import MyButton from './components/MyButton.vue';
import MOVIES from './assets/movies.json';

const movies = MOVIES.slice(); // clone input array
const itemsPerPage = ref(5);
const page = ref(1);
const yearsSelected = ref([]);
const filterTitle = ref('');

function goToNext() {
  page.value++;
}

function goToPrev() {
  page.value--;
}

function filterByYear(movie) {
  // (yearsSelected.value.length === 0) means no years were selected
  if (yearsSelected.value.length === 0) return true;
  return yearsSelected.value.includes(movie.year);
}

function filterByTitle(movie) {
  if(filterTitle.value.length < 2) return true;
  const lowerCaseTitle = filterTitle.value.toLowerCase();
  return movie.title.toLowerCase().includes(lowerCaseTitle);
}

function avergageScore(list) {
  const sum = list
    .map(m => Number(m.score))
    .reduce((a, b) => a + b, 0);
  return (sum /list.length) || 0;
}
const filterMoviesAverageScore = computed(() => {
  return avergageScore(filteredMovies.value);
})

const totalAverageScore = computed(() => {
  return avergageScore(movies);
})

const currentPageMoviesAverageScore = computed(() => {
  return avergageScore(moviesSnapshot.value);
})
const filteredMovies = computed(() => {
  const fm = movies
    .filter(filterByYear)
    .filter(filterByTitle);

    fm.sort((movie1, movie2) => {
      return Number(movie2.score) - Number(movie1.score);
    })
    return fm;
});

const moviesSnapshot = computed(() => {
    return filteredMovies.value
    .slice(
      (page.value - 1) * itemsPerPage.value,
      page.value * itemsPerPage.value
    );
});

const totalPages = computed(() => {
  return Math.ceil(filteredMovies.value.length / itemsPerPage.value);
});

watch(itemsPerPage, () => {
  page.value = 1;
});

</script>

<template>
  <input type="number" v-model="itemsPerPage" /> <br/>
  filter avergage score: {{ filterMoviesAverageScore }}<br/>
  current page avg score: {{currentPageMoviesAverageScore}}<br/>
  total score: {{ totalAverageScore }}<br/>
  <ul>
    <li
      v-for="movie in moviesSnapshot"
      :key="movie.id"
    >
      {{ movie.title }} || {{ movie.year }} || {{ movie.score }}
    </li>
  </ul>
  <h3>Pagination Controls</h3>
  <MyButton
    :disabled="page === 1"
    @click="page = 1">
    first page
  </MyButton>
  <MyButton
    :disabled="page === 1"
    @click="goToPrev">
    &lt;
  </MyButton>
  {{ page }} / {{ totalPages }}
  <MyButton
  :disabled="page === totalPages"
  @click="goToNext">
    &gt;
  </MyButton>
  <MyButton
    :disabled="page === totalPages"
    @click="page = totalPages">
    last page
  </MyButton>

  <hr />
  <h3>Filters</h3>
  <input v-model="filterTitle" type="text" />
  <br/><br/>
  <select v-model="yearsSelected" multiple>
      <option>1999</option>
      <option>2015</option>
      <option>2022</option>
    </select>
</template>
