<template>
  <div id="app">
    <input
        v-model="city"
        type="text"
        autocomplete="off"
        class="mt-5 text-light"
        placeholder="Search a city"
        v-on:keydown.enter="getCurrentPosition"  />
    <h1 class="fw-bold mt-5 text-light">{{ city }}</h1>
    <h5 class="text-light">{{ currentDate }}</h5>
    <div class="temp">{{ temperature }} <span>°c</span></div>
    <h2 class=" fst-italic fw-bold text-light">Clouds</h2>
    <p class=" fw-bold fs-4 text-light " >1°c / 7°c</p>

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
      curr: false,
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
      const { latitude, longitude } = position.coords
      const apiUrl = `https://api.openweathermap.org/data/2.5/weather?lat=${latitude}&lon=${longitude}&units=metric&appid=${this.apiKey}`

      try {
        const response = await fetch(apiUrl)
        const data = await response.json()
        this.city = data.name
        this.temperature = Math.round(data.main.temp)
      } catch (error) {
        console.log('Error:', error)
      }
    },
  },
  //   getCurrentCity() {
  //     navigator.geolocation.getCurrentPosition(position => {
  //       const { latitude, longitude } = position.coords
  //       fetch(`https://api.opencagedata.com/geocode/v1/json?q=${latitude}+${longitude}&key=YOUR_API_KEY`)
  //           .then(response => response.json())
  //           .then(data => {
  //             this.currentCity = data.results[0].components.city
  //           })
  //           .catch(error => console.error(error))
  //     })
  //   },
  //   async searchCity() {
  //     const apiKey = 'aeab86d76c55d95e8376ad6c3aa3b990';
  //     const url = `https://api.openweathermap.org/data/2.5/weather?q=${this.city}&units=metric&appid=${apiKey}`;
  //
  //     try {
  //       const response = await fetch(url);
  //       const data = await response.json();
  //       this.weather = data;
  //     } catch (error) {
  //       console.error(error);
  //     }
  //   }
  // },
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

.temp span {
  font-weight: 400;
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
