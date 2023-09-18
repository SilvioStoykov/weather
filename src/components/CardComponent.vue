<template>
    <div class="weather-card">
      <p id="city">{{ cityRender }}</p>
      <p id="temp">{{ tempRender}}</p>
      <!-- <p id="wind">Windy</p> -->
    </div>
</template>

<script setup>

</script>

<style lang="scss" scoped>
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