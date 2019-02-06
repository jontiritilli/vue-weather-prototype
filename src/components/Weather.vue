<template>
  <div class="container">
    <b-jumbotron>
      <template slot="header">
        Worth a Wash??
      </template>
      <template slot="lead">
        Let's see if you should wash that car of yours. Enter you zip and click get weather to see if there's rain in the forecast
      </template>
      <hr class="my-4">
      <b-form @submit.prevent='handleSubmit'>
        <b-input-group>
          <b-form-input id='zip-input' size='lg' type="text" maxlength=5 v-model='zip' placeholder='Enter Zip' />
          <b-input-group-append>
            <b-btn type='submit' v-bind:class="{ disabled: !isValidZip }" variant='success' size='lg'>Get Weather</b-btn>
          </b-input-group-append>
        </b-input-group>
      </b-form>
      <hr class="my-4">
      <div class="col" v-if="!forecastedRain.length && dataReceived">
        <h1 class='display-4'>Go ahead and wash your car</h1>
        <h3>It's NOT gonna rain</h3>
      </div>
      <div class="col" v-if="forecastedRain.length">
        <h1 class='display-4'>Don't wash your car</h1>
        <h3>It's definitely gonna rain</h3>
      </div>
    </b-jumbotron>
  </div>
</template>

<script>

import axios from 'axios';
import config from '../config'

const { weatherApiKey } = config;

export default {
  computed: {
    isValidZip() {
      return this.zip.length === 5 ? true : false;
    }
  },
  data() {
    return {
      dataReceived: false,
      zip: '',
      weatherData: {},
      currentWeather: {},
      forecastedRain: [],
      city: {},
    }
  },
  name: 'Weather',
  props: {
    msg: String
  },
  methods: {
    handleSubmit() {
      if (this.zip.length < 5) return;
      const requestString = `https://api.openweathermap.org/data/2.5/forecast?zip=${this.zip}&cluster=yes&format=json&units=imperial&APPID=${weatherApiKey}`;
      // get the weather data and save the components we need
      axios.get(requestString)
        .then(res => {
          this.dataReceived = true;
          this.city = res.data.city;
          this.currentWeather = res.data.list[0];
          this.weatherData = res.data.list;
          this.forecastedRain = res.data.list.map(d => d.weather).filter(w => w[0].main === "Rain")
        })
        .catch(err => {
          // eslint-disable-next-line
          console.log(err);
        })
      }
  }
}
</script>

<style scoped>
.form {
  width: 40%;
  margin: auto 0;
}
</style>
