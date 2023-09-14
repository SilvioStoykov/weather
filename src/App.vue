
<script setup defer>
import { ref } from 'vue';
//calling the api with cityName being the value of the input
//fetching information about lon and lat from the city name
//fetching information about the weather from the lon and lat

let cityName = ref("")
let cityRender = ref("")
let tempRender = ref("")

let city = document.querySelector('#city')
let temperature = document.querySelector('#temp')
let wind = document.querySelector('#wind')


const getGeo = async () => {
  const response = await fetch(`https://geocoding-api.open-meteo.com/v1/search?name=${cityName.value}`)
  const data = await response.json()
  let lat = data.results[0].latitude
  let lon = data.results[0].longitude
  console.log(data)
  return {lat, lon, data}
}

async function getWeatherData (){
  const geoData = await getGeo()
  const response = await fetch (`https://api.open-meteo.com/v1/forecast?latitude=${geoData.lat}&longitude=${geoData.lon}&timezone=auto&daily=apparent_temperature_max`)
  const data = await response.json()
  console.log(data)
  return data
}

async function weatherBuilder(){
  const geoData = await getGeo()
  const weatherData = await getWeatherData()
  cityRender.value = `${await geoData.data.results[0].name}` + `, ` + `${await geoData.data.results[0].country}`
  tempRender.value = `${await weatherData.daily.apparent_temperature_max[0]} °C` 
}

</script>

<template>
  <link href="https://fonts.googleapis.com/css2?family=Titillium+Web:wght@300;700&display=swap" rel="stylesheet">
  <body>
    <div class="searchbar-wrapper">
      <input id="searchbar" type="text" v-model="cityName" @keyup.enter="weatherBuilder">
    </div>
  
    <div class="weather-card">
      <p id="city">{{ cityRender }}</p>
      <p id="temp">{{ tempRender}}</p>
      <!-- <p id="wind">Windy</p> -->
    </div>
  </body>
  </template>


<style scoped>

.searchbar-wrapper{
  margin: auto;
  text-align: center;
  margin-top: 40px;
}

#searchbar{
  border-style: none;
  border-radius: 15px;

  width: 600px;
  height: 30px;
  padding: 5px;

  font-family: 'Titillium Web', sans-serif;
  font-weight: 500;

  opacity: 75%;
}

#searchbar:focus{
  outline: none;
}
.weather-card{
  width: 500px;
  height: fit-content;
  border-radius: 15px;
  opacity: 75%;
  background-color: rgba(255, 255, 255, 0.797);
  text-align: center;
  font-family: 'Titillium Web', sans-serif;
  margin: auto;
  padding: 10px;
  margin-top: 100px;
}

#city{
  font-size: 50px;
  margin-top: 15px;
}

#temp{
  font-size: 80px;
}

#wind{
  font-size: 35px
}
</style>
