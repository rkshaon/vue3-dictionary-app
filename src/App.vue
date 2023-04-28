<template>
  <div class="container">
    <div style="background-color: black; padding: 20px 0px; text-align: center; color: white; text-transform: uppercase;">
      <h1>Dictionary App</h1>
    </div>
    <div style="padding: 10px 0px;">
      <form @submit.prevent="getDefinitions()">
        <div class="input-group mb-3">
          <div class="input-group-prepend">
            <span class="input-group-text me-1" id="inputGroup-sizing-default">Word</span>
          </div>
          <input type="text" class="form-control" aria-label="Default" aria-describedby="inputGroup-sizing-default" placeholder="Type a word" id="word" v-model="word">
        </div>
        <button class="btn btn-success" type="submit">Get definitions</button>
      </form>
    </div>
    <div>    
      <div v-if="word">
        <p>Definitions for word <i>{{ word }}</i></p>
      </div>
      <p v-if="!validQuery">Error: the word you are searching does not exist.</p>
      <div v-for="(definition, index) in definitionsArray" :key="definition">
        <!-- <div>{{ definition }}</div> -->
        <div class="card bg-dark" style="margin-top: 5px;">
          <div class="card-body">
            <p class="card-text text-warning">{{ index + 1 }}</p>
            <h5 class="card-title text-white">{{ definition }}</h5>            
          </div>
        </div>
      </div>
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
