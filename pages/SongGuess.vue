<template>
  <div class="mx-auto min-h-screen p-10 md:w-2/3">
    <div class="flex flex-row items-center justify-center">
      <img class="flex h-28 flex-col" alt="google maps icon" src="~/assets/images/Spotify/logo.png" />
      <p class="flex flex-col text-center text-4xl font-bold tracking-tight md:text-7xl">Guess the Song (ASIEL)</p>
    </div>
    <div v-if="!started" class="mt-8">
      <button
        class="mx-auto flex rounded-lg bg-green-500 px-6 py-2 text-white"
        @click="
          started = true;
          newSong()
        "
      >
        Start
      </button>
    </div>
    <div v-else>
      <div class="mx-auto mt-8 w-fit text-center">
        <audio ref="audioPlayer" controls :src="audio" type="audio/mp3" class="" />
        <button class="mx-auto mt-2 rounded-lg bg-blue-500 px-6 py-2 text-white" @click="playForOneSecond">
          Play for 1 second
        </button>
      </div>
      <div class="flex flex-col md:flex-row">
        <div v-for="option of options" :key="option" class="flex w-full flex-col rounded-lg p-4 text-center md:w-1/5">
          <div
            class="w-full rounded-lg p-4 ring-2 ring-black transition ease-in-out hover:scale-105 dark:ring-white md:aspect-square"
            :class="questionDone && option === name ? 'bg-green-500' : 'bg-gray-500'"
            @click="choose(option)"
          >
            <p class="text-2xl font-bold">{{ option }}</p>
          </div>
        </div>
      </div>
      <div class="text-center text-3xl font-bold md:text-7xl">
        <p v-if="questionDone && correct">Guessed Correctly in {{ seconds }} seconds</p>
        <p v-if="questionDone && !correct">Guess Incorrectly</p>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import data from '~/assets/data/Spotify/data.json'

const started = ref(false)
const questionDone = ref(false)
const correct = ref(false)
const seconds = ref(0)
const audioPlayer = ref<HTMLAudioElement>()

const audio = ref('')
const name = ref('')
const options = ref([])

function newSong() {
  const randomSong = data[Math.floor(Math.random() * data.length)]
  audio.value = randomSong['Track Preview URL']
  name.value = randomSong['Track Name'] + ' - '
  for (const artist of randomSong['Artist Name(s)']) {
    name.value += artist + ' '
  }
  if (audio.value === '') {
    newSong()
  } else {
    newOptions()
  }
}
// find four random songs and add the correct one to the options then shuffle the options
function newOptions() {
  options.value = []
  while (options.value.length < 5) {
    const randomSong = data[Math.floor(Math.random() * data.length)]
    let display = randomSong['Track Name'] + ' - '
    for (const artist of randomSong['Artist Name(s)']) {
      display += artist + ' '
    }
    if (!options.value.includes(display)) {
      options.value.push(display)
    }
  }
  options.value[Math.floor(Math.random() * 5)] = name.value
}

function playForOneSecond() {
  audioPlayer.value.play()
  seconds.value += 1
  setTimeout(() => {
    audioPlayer.value.pause()
  }, 1000)
}

function choose(option: string) {
  questionDone.value = true
  if (option === name.value) {
    correct.value = true
  } else {
    correct.value = false
  }
  setTimeout(() => {
    questionDone.value = false
    seconds.value = 0
    newSong()
  }, 3000)
}
</script>
