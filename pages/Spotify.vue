<template>
  <div class="mx-auto min-h-screen p-10 md:w-2/3">
    <div class="flex flex-row items-center justify-center">
      <img class="flex h-28 flex-col" alt="google maps icon" src="~/assets/images/Spotify/logo.png" />
      <p class="flex flex-col text-center text-4xl font-bold tracking-tight md:text-7xl">Spotify Playlist Analyzer</p>
    </div>
    <p class="mt-2 text-center text-xl tracking-tighter md:text-2xl">
      Analyzing all available metrics for my main playlist on Spotify.
    </p>
    <p>
      {{ data[0] }}
    </p>
    <BackButton />
  </div>
</template>

<script setup lang="ts">
import data from '~/assets/data/Spotify/data.json'
</script>
