<template>
  <q-page class="flex column" :class="bgClass">
    <div class="col q-pt-lg q-px-md" >
      <q-input
        v-model="search"
        placeholder="Search"
        @keyup.enter="getWeatherBySearch"
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
          <q-btn
          @click="getWeatherBySearch"
            round
            dense
            flat
            icon="search" 
            />
        </template>
      </q-input>
    </div>

    <template v-if="weatherData">
      <div class="col text-white text-center">
        <div class="text-h4 text-weight-light">
          {{weatherData.name}}
        </div>
        <div class="text-h6 text-weight-light">
          {{weatherData.weather[0].main}}
        </div>
        <div class="text-h1 text-weight-thin q-my-lg relative-position">
          <span>{{Math.round(weatherData.main.temp)}}</span>
          <span class="text-h4 relative-position degree">&deg;C</span>
        </div>
      </div>
      <div class="div col text-center">
        <img :src="`http://openweathermap.org/img/wn/${weatherData.weather[0].icon}@2x.png`">
      </div>
    </template>
    <template v-else>
      <div class="col column text-center text-white">
        <div class="col text-h2 text-weight-thin">
          Quasar <br> Weather
        </div>
        <q-btn class="col" flat @click="getLocation">
          <q-icon
            left
            size="3em"
            name="my_location" 
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

import { defineComponent, ref, computed } from 'vue'
import axios from 'axios'
import { useQuasar } from 'quasar'

export default defineComponent({
  name: 'IndexPage',

  setup () {
    const $q = useQuasar()
    const search = ref('')
    const weatherData = ref(null)
    const lat = ref(null)
    const lon = ref(null)
    const apiKey = ref('52e8e4553c2d6e62aea2c8e174025ad7')
    const baseUrl = ref('https://api.openweathermap.org/data/2.5/weather')
    const getLocation = () => {
      $q.loading.show()
      navigator.geolocation.getCurrentPosition(position => {
        console.log('position:', position)
        lat.value = position.coords.latitude
        lon.value = position.coords.longitude
        getWeatherByCoords()
        $q.loading.hide()
      })
    }
    const getWeatherByCoords = () => {
      $q.loading.show()
      axios(`${baseUrl.value}?lat=${lat.value}&lon=${lon.value}&appid=${apiKey.value}&units=metric`)
      .then(response => {
        weatherData.value = response.data
        $q.loading.hide()
      })
    }
    const getWeatherBySearch = () => {
      $q.loading.show()
      axios(`${baseUrl.value}?q=${ search.value }&appid=${apiKey.value}&units=metric`)
      .then(response => {
        weatherData.value = response.data
        search.value = ''
        $q.loading.hide()
      })
    }
    const bgClass = computed(() => {
      if (weatherData.value){
        if (weatherData.value.weather[0].icon.endsWith('n')){
          return 'bg-night'
        }
        else {
          return 'bg-day'
        }
      }
    })
    return { search, weatherData, getLocation, getWeatherByCoords, getWeatherBySearch, bgClass }
  } 

})



</script>

<style lang="sass">
  .q-page
    background: linear-gradient(to bottom, #136a8a, #267871)
    &.bg-night
      background: linear-gradient(to bottom, #232526, #414345)
    &.bg-day
      background: linear-gradient(to bottom, #00b4db, #0083b0)
  .degree
    top: -45px
  .skyline
    flex: 0 0 100px
    background: url(../assets/dubiaskyline.png)
    background-size: contain
    background-position: center bottom
</style>
