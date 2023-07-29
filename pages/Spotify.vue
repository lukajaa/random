<template>
  <div class="mx-auto min-h-screen p-10 md:w-2/3">
    <div class="flex flex-row items-center justify-center">
      <img class="flex h-28 flex-col" alt="google maps icon" src="~/assets/images/Spotify/logo.png" />
      <p class="flex flex-col text-center text-4xl font-bold tracking-tight md:text-7xl">Spotify Playlist Analyzer</p>
    </div>
    <p class="mt-2 text-center text-xl tracking-tighter md:text-2xl">
      Analyzing all available metrics for my main playlist on Spotify.
    </p>
    <div class="mt-8 text-center text-lg">
      <p class="text-xl font-bold">Most Common Artists in Playlist</p>
      <p
        v-for="artist in sortedArtistFrequencies.slice(0, 25)"
        :key="artist[0]"
        data-aos="fade-in"
        :data-aos-delay="100 + 100 * sortedArtistFrequencies.indexOf(artist)"
        data-aos-duration="1000"
        data-aos-easing="ease-in"
      >
        {{ artist[0] }}: {{ artist[1] }}
      </p>
    </div>
    <div class="mt-8 text-center text-lg">
      <p class="text-xl font-bold">Most Common Albums in Playlist</p>
      <p
        v-for="album in sortedAlbumFrequencies.slice(0, 25)"
        :key="album[0]"
        data-aos="fade-in"
        :data-aos-delay="100 + 100 * sortedAlbumFrequencies.indexOf(album)"
        data-aos-duration="1000"
        data-aos-easing="ease-in"
      >
        {{ album[0] }}: {{ album[1] }}
      </p>
    </div>
    <div class="mt-8 text-center text-lg">
      <p class="text-xl font-bold">Most Common Artist Genres in Playlist</p>
      <p
        v-for="genre in sortedArtistGenreFrequencies.slice(0, 25)"
        :key="genre[0]"
        data-aos="fade-in"
        :data-aos-delay="100 + 100 * sortedArtistGenreFrequencies.indexOf(genre)"
        data-aos-duration="1000"
        data-aos-easing="ease-in"
      >
        {{ genre[0] }}: {{ genre[1] }}
      </p>
    </div>
    <div class="flex flex-wrap justify-center">
      <div class="flex w-1/5 flex-col p-8 text-center">
        <p class="text-6xl font-bold">{{ Math.round(averageTrackDuration) }}</p>
        <p class="text-lg">Average Track Duration</p>
      </div>
      <div class="flex w-1/5 flex-col p-8 text-center">
        <p class="text-6xl font-bold">{{ Math.round(averageTrackPopularity) }}</p>
        <p class="text-lg">Average Track Popularity</p>
      </div>
      <div class="flex w-1/5 flex-col p-8 text-center">
        <p class="text-6xl font-bold">{{ numExplicitTracks }}</p>
        <p class="text-lg">Number of Explicit Tracks</p>
      </div>
      <div class="flex w-1/5 flex-col p-8 text-center">
        <p class="text-6xl font-bold">{{ Math.round(averageDanceability * 100) / 100 }}</p>
        <p class="text-lg">Average Danceability</p>
      </div>
      <div class="flex w-1/5 flex-col p-8 text-center">
        <p class="text-6xl font-bold">{{ Math.round(averageEnergy * 100) / 100 }}</p>
        <p class="text-lg">Average Energy</p>
      </div>
      <div class="flex w-1/5 flex-col p-8 text-center">
        <p class="text-6xl font-bold">{{ Math.round(averageKey * 100) / 100 }}</p>
        <p class="text-lg">Average Key</p>
      </div>
      <div class="flex w-1/5 flex-col p-8 text-center">
        <p class="text-6xl font-bold">{{ Math.round(averageLoudness * 100) / 100 }}</p>
        <p class="text-lg">Average Loudness</p>
      </div>
      <div class="flex w-1/5 flex-col p-8 text-center">
        <p class="text-6xl font-bold">{{ Math.round(averageSpeechiness * 100) / 100 }}</p>
        <p class="text-lg">Average Speechiness</p>
      </div>
      <div class="flex w-1/5 flex-col p-8 text-center">
        <p class="text-6xl font-bold">{{ Math.round(averageAcousticness * 100) / 100 }}</p>
        <p class="text-lg">Average Acousticness</p>
      </div>
      <div class="flex w-1/5 flex-col p-8 text-center">
        <p class="text-6xl font-bold">
          {{ Math.round(averageInstrumentalness * 100) / 100 }}
        </p>
        <p class="text-lg">Average Instrumentalness</p>
      </div>
      <div class="flex w-1/5 flex-col p-8 text-center">
        <p class="text-6xl font-bold">
          {{ Math.round(averageLiveness * 100) / 100 }}
        </p>
        <p class="text-lg">Average Liveness</p>
      </div>
      <div class="flex w-1/5 flex-col p-8 text-center">
        <p class="text-6xl font-bold">
          {{ Math.round(averageValence * 100) / 100 }}
        </p>
        <p class="text-lg">Average Valence</p>
      </div>
      <div class="flex w-1/5 flex-col p-8 text-center">
        <p class="text-6xl font-bold">
          {{ Math.round(averageTempo * 100) / 100 }}
        </p>
        <p class="text-lg">Average Tempo</p>
      </div>
    </div>
    <BackButton />
  </div>
</template>

<script setup lang="ts">
import data from '~/assets/data/Spotify/data.json'

const artistFrequencies: Record<string, number> = {}
const albumFrequencies: Record<string, number> = {}
const artistGenreFrequencies: Record<string, number> = {}
let averageTrackDuration = 0
let averageTrackPopularity = 0
let numExplicitTracks = 0
let averageDanceability = 0
let averageEnergy = 0
let averageKey = 0
let averageLoudness = 0
let averageSpeechiness = 0
let averageAcousticness = 0
let averageInstrumentalness = 0
let averageLiveness = 0
let averageValence = 0
let averageTempo = 0
for (const track of data) {
  for (const artist of track['Artist Name(s)']) {
    if (artist in artistFrequencies) {
      artistFrequencies[artist]++
    } else {
      artistFrequencies[artist] = 1
    }
  }
  if (track['Album Name'] in albumFrequencies) {
    albumFrequencies[track['Album Name']]++
  } else {
    albumFrequencies[track['Album Name']] = 1
  }
  for (const genre of track['Artist Genres']) {
    if (genre in artistGenreFrequencies) {
      artistGenreFrequencies[genre]++
    } else {
      artistGenreFrequencies[genre] = 1
    }
  }
  averageTrackDuration += Number(track['Track Duration (ms)'])
  averageTrackPopularity += Number(track['Popularity'])
  if (track['Explicit'] === 'true') {
    numExplicitTracks++
  }
  averageDanceability += Number(track['Danceability'])
  averageEnergy += Number(track['Energy'])
  averageKey += Number(track['Key'])
  averageLoudness += Number(track['Loudness'])
  averageSpeechiness += Number(track['Speechiness'])
  averageAcousticness += Number(track['Acousticness'])
  averageInstrumentalness += Number(track['Instrumentalness'])
  averageLiveness += Number(track['Liveness'])
  averageValence += Number(track['Valence'])
  averageTempo += Number(track['Tempo'])
}
const sortedArtistFrequencies = Object.entries(artistFrequencies).sort((a, b) => b[1] - a[1])
const sortedAlbumFrequencies = Object.entries(albumFrequencies).sort((a, b) => b[1] - a[1])
const sortedArtistGenreFrequencies = Object.entries(artistGenreFrequencies).sort((a, b) => b[1] - a[1])
averageTrackDuration /= data.length
averageTrackDuration /= 1000
averageTrackPopularity /= data.length
averageDanceability /= data.length
averageEnergy /= data.length
averageKey /= data.length
averageLoudness /= data.length
averageSpeechiness /= data.length
averageAcousticness /= data.length
averageInstrumentalness /= data.length
averageLiveness /= data.length
averageValence /= data.length
averageTempo /= data.length
</script>
