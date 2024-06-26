<script setup>
import { ref, computed, watch } from "vue";

const BASE_URL = "https://rickandmortyapi.com/api/character";

const info = ref(null);
const url = ref(BASE_URL);
const charactersData = ref([]);
const query = ref("");
const error = ref("");

const page = computed(() => parseInt(url.value.slice(-1)) || 1);

const names = {};
const getEpisodes = () =>
  Promise.all(
    [1, 2, 3].map((page) => {
      return fetch(
        `https://rickandmortyapi.com/api/episode/?page=${page}`
      ).then((response) => response.json());
    })
  ).then((items) => {
    items.forEach(({ results }) => {
      results.forEach(({ id, name }) => {
        names[`https://rickandmortyapi.com/api/episode/${id}`] = name;
      });
    });
  });

const getData = () => {
  fetch(url.value)
    .then((response) => response.json())
    .then((data) => {
      info.value = data.info;
      error.value = data.error || "";
      charactersData.value = data.results;
    });
};

watch(() => url.value, getData);

getEpisodes().then(getData); // Вызываем getData для загрузки данных при инициализации
</script>

<template>
  <div class="wrapper">
    <h1 class="title">The Rick and Morty API</h1>
    <div class="controls-wrapper">
      <div class="buttons">
        <button @click="prevPage" :disabled="disabledPrev">PREV</button>
        <span>{{ page }}</span>
        <button @click="nextPage" :disabled="disabledNext">NEXT</button>
      </div>
      <div>
        <input type="search" v-model="searchItem" />
        <button @click="filteredData">Искать</button>
      </div>
    </div>
    <ul class="character-list">
      <li class="character-item" v-for="character of charactersData">
        <img :src="character.image" alt="..." width="230" height="220" />
        <dl class="info">
          <dt class="info__name">{{ character.name }}</dt>
          <dd class="info__status">
            <span class="info__status-indicator"></span>{{ character.status }} -
            {{ character.species }}
          </dd>
          <dt class="info__param">Last known location:</dt>
          <dd>
            <a :href="character.location.url">{{ character.location.name }}</a>
          </dd>
          <dt class="info__param">First seen in:</dt>
          <dd>
            <a :href="character.episode[0]">{{
              names[character.episode[0]]
            }}</a>
          </dd>
        </dl>
      </li>
    </ul>
  </div>
</template>

<style>
body {
  background: rgb(32, 35, 41);
}

button {
  color: #ffffff;
  background: rgb(60, 62, 68);
  border: none;
  cursor: pointer;
  width: 100px;
  height: 40px;
  margin: 10px;
}

input[type="search"] {
  width: 200px;
  height: 35px;
  padding: 5px 10px;
}

.wrapper {
  max-width: 1440px;
  margin-right: auto;
  margin-left: auto;
}

.title {
  text-align: center;
  color: #ffffff;
  font-weight: 900;
  z-index: 1;
}

.controls-wrapper {
  display: flex;
}

.buttons {
  color: #ffffff;
  font-size: 30px;
  padding: 10px;
}

.buttons span {
  width: 30px;
  display: inline-block;
  text-align: center;
}

.character-list {
  margin: 0;
  padding: 0;
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 20px;
  width: 1240px;
}

.character-item {
  display: flex;
  gap: 30px;
  color: #ffffff;
  background-color: rgb(60, 62, 68);
  width: 600px;
  height: 220px;
  display: flex;
  overflow: hidden;
  background: rgb(60, 62, 68);
  border-radius: 0.5rem;
  margin: 20px;
  box-shadow: rgba(0, 0, 0, 0.1) 0px 4px 6px -1px,
    rgba(0, 0, 0, 0.06) 0px 2px 4px -1px;
}

.character-item img {
  margin: 0px;
  opacity: 1;
  transition: opacity 0.5s;
}

.info {
  margin: 0;
  padding: 0;
  padding: 7px;
  font-size: 20px;
}

.info dd {
  margin: 0;
  margin-bottom: 20px;
}

.info__name {
  font-size: 2.5rem;
}

.info__status {
  font-size: 16px;
}

.info__status-indicator {
  height: 0.5rem;
  width: 0.5rem;
  margin-right: 0.375rem;
  background: rgb(85, 204, 68);
  border-radius: 50%;
  display: inline-block;
}

.info__location,
.info__seen {
  color: rgb(158, 158, 158);
  font-size: 16px;
  font-weight: 500;
}
</style>
