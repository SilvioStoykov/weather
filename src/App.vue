
<script setup defer>
import { ref } from 'vue';
//calling the api with cityName being the value of the input
//fetching information about lon and lat from the city name
//fetching information about the weather from the lon and lat

let cityName = ref("")
let cityRender = ref("")
let countryRender = ref("")

let humidityRender = ref("")
let windSpeedRender = ref("")
let rainChanceRender = ref("")

let tempRender = ref("")
let showLabel = ref(false)

let city = document.querySelector('#city')
let temperature = document.querySelector('#temp')
let wind = document.querySelector('#wind')


function largestElement(arr) {
    return arr.reduce((largest, current) =>
        (current > largest ? current : largest), arr[0]);
}

function averageSum(arr){
  return arr.reduce((a, b) => a + b) / arr.length
}

const getGeo = async () => {
  const response = await fetch(`https://geocoding-api.open-meteo.com/v1/search?name=${cityName.value}`)
  const data = await response.json()
  let lat = data.results[0].latitude
  let lon = data.results[0].longitude
  console.log(data)
  if (response.status === 200){
    return {lat, lon, data}
  }else{
    showLabel.value = true
  }
  
  return console.error("No such city found")
}

async function getWeatherData (){
  const geoData = await getGeo()
  const response = await fetch (`https://api.open-meteo.com/v1/forecast?latitude=${geoData.lat}&longitude=${geoData.lon}&timezone=auto&daily=apparent_temperature_max&hourly=temperature_2m,apparent_temperature,precipitation_probability,windspeed_80m,relativehumidity_2m`)
  const data = await response.json()
  console.log(data)
  return data
}

async function weatherBuilder(){
  const geoData = await getGeo()
  const weatherData = await getWeatherData()
  cityRender.value = `${await geoData.data.results[0].name}`
  countryRender.value = `${await geoData.data.results[0].country}`
  tempRender.value = `${await weatherData.daily.apparent_temperature_max[0]}°C`
  let maxRain = await largestElement(weatherData.hourly.precipitation_probability)
  rainChanceRender.value = `${await maxRain}%`
  let windSpeed = await Math.floor(averageSum(weatherData.hourly.windspeed_80m))
  windSpeedRender.value = `${await windSpeed} km/h`
  let humidity = await Math.floor(averageSum(weatherData.hourly.relativehumidity_2m))
  humidityRender.value = `${await humidity}%`
}

</script>

<template>
  <link href="https://fonts.googleapis.com/css2?family=Titillium+Web:wght@300;700&display=swap" rel="stylesheet">
  <body>
    <div class="searchbar-wrapper">
      <input id="searchbar" type="text" v-model="cityName" @keyup.enter="weatherBuilder">
      <p v-if="showLabel" id="errLabel">No such city found.</p>
    </div>
  
    <div class="weather-card">
      <div class="main-info">
        <p id="city">{{ cityRender }}</p>
        <p id="country">{{ countryRender }}</p>
        <div class="temperature">
          <img class="weather-img" src="\src\assets\cloud-sun.png" alt="weather image"> <!--This will be a reactive string binded to a funciton output-->
          <p id="temp">{{ tempRender }}</p>
        </div>
      </div>

      <div class="secondary-info">
        <label id="humidity-label" for="humidityValue">Humidity</label>
        <p id="humidity-value">{{ humidityRender }}</p>

        <label id="wind-label" for="wind-value">Wind Speed</label>
        <p id="wind-value">{{windSpeedRender}}</p>

        <label id="rain-label" for="rain-value">Chacnes of rain</label>
        <p id="rain-value">{{ rainChanceRender }}</p>
      </div>   
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
  width: fit-content;
  height: fit-content;
  border-radius: 15px;
  background-color: rgba(255, 255, 255, 0.279);
  text-align: left;
  font-family: 'Titillium Web', sans-serif;
  margin: auto;
  padding: 10px;
  margin-top: 100px;
  display: grid;
  grid-template-columns: 1fr 1fr;
}

#city{
  font-size: 50px;
  margin-top: 3px;
  margin-bottom: .05px;
  font-weight:bolder;
  color: #fff;
  top: 100px;
  z-index: -1;
  margin-left: 8px;
}

#temp{
  font-size: 80px;
  text-align: center;
  color: #fff;
  font-weight: bold;
}

#wind{
  font-size: 35px
}

#errLabel{
  font-size: 25px;
}

#country{
  margin-top: .5px;
  font-size: 50px;
  font-weight: 100;
  color: #fff;
  margin-bottom: 0px;
  margin-left: 8px;
}

.weather-img{
  background-color: rgba(255, 255, 255, 0);
  width: 100px;
  height: 100px;
}

.temperature{
  display: flex;
  margin: 5px;
  margin-top: 0px;
  align-items: center;
}

.secondary-info{
  margin-left: 155px;
  margin-right: 0px;
  margin-top: 55px;
}

#humidity-label{
  font-size: 25px;
  font-weight: 600;
}

#humidity-value{
  margin-top: 5px;
  font-size: 25px;
  color: #fff;
}


#wind-label{
  font-size: 25px;
  font-weight: 600;
}

#wind-value{
  margin-top: 5px;
  font-size: 25px;
  color: #fff;
}

#rain-label{
  font-size: 25px;
  font-weight: 600;
}

#rain-value{
  margin-top: 5px;
  font-size: 25px;
  color: #fff;
}
</style>
