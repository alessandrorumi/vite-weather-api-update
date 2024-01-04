<script>
export default {
  name: 'Weather',
  data() {
    return {
      city: '',
      weatherData: null,
      sunrise: '',
      sunset: '',
      isLoading: true
    }
  },
  methods: {
    searchWeather() {
      this.isLoading = true;
      const url = `https://api.openweathermap.org/data/2.5/weather?q=${this.city}&appid=dd6d85318caaec5bb7e1286a68c398ce&lang=it&units=metric`
      axios.get(url)
        .then((risposta) => {
          risposta.data.main.temp = parseFloat(risposta.data.main.temp.toFixed(1));
          risposta.data.main.temp_min = parseFloat(risposta.data.main.temp_min.toFixed(1));
          risposta.data.main.temp_max = parseFloat(risposta.data.main.temp_max.toFixed(1));
          risposta.data.wind.speed = parseFloat(risposta.data.wind.speed.toFixed(1));
          risposta.data.weather[0].description = risposta.data.weather[0].description.charAt(0).toUpperCase() + risposta.data.weather[0].description.slice(1);

          // Alba
          var sunriseTime = risposta.data.sys.sunrise;
          var date = new Date(sunriseTime * 1000);
          this.sunrise = date.toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' });

          // Tramonto
          var sunsetTime = risposta.data.sys.sunset;
          var date = new Date(sunsetTime * 1000);
          this.sunset = date.toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' });

          this.weatherData = risposta.data;

          this.isLoading = false;
        })
    }
  }
}
</script>

<template>
  <div class="container">
    <div class="location">
      <div class="search">
        <i class="fa-solid fa-map-location-dot"></i>
        <input type="text" placeholder="Località" v-model="city" @keyup.enter="searchWeather">
      </div>
      <div class="icon-search">
        <i class="fa-solid fa-magnifying-glass-location" @click="searchWeather"></i>
      </div>
    </div>
    <div class="forecast" v-if="weatherData">
      <h2>{{ weatherData.name }}, {{ weatherData.sys.country }}</h2>
      <div v-if="!isLoading">
        <div class="images">

          <!-- Sereno -->
          <img v-if="weatherData.weather[0].main === 'Clear'" src="../../public/weather/clear.png" alt="Clear Sky">

          <!-- Poche Nubi -->
          <img v-if="weatherData.weather[0].main === 'Clouds' && weatherData.clouds.all < 50"
            src="../../public/weather/few-clouds.png" alt="Few Clouds">

          <!-- Nuvoloso -->
          <img v-if="weatherData.weather[0].main === 'Clouds' && weatherData.clouds.all >= 50"
            src="../../public/weather/clouds.png" alt="Clouds">

          <!-- Pioggia Lieve -->
          <img
            v-if="weatherData.weather[0].description === 'Pioggia leggera' || weatherData.weather[0].description === 'Pioggerella'"
            src="../../public/weather/drizzle.png" alt="Light Rain">

          <!-- Pioggia Moderata -->
          <img v-if="weatherData.weather[0].description === 'Pioggia moderata'" src="../../public/weather/rain.png"
            alt="Rain">

          <!-- Pioggia Intensa -->
          <img
            v-if="weatherData.weather[0].description === 'Pioggia di forte intensità' || weatherData.weather[0].description === 'Pioggia molto forte' || weatherData.weather[0].description === 'Pioggia estrema'"
            src="../../public/weather/rain-2.png" alt="Heavy Rain">

          <!-- Neve -->
          <img v-if="weatherData.weather[0].main === 'Snow'" src="../../public/weather/snow.png" alt="Snow">

          <!-- Nebbia -->
          <img v-if="weatherData.weather[0].main === 'Mist' || weatherData.weather[0].main === 'Fog'"
            src="../../public/weather/mist.png" alt="Mist">

          <!-- Fulmini -->
          <img v-if="weatherData.weather[0].main === 'Thunderstorm'" src="../../public/weather/thunderstorm.png"
            alt="Mist">

        </div>
        <div class="info">
          <h1>{{ weatherData.main.temp }}°C</h1>
          <h2>{{ weatherData.weather[0].description }}</h2>
          <!-- Umidità e Velocità Vento -->
          <div class="humidity-wind">
            <!-- Umidità -->
            <div class="humidity">
              <i class="fa-solid fa-water"></i>
              <div class="text">
                <h3>{{ weatherData.main.humidity }}%</h3>
                <h5>Umidità</h5>
              </div>
            </div>
            <!-- Vento -->
            <div class="wind">
              <i class="fa-solid fa-wind"></i>
              <div class="text">
                <h3>{{ weatherData.wind.speed }} Km/h</h3>
                <h5>Vento</h5>
              </div>
            </div>
          </div>
          <!-- Temp minima e massima -->
          <div class="temp-min-max">
            <!-- Minima -->
            <div class="min">
              <i class="fa-solid fa-temperature-low"></i>
              <div class="text">
                <h3>{{ weatherData.main.temp_min }}°C</h3>
                <h5>Minima</h5>
              </div>
            </div>
            <!-- Massima -->
            <div class="max">
              <i class="fa-solid fa-temperature-high"></i>
              <div class="text">
                <h3>{{ weatherData.main.temp_max }}°C</h3>
                <h5>Massima</h5>
              </div>
            </div>
          </div>
          <!-- Alba e tramonto -->
          <div class="sunrise-sunset">
            <div class="sunrise">
              <i class="fa-solid fa-sun"></i>
              <div class="text">
                <h3>{{ sunrise }}</h3>
                <h5>Alba</h5>
              </div>
            </div>
            <div class="sunset">
              <i class="fa-solid fa-moon"></i>
              <div class="text">
                <h3>{{ sunset }}</h3>
                <h5>Tramonto</h5>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style lang="scss" scoped>
.container {
  background-color: whitesmoke;
  width: 300px;
  height: auto;
  margin: 3rem auto;
  padding: 2rem;
  border-radius: 10px;

  .location {
    display: flex;
    align-items: center;

    input {
      border: none;
      outline: none;
      background-color: whitesmoke;
      font-size: 18px;
    }

    .icon-search {
      .fa-magnifying-glass-location {
        padding: .5rem;
        border-radius: 50%;

        &:hover {
          cursor: pointer;
          background-color: rgb(230, 230, 230);
        }
      }
    }
  }

  .forecast {

    h2 {
      margin: 1rem 0 -1rem;
    }

    .images {
      margin: 0 0 -3rem;
    }

    .info {
      h2 {
        margin: .1rem 0;
      }

      &>div {
        display: flex;
        justify-content: space-evenly;
        margin: 2rem 0;

        &>div {
          display: flex;
        }
      }
    }
  }
}
</style>
