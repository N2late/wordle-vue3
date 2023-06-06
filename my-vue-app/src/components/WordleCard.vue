<template>
  <div class="world-card-field" @keyup.enter="checkWord(wordOfTheDay)">
    <input
      v-for="(letter, index) in letters"
      :key="index"
      required
      type="text"
      maxlength="1"
      class="world-card-letter"
      v-model="letter.value"
      :style="{ backgroundColor: letters[index].backgroundColor.value }"
      @input="focusNextInput(index)"
    />
  </div>
</template>

<script setup>
import { ref, getCurrentInstance, nextTick } from 'vue';
import wordsWith5L from '../../db/words_with_five_letters.json';

defineProps({
  wordOfTheDay: String,
  count: Number,
});

defineEmits(['updateCount']);

const { emit } = getCurrentInstance();

const letters = [
  { value: null, backgroundColor: ref('white') },
  { value: null, backgroundColor: ref('white') },
  { value: null, backgroundColor: ref('white') },
  { value: null, backgroundColor: ref('white') },
  { value: null, backgroundColor: ref('white') },
];

function checkWord(wordOfTheDay) {
  const word = letters.map((letter) => letter.value).join('');

  if (!isEnglishWord(word)) {
    alert('This is not an English word');
    return;
  }

  highlightLetters(word, wordOfTheDay);

  disableInputFields('currentWordleComponent');

  if (isWordOfTheDay(word, wordOfTheDay)) {
    alert('You win!');
    disableInputFields('allWordleComponents');
    return;
  }

  emit('updateCount');
}

function highlightLetters(word, wordOfTheDay) {
  letters.forEach((letter, index) => {
    const inputOccurrences = countOccurrences(word, letter.value);
    const letterFromWOTDOccurrences = countOccurrences(
      wordOfTheDay,
      letter.value,
    );
    if (letter.value === wordOfTheDay[index]) {
      highlightLetter(letter, 'green');
    } else if (
      wordOfTheDay.includes(letter.value) &&
      inputOccurrences <= letterFromWOTDOccurrences
    ) {
      highlightLetter(letter, 'yellow');
    } else {
      highlightLetter(letter, 'lightgrey');
    }
  });
}

function highlightLetter(letter, color) {
  letter.backgroundColor.value = color;
}

function disableInputFields(onComponent) {
  const allInputs = document.querySelectorAll('input');
  if (onComponent === 'allWordleComponents') {
    allInputs.forEach((input) => {
      input.disabled = true;
    });
  } else {
    allInputs.forEach((input) => {
      if (input.value) {
        input.disabled = true;
      }
    });
  }
}

function countOccurrences(str, char) {
  const regex = new RegExp(char, 'g');
  return (str.match(regex) || []).length;
}

function isEnglishWord(word) {
  // check if word is in the database
  return Object.keys(wordsWith5L).includes(word.toLowerCase());
}

function isWordOfTheDay(word, wordOfTheDay) {
  return word === wordOfTheDay;
}

function focusNextInput(index) {
  if (index === letters.length - 1) {
    return;
  }

  nextTick(() => {
    // get current input index from querySelectorAll
    const allInputs = document.querySelectorAll('input');
    // get current number of tab
    const currentTab = Array.from(allInputs).indexOf(document.activeElement);
    // focus next input
    allInputs[currentTab + 1].focus();
  });
}

function reset() {
  letters.forEach((letter) => {
    letter.value = null;
    letter.backgroundColor.value = 'white';
  });

  const allInputs = document.querySelectorAll('input');
  allInputs.forEach((input) => {
    input.disabled = false;
  });
}
</script>

<style>
.world-card-field {
  display: flex;
  background-color: #414141;
}

.world-card-letter {
  width: 50px;
  height: 50px;
  border: 1px solid #000;
  text-align: center;
  font-size: 30px;
  margin: 5px;
  border-radius: 5px;
}
</style>
