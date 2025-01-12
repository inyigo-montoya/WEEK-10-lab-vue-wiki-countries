<script setup>
import countries from "../countries.json";
import { useRoute } from "vue-router";
import { computed, watch, ref, onMounted } from "vue";
import LoadingSpinner from "./LoadingSpinner.vue";

const route = useRoute();

const country = ref(null);
const loading = ref(true);
const error = ref(null);
const apiUrl = computed(
  () => `https://ih-countries-api.herokuapp.com/countries/${route.params.id}`
);

function fetchData() {
  fetch(apiUrl.value)
    .then((res) => {
      if (!res.ok) error.value = "Error - API unavailable";
      return res.json();
    })
    .then((data) => (country.value = data))
    .catch((err) => (error.value = err))
    .then(() => (loading.value = false));
}

onMounted(() => fetchData());
watch((route) => fetchData());
</script>

<template>
  <div v-if="country" class="counry-container">
    <LoadingSpinner v-if="loading" />
    <TransitionGroup name="list">
      <p v-if="error">{{ error }}</p>
      <p v-if="loading">fetching... 🤓</p>

      <img
        :src="`https://flagpedia.net/data/flags/icon/72x54/${country.alpha2Code.toLowerCase()}.png`"
        alt="country flag"
        style="width: 100px"
      />
      <h1>{{ country.name.common }}</h1>
      <table class="table">
        <thead></thead>
        <tbody>
          <tr>
            <td>Capital</td>
            <td>{{ country.capital.join(", ") }}</td>
          </tr>
          <tr>
            <td>Area</td>
            <td>
              {{ country.area }}
              km<sup>2</sup>
            </td>
          </tr>
          <tr>
            <td>Borders</td>
            <td>
              <ul>
                <li v-for="border in country.borders" :key="border">
                  <RouterLink :to="border">{{ border }}</RouterLink>
                </li>
              </ul>
            </td>
          </tr>
        </tbody>
      </table>
    </TransitionGroup>
  </div>
</template>

<style scoped>
li {
  list-style: none;
}

.list-enter-active,
.list-leave-active {
  transition: all 0.5s ease;
}
.list-enter-from,
.list-leave-to {
  opacity: 0;
  transform: translateY(30px);
}
.counry-container {
  width: -webkit-fill-available;
}
</style>
