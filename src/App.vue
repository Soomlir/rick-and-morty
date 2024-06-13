<script setup>
import { ref } from "vue";

let charactersData = ref([]);
let page = ref(1); // Изменяем начальное значение страницы на 1
const disabledPrev = ref(false);
const disabledNext = ref(false);
const searchItem = ref("");

const fetchData = (pageNumber) => {
  fetch(`https://rickandmortyapi.com/api/character/?page=${pageNumber}`)
    .then((response) => response.json())
    .then((data) => {
      charactersData.value = data.results;
    });
};

fetchData(page.value); // Вызываем fetchData для загрузки данных при инициализации

const nextPage = () => {
  if (page.value < 42) {
    // Проверяем, что значение страницы меньше 42
    page.value++;
    fetchData(page.value);
  }
};

const prevPage = () => {
  if (page.value > 1) {
    page.value--; // Уменьшаем значение страницы на 1, если текущая страница больше 1
    fetchData(page.value); // Вызываем fetchData с новым значением страницы
  }
};

const filteredData = (searchItem) => {
  fetch(
    `https://rickandmortyapi.com/api/character/?name=${searchItem}&status=alive`
  )
    .then((response) => response.json())
    .then((data) => {
      charactersData.value = data.results;
    });
};
}
</script>

<template>
  <div class="wrapper">
    <h1 class="title">The Rick and Morty API</h1>
    <div class="buttons">
      <button @click="prevPage" :disabled="disabledPrev">PREV</button>
      <span>{{ page }}</span>
      <button @click="nextPage" :disabled="disabledNext">NEXT</button>
    </div>
    <div>
      <input type="search" v-model="searchItem" />
      <button @click="filteredData">Искать</button>
    </div>
    <ul class="character-list">
      <li class="character-item" v-for="character of charactersData">
        <img :src="character.image" alt="..." width="230" height="260" />
        <div class="info">
          <h2>{{ character.name }}</h2>
          <p>{{ character.status }} - {{ character.species }}</p>
          <p>Last known location:</p>
          <p>{{ character.origin.name }}</p>
          <p>First seen in:</p>
          <p>{{ character.episode[0] }}</p>
        </div>
      </li>
    </ul>
  </div>
</template>

<style>
body {
  background-color: lightgray;
}

.wrapper {
  width: 960px;
  margin-right: auto;
  margin-left: auto;
}

.title {
  text-align: center;
}

.character-list {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 30px;
}
.character-item {
  display: flex;
  gap: 30px;
  color: #ffffff;
  background-color: gray;
}
.info {
  min-height: 230px;
}
</style>
