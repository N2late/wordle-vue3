<template>
  <div class="container">
    <h1 class="title">{{ msg }}</h1>

    <div class="card">
      <!-- render 6 times the same component -->
      <button v-if="!clicked" class="btn" @click="setWordOfTheDay">Play</button>
      <WordleCard
        v-if="clicked"
        v-for="i in 6"
        :key="i + id"
        :wordOfTheDay="wordOfTheDay"
        :count="count"
        @updateCount="updateCount"
      />
    </div>

    <div class="reset-form">
      <button v-if="clicked" class="btn" @click="playAgain">
        Play a new game
      </button>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue';
import WordleCard from './WordleCard.vue';
import wordsWith5L from '../../db/words_with_five_letters.json';

defineProps({
  msg: String,
});

const wordOfTheDay = ref(null);
const clicked = ref(false);
const count = ref(0);
const id = ref(0);

function setWordOfTheDay() {
  wordOfTheDay.value =
    Object.keys(wordsWith5L)[
      Math.floor(Math.random() * Object.keys(wordsWith5L).length)
    ];
  console.log(wordOfTheDay.value);
  clicked.value = true;
}

function updateCount() {
  count.value += 1;
  if (count.value === 6) {
    alert('No more guesses left! The word was ' + wordOfTheDay.value);
    clicked.value = false;
  }
}

function playAgain() {
  wordOfTheDay.value = null;
  count.value = 0;
  // reset values in all the child components
  id.value += 1;
  setWordOfTheDay();
}
</script>

<style>
.container {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  height: 100%;
  width: 100%;
  background-color: #f5f5f5;
}

.title {
  font-size: 3rem;
  margin-bottom: 2rem;
  color: #333;
}

.card {
  padding: 2rem;
  background-color: #fff;
  border-radius: 0.5rem;
  box-shadow: 0 0.5rem 1rem rgba(0, 0, 0, 0.1);
}

.btn {
  background-color: #333;
  border: none;
  color: white;
  padding: 1rem 2rem;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  font-size: 1.5rem;
  margin: 1rem;
  border-radius: 0.5rem;
  cursor: pointer;
  transition: all 0.2s ease-in-out;
}

.btn:hover {
  background-color: #555;
  opacity: 0.8;
}

.reset-form {
  margin-top: 2rem;
}

@media (max-width: 768px) {
  .title {
    font-size: 2rem;
  }

  .btn {
    font-size: 1.2rem;
  }
}
</style>
