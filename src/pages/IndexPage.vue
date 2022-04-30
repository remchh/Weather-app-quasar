<template>
  <q-page class="flex column">
    <div class="col q-pt-lg q-px-md">
      <q-input
        v-model="search"
        placeholder="Search"
        dark
        borderless
        >
        <template v-slot:before>
          <q-icon
            name="my_location" 
            @click="getLocation"
          />
        </template>

        <template v-slot:append>
          <q-btn round dense flat icon="search" />
        </template>
      </q-input>
    </div>

    <template v-if="weatherData">
      <div class="col text-white text-center">
        <div class="text-h4 text-weight-light">
          San Pedro Sula
        </div>
        <div class="text-h6 text-weight-light">
          Sunny
        </div>
        <div class="text-h1 text-weight-thin q-my-lg relative-position">
          <span>23</span>
          <span class="text-h4 relative-position degree">&deg;</span>
        </div>
      </div>
      <div class="div col text-center">
        <img src="https://www.fillmurray.com/100/100" alt="Bill">
      </div>
    </template>
    <template v-else>
      <div class="col column text-center text-white">
        <div class="col text-h2 text-weight-thin">
          Quasar <br> Weather
        </div>
        <q-btn class="col" flat>
          <q-icon
            left
            size="3em"
            name="my_location" 
            @click="getLocation"
          />
          <div>Find my location</div>
        </q-btn>
      </div>
    </template>
    <div class="col skyline">
    </div>
  </q-page>
</template>

<script>

import { defineComponent, ref } from 'vue'
import axios from 'axios'

export default defineComponent({
  name: 'IndexPage',

  setup () {
    const search = ref('')
    const weatherData = ref(null)
    const lat = ref(null)
    const lon = ref(null)
    const apiKey = ref('52e8e4553c2d6e62aea2c8e174025ad7')
    const baseUrl = ref('https://api.openweathermap.org/data/2.5/weather')
    const getLocation = () => {
      navigator.geolocation.getCurrentPosition(position => {
        console.log('position:', position)
        lat.value = position.coords.latitude
        lon.value = position.coords.longitude
        getWeatherByCoords()
      })
    }
    const getWeatherByCoords = () => {
      axios.get(`${baseUrl.value}?lat=${lat.value}&lon=${lon.value}&appid=${apiKey.value}&units=metric`).then(response => {
        console.log('response:', response)
      })
    }
    return { search, weatherData, getLocation, getWeatherByCoords }
  } 

})



</script>

<style lang="sass">
  .q-page
    background: linear-gradient(to bottom, #136a8a, #267871)
  .degree
    top: -45px
  .skyline
    flex: 0 0 100px
    background: url(../assets/dubiaskyline.png)
    background-size: contain
    background-position: center bottom
</style>
