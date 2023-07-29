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
      <p class="mt-2 text-center">Number of Options</p>
      <input v-model="numOptions" type="number" class="mx-auto mt-2 flex rounded-lg bg-gray-500 px-6 py-2 text-white" />
    </div>
    <div v-else>
      <div class="mx-auto mt-12 w-fit text-center">
        <audio ref="audioPlayer" controls :src="audio" type="audio/mp3" class="invisible fixed" />
        <p>You have heard {{ seconds }} seconds of the song</p>
        <p class="mt-2 font-bold">Stats:</p>
        <p>Correct: {{ numCorrect }} Incorrect: {{ numIncorrect }}</p>
        <p>Total Seconds Played: {{ totalSeconds }} seconds</p>
        <p>Total Songs Played: {{ totalSongs }}</p>
        <button class="mx-auto mt-2 rounded-lg bg-blue-500 px-6 py-2 text-white" @click="playForOneSecond">
          Play for 1 second
        </button>
      </div>
      <div class="flex flex-wrap">
        <div v-for="option of options" :key="option" class="flex w-full flex-col rounded-lg p-4 md:w-1/5">
          <div
            class="w-full rounded-lg bg-cover bg-center p-2 transition ease-in-out hover:scale-105 md:aspect-square"
            :class="questionDone && option === name ? 'bg-green-500' : 'bg-gray-500'"
            :style="'background-image: url(' + optionsAlbumCovers[option] + ')'"
            @click="choose(option)"
          ></div>
          <p class="mt-1 text-2xl font-bold drop-shadow-xl">{{ option }}</p>
        </div>
      </div>
    </div>
    <div
      v-if="questionDone"
      class="fixed left-0 top-0 flex h-screen w-full flex-col items-center justify-center text-center text-3xl font-bold md:text-6xl"
      style="background-color: rgba(0, 0, 0, 0.5)"
    >
      <div v-if="correct" class="flex flex-col text-green-500">
        <p class="drop-shadow-lg">Guessed Correctly in {{ seconds }} seconds</p>
        <p class="drop-shadow-lg">The song was {{ name }}</p>
      </div>
      <div v-else class="flex flex-col text-red-500">
        <p class="drop-shadow-lg">Guessed Incorrectly in {{ seconds }} seconds</p>
        <p class="drop-shadow-lg">The song was {{ name }}</p>
      </div>
    </div>
    <BackButton />
  </div>
</template>

<script setup lang="ts">
import data from '~/assets/data/Spotify/data.json'

const numOptions = ref(5)
const started = ref(false)
const questionDone = ref(false)
const correct = ref(false)
const numCorrect = ref(0)
const numIncorrect = ref(0)
const totalSeconds = ref(0)
const totalSongs = ref(0)
const seconds = ref(0)
const audioPlayer = ref<HTMLAudioElement>()

const audio = ref('')
const name = ref('')
const albumCover = ref('')
const options = ref([])
const optionsAlbumCovers = ref([])

function newSong() {
  const randomSong = data[Math.floor(Math.random() * data.length)]
  audio.value = randomSong['Track Preview URL']
  name.value = randomSong['Track Name'] + ' - '
  for (const artist of randomSong['Artist Name(s)']) {
    name.value += artist + ' '
  }
  albumCover.value = randomSong['Album Image URL']
  if (audio.value === '') {
    newSong()
  } else {
    newOptions()
    totalSongs.value += 1
  }
}
// find four random songs and add the correct one to the options then shuffle the options
function newOptions() {
  options.value = []
  while (options.value.length < numOptions.value) {
    const randomSong = data[Math.floor(Math.random() * data.length)]
    let display = randomSong['Track Name'] + ' - '
    for (const artist of randomSong['Artist Name(s)']) {
      display += artist + ' '
    }
    if (!options.value.includes(display)) {
      options.value.push(display)
      optionsAlbumCovers.value[display] = randomSong['Album Image URL']
    }
  }
  options.value[Math.floor(Math.random() * numOptions.value)] = name.value
  optionsAlbumCovers.value[name.value] = albumCover.value
}

function playForOneSecond() {
  audioPlayer.value.play()
  seconds.value += 1
  totalSeconds.value += 1
  setTimeout(() => {
    audioPlayer.value.pause()
  }, 1000)
}

function choose(option: string) {
  audioPlayer.value.play()
  questionDone.value = true
  if (option === name.value) {
    correct.value = true
    numCorrect.value += 1
  } else {
    correct.value = false
    numIncorrect.value += 1
  }
  setTimeout(() => {
    audioPlayer.value.pause()
    questionDone.value = false
    seconds.value = 0
    newSong()
  }, 3000)
}
</script>
