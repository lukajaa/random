<template>
  <div class="min-h-screen p-24">
    <p class="text-center text-4xl font-bold tracking-tight md:text-8xl">Explore Random Projects!</p>
    <p class="mt-2 text-center text-xl tracking-tighter md:text-2xl">
      These are projects that weren't big enough to warrant their own website but still cool enough to show off.
    </p>
    <div class="flex flex-wrap">
      <div v-for="link in links" :key="link.name" class="flex w-full p-8 md:w-1/3">
        <NuxtLink
          :to="link.link"
          class="h-32 w-full rounded-lg p-4 shadow-lg ring-4 ring-black transition duration-300 ease-in-out hover:scale-105"
        >
          <p class="text-xl font-bold">{{ link.name }}</p>
        </NuxtLink>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
const links = [
  {
    name: 'Google Maps Photo View Predictor',
    link: '/GoogleMaps'
  },
  {
    name: 'Spotify Playlist Analyzer',
    link: '/Spotify'
  },
  {
    name: 'Guess the Song (ASIEL)',
    link: '/SongGuess'
  }
]
</script>
