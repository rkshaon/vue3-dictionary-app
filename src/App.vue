<template>
  <form @submit.prevent="getDefinitions()">
    <input type="text" placeholder="Type a word" id="word" v-model="word">
    <button type="submit">Get definitions</button>
  </form>
  <div>    
    <div v-if="word">
      <p>Definitions for word <i>{{ word }}</i></p>
    </div>
    <p v-if="!validQuery">Error: the word you are searching does not exist.</p>
    <div v-for="definition in definitionsArray" :key="definition">
      <div>{{ definition }}</div>
    </div>
  </div>
</template>

<script>
import { ref } from 'vue';
import axios from 'axios';

export default {
  name: 'App',
  setup() {
    const word = ref("");
    let validQuery = ref(true);
    const definitionsArray = ref([]);

    async function getDefinitions() {
      definitionsArray.value.splice(0, definitionsArray.value.length);
      validQuery.value = true;

      try {
        const url = "https://api.dictionaryapi.dev/api/v2/entries/en/" + word.value;
        const { data } = await axios.get(url);

        data.map(({ meanings }) => {
          meanings.map(({ definitions }) => {
            definitions.forEach(({ definition }) => {
              if (typeof definition === 'string') {
                definitionsArray.value.push(definition);
              }
            });
          })
        })
      } catch (error) {
        validQuery.value = false;
      }
    }
    
    return { word, getDefinitions, validQuery, definitionsArray };
  },
}
</script>

<style>
</style>
