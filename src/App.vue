<template>
  <div class="w-full h-full flex justify-center items-center flex-col" v-if="state.jokeStatus != 'loading'">
    <span class="text-3xl mb-5">{{ state.joke.setup }}</span>
    <span class="text-2xl text-gray-400 mb-10" v-if="state.jokeStatus == 'delivered'">
      {{ state.joke.delivery }}
    </span>
    <span class="text-2xl text-gray-400 mb-10" v-else> ... </span>
    <button class="rounded-lg shadow w-24 h-12 bg-gray-200" @click="handleDelivery" v-if="state.jokeStatus == 'setup'">
      Tell Me!
    </button>
    <button class="rounded-lg shadow w-24 h-12 bg-gray-200" v-if="state.jokeStatus == 'delivered'" @click="handleSetup">
      Another!
    </button>
  </div>
  <div class="w-full h-full flex justify-center items-center flex-col" v-else>Loading...</div>
</template>

<script setup lang="ts">
import { reactive, onMounted } from 'vue'

type Joke = {
  setup: string
  delivery: string
}

const state = reactive({
  jokeStatus: 'none',
  updateJokeStatus: event => {
    console.log(event)
    switch (event) {
      case 'loading':
        state.jokeStatus = 'loading'
        break
      case 'setup':
        state.jokeStatus = 'setup'
        break
      case 'delivered':
        state.jokeStatus = 'delivered'
        break
      default:
        state.jokeStatus = 'none'
    }
  },
  joke: {} as Joke,
})

onMounted(async () => {
  loadJoke()
})

const fetchJoke = async () => {
  const response = await fetch('https://v2.jokeapi.dev/joke/christmas')
  const joke = await response.json()
  return joke as Joke
}

const loadJoke = async () => {
  console.log(state.jokeStatus)
  state.updateJokeStatus('loading')
  state.joke = await fetchJoke()
  state.updateJokeStatus('setup')
}

const handleDelivery = () => {
  state.updateJokeStatus('delivered')
}

const handleSetup = () => {
  loadJoke()
}
</script>
