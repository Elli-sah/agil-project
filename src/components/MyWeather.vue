<script>
  import axios from 'axios'
  export default {
    created() {
      this.getLocation()
    },

    data() {
      return {
        temp: null,
        weather: null,
        img: null
      }
    },

    mounted() {
      this.getLocation()
    },
    methods: {
      async getLocation() {
        try {
          navigator.geolocation.getCurrentPosition(({ coords }) => {
            const { latitude, longitude } = coords
            this.getWeatherData(latitude, longitude)
          })
        } catch (error) {
          console.log(error)
        }
      },
      async getWeatherData(latitude, longitude) {
        try {
          const response = await axios.get(
            `https://api.openweathermap.org/data/2.5/weather?lat=${latitude}&lon=${longitude}&lang=sv,se&appid=522a9f3cd92ded5c0f211fd40fe17e5b`
          )

          this.weather = response.data

          const fahrenheit = this.weather.main.temp
          const celsius = fahrenheit - 273.15
          this.temp = celsius.toFixed(1)
          this.img = this.weather.weather[0].icon
          console.log(this.weather)
        } catch (error) {
          console.log(error)
        }
      }
    }
  }
</script>

<template>
  <div v-if="img" id="weather-container">
    <p id="temp">{{ temp }}℃</p>
    <img :src="`http://openweathermap.org/img/wn/${img}.png`" />
  </div>
  <transition class="slide-in-enter-active" name="slide-in">
    <div v-if="temp !== null" ref="box">
      <p class="weather" v-if="temp > 15 && img === '01d'">
        Temperaturen hos dig är över 15℃ och sol... tänk på att vattna dina
        växter, och skydda dom från direkt solljus då!
      </p>
      <p class="weather" v-if="temp < 5">
        Temperaturen hos dig är under 5℃... tänk på att skydda dina växter från
        kalla luftdrag!
      </p>
    </div>
  </transition>
</template>

<style scoped>
  #weather-container {
    width: 50%;
    position: absolute;
    right: 0px;
    top: 65px;
    z-index: 0;
    padding: 5px 10px 5px 40px;
    display: flex;
    align-items: center;
    justify-content: end;
  }
  .weather {
    margin: auto;
    display: flex;
    justify-content: center;
    align-items: center;
    text-align: center;
    width: 500px;
    padding: 10px;
    background-color: white;
    border-radius: 20px;
    height: 100px;
  }

  p {
    margin: 0 5px;
    padding: 10;
    max-width: 60%;
  }
  img {
    width: 40px;
    height: 40px;
  }

  .slide-in-enter-active {
    animation: slide-in 1s forwards;
  }

  @keyframes slide-in {
    from {
      transform: translateX(200%);
    }
    to {
      transform: translateX(0);
    }
  }

  @media (min-width: 992px) {
    #weather-container {
      top: 100px;
      right: 0;
      width: 400px;
      z-index: 0;
    }
    #weather {
      top: 150px;
    }
    img {
      width: 50px;
      height: 50px;
    }
  }
</style>
