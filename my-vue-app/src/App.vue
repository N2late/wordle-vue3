<script setup>
import WordleGame from './components/WordleGame.vue';
</script>

<template>
  <div class="container">
    <WordleGame msg="WORDLE" />
  </div>
</template>

<style>
.container {
  width: 90vw;
  height: 90vh;
}
</style>
