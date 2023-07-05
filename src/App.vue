<template>
  <div id="app">
    <input
        v-model="city"
        type="text"
        autocomplete="off"
        class="mt-5 text-light"
        placeholder="Search a city"
        v-on:keydown.enter="getWeatherDataByCity"/>
    <button class="btn btn-outline-secondary d-block my-4 m-auto py-2 px-4" @click="getWeatherDataByCity">Search
    </button>
    <h1 class="fw-bold mt-5 text-light">{{ city }}</h1>
    <h5 class="text-light">{{ currentDate }}</h5>
    <div class="temp">{{ temperature }} <span>°c</span></div>
    <h2 class="shadow w-25 m-auto my-3 fst-italic fw-bold text-dark">Humidity: {{ humidity }}%</h2>
    <h2 class="shadow w-25 m-auto fst-italic fw-bold text-dark">Pressure: {{ pressure }} hPa</h2>
    <div v-show="showAlert" class="overlay">
      <transition name="fade">
        <div class="alert alert-danger py-4"
             role="alert">
          We couldn't find the location you searched.
        </div>
      </transition>
    </div>
  </div>
</template>

<script>

export default {
  name: 'App',
  data() {
    return {
      currentDate: '',
      city: '',
      image: require("@/assets/image.jpg"),
      apiKey: process.env.VUE_APP_API_KEY,
      temperature: null,
      humidity: null,
      pressure: null,
      showAlert: false,
    }
  },
  mounted() {
    let date = new Date();
    this.currentDate = date.toDateString();
    this.getCurrentPosition();
  },
  methods: {
    getCurrentPosition() {
      console.log(this.apiKey)
      if (navigator.geolocation) {
        navigator.geolocation.getCurrentPosition(this.getWeatherData)
      } else {
        console.log('Geolocation is not supported by this browser.')
      }
    },
    async getWeatherData(position) {
      const {latitude, longitude} = position.coords
      const apiUrl = `https://api.openweathermap.org/data/2.5/weather?lat=${latitude}&lon=${longitude}&units=metric&appid=${this.apiKey}`
      try {
        const response = await fetch(apiUrl)
        const data = await response.json()
        this.city = data.name
        this.temperature = Math.round(data.main.temp)
        this.humidity = Math.round(data.main.humidity)
        this.pressure = Math.round(data.main.pressure)
        console.log(data.main)
      } catch (error) {
        console.log('Error:', error)
      }
    },
    async getWeatherDataByCity() {
      const apiUrl = `https://api.openweathermap.org/data/2.5/weather?q=${this.city}&units=metric&appid=${this.apiKey}`;
      try {
        const response = await fetch(apiUrl);
        const data = await response.json();
        this.temperature = Math.round(data.main.temp);
        this.humidity = Math.round(data.main.humidity)
        this.pressure = Math.round(data.main.pressure)
      } catch (error) {
        console.log('Error:', error);
        this.temperature = null;
        this.humidity = null;
        this.pressure = null;
        this.showAlert = true;
        let clear = () => (this.showAlert = false)
        if (this.showAlert) {
          setTimeout(clear, 3000);
        }
      }
    },
  },
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  background-image: url("@/assets/image.jpg");
}

html {
  background-image: url("@/assets/image.jpg");
}

.temp {
  color: white;
  font-size: 102px;
  font-weight: 900;
  margin: 20px 0px;
  text-shadow: 2px 10px rgba(14, 1, 1, 0.81);
}

.alert {
  position: absolute;
  top: 35%;
  left: 32%;
  width: 36%;
  height: max-content;
  z-index: 2;
}

.temp span {
  font-weight: 400;
}

.overlay {
  width: 100%;
  border-bottom: solid 1px #333;
  display: grid;
  background-color: #a2a2a2;
  position: fixed;
  top: 0;
  left: 0;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  z-index: 1;
}

input {
  width: 100%;
  max-width: 280px;
  padding: 10px 15px;
  border: none;
  outline: none;
  background-color: rgba(137, 137, 133, 0.4);
  border-radius: 16px;
  border-bottom: 2px solid #eec009;
  font-size: 20px;
  transition: 0.2s ease-out;
}

input:focus {
  background-color: rgba(255, 255, 255, 0.3);
}
</style>
