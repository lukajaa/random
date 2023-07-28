<template>
  <div class="mx-auto min-h-screen p-10 md:w-2/3">
    <div class="flex flex-row items-center justify-center">
      <img class="flex h-28 flex-col" alt="google maps icon" src="~/assets/images/GoogleMaps/icon.png" />
      <p class="flex flex-col text-center text-4xl font-bold tracking-tight md:text-7xl">
        Google Maps Photo View Predictor
      </p>
    </div>
    <p class="mt-2 text-center text-xl tracking-tighter md:text-2xl">
      Predicting when I will get 1,000,000 photo views on google maps and achieve the master photographer badge.
    </p>
    <div class="mx-auto mt-8 flex flex-row space-x-4 p-4">
      <div class="flex w-1/6 flex-col">
        <img src="~/assets/images/GoogleMaps/pfp.png" alt="profile picture" class="mx-auto w-40 rounded-full" />
      </div>
      <div class="flex w-5/6 flex-col">
        <p class="text-2xl font-bold tracking-tight md:text-4xl">About Me</p>
        <p class="mt-2 text-xl tracking-tighter md:text-2xl">Local Guide - Level 7 (10,575 points)</p>
        <p class="mt-2 text-lg tracking-tighter md:text-lg">
          Local Guides can earn points by writing reviews, uploading photos, answering questions, and adding or editing
          places. So far, I have {{ currentViews.toLocaleString() }} photo views. Here, I show multiple methods of
          predicting when I will reach 1,000,000 photo views (and earn the Master Photographer badge).
        </p>
      </div>
    </div>
    <p class="mt-8 text-center text-2xl font-bold tracking-tighter md:text-4xl">Predictions</p>
    <p class="mt-4 text-center text-xl tracking-tighter md:text-2xl">Linear Regression</p>
    <p class="mt-2 text-center text-lg tracking-tighter md:text-lg">
      Using linear regression, I can predict that I will reach 1,000,000 photo views
      {{ linearRegressionDaysToCrossing }} days after I started on June 24 which is on
      {{
        new Date(
          new Date('2021-06-24').getTime() + linearRegressionDaysToCrossing * 24 * 60 * 60 * 1000
        ).toDateString()
      }}. This method works by fitting a line of best fit to the data and then finding the x value where the line of
      best fit crosses the y value of 1,000,000.
    </p>
    <div class="mx-auto mt-4 h-64">
      <Scatter :data="linearRegressionData" :options="chartOptions" />
    </div>
    <p class="mt-4 text-center text-xl tracking-tighter md:text-2xl">Last View Increase Method</p>
    <p class="mt-2 text-center text-lg tracking-tighter md:text-lg">
      Using the last view increase, I can predict that I will reach 1,000,000 photo views
      {{ lastViewIncreaseDaysToCrossing }} days after I started on June 24 which is on
      {{
        new Date(
          new Date('2021-06-24').getTime() + lastViewIncreaseDaysToCrossing * 24 * 60 * 60 * 1000
        ).toDateString()
      }}. This method works by finding the last increase in views and then finding the x value where the slope of the
      line passes 1,000,000. The idea behind this method is that, because I add more photos over time, the most recent
      change in views will likely be the most accurate predictor of future views.
    </p>
    <div class="mx-auto mt-4 h-64">
      <Scatter :data="lastViewIncreaseData" :options="chartOptions" />
    </div>
    <BackButton />
  </div>
</template>

<script setup lang="ts">
import { Chart as ChartJS, LinearScale, PointElement, LineElement, Tooltip, Legend } from 'chart.js'
import { Scatter } from 'vue-chartjs'
import regression from 'regression'
import data from '~/assets/data/GoogleMaps/data.json'

ChartJS.register(LinearScale, PointElement, LineElement, Tooltip, Legend)

const currentViews = data[data.length - 1][1]
const actualViews = {
  label: 'Actual Views',
  fill: false,
  borderColor: '#f87979',
  backgroundColor: '#f87979',
  data: data.map(([x, y]) => ({ x, y }))
}
const chartOptions = {
  responsive: true,
  maintainAspectRatio: false
}

// Linear Regression
const linearRegression = regression.linear(data)

const linearRegressionDaysToCrossing = Math.ceil(
  (1000000 - linearRegression.equation[1]) / linearRegression.equation[0] - 1
)

const linearRegressionLineOfBestFit = Array.from({ length: linearRegressionDaysToCrossing + 1 }, (_, i) => ({
  x: i,
  y: linearRegression.equation[0] * i + linearRegression.equation[1]
}))

const linearRegressionData = {
  datasets: [
    actualViews,
    {
      label: 'Predicted Views',
      fill: false,
      borderColor: '#79f879',
      backgroundColor: '#79f879',
      data: linearRegressionLineOfBestFit,
      showLine: true
    }
  ]
}

// Last View Increase Method
const lastIncrease = data[data.length - 1][1] - data[data.length - 2][1]

const lastViewIncreaseDaysToCrossing =
  Math.ceil((1000000 - data[data.length - 1][1]) / lastIncrease) + data[data.length - 1][0] + 1

const lastViewIncreaseLineOfBestFitArray = [
  ...data,
  ...Array.from({ length: lastViewIncreaseDaysToCrossing - data[data.length - 1][0] }, (_, i) => [
    data[data.length - 1][0] + i,
    data[data.length - 1][1] + lastIncrease * i
  ])
]

const lastViewIncreaseLineOfBestFit = lastViewIncreaseLineOfBestFitArray.map(([x, y]) => ({ x, y }))

const lastViewIncreaseData = {
  datasets: [
    actualViews,
    {
      label: 'Predicted Views',
      fill: false,
      borderColor: '#79f879',
      backgroundColor: '#79f879',
      data: lastViewIncreaseLineOfBestFit,
      showLine: true
    }
  ]
}
</script>
