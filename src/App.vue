<template>
  <div class="container" :class="isDay ? 'night' : 'day'">
    <img src="./assets/Weather-icon.png" alt="" />
    <div class="horloge">
      <div style="text-align:center;padding:1em 0;">
        <h3>
          <a
            style="text-decoration:none;"
            href="https://www.zeitverschiebung.net/fr/city/3031582"
            ><span style="color:gray;">Heure actuelle</span><br />Bordeaux,
            France</a
          >
        </h3>
        <iframe
          src="https://www.zeitverschiebung.net/clock-widget-iframe-v2?language=fr&size=medium&timezone=Europe%2FParis"
          width="100%"
          height="115"
          frameborder="0"
          seamless
        ></iframe>
      </div>
    </div>
    <form class="search-location" @submit.prevent="getWeather">
      <input type="text" placeholder="type a city" v-model="citySearch" />
    </form>
    <div class="not-found" v-if="!searchResult">
      <p>city not found</p>
    </div>
    <header class="localisation-info" v-if="searchResult">
      <h1>{{ weather.cityName }}</h1>
      <p>{{ weather.country }}</p>
    </header>
    <section class="details" v-if="searchResult">
      <h2 class="temp">{{ weather.temperature }}°C</h2>
      <p class="description">At your window :{{ weather.description }}</p>
      <p class="lowTemp">lowest temperature :{{ weather.lowTemp }}</p>
      <p class="highTemp">Highest temperature :{{ weather.highTemp }}</p>
      <p class="humidity">Air humidity :{{ weather.humidity }}%</p>
      <p class="feels_like">temperature feeled :{{ weather.feelsLike }}</p>
    </section>
  </div>
</template>

<script>
export default {
  name: "App",
  components: {},
  data() {
    return {
      isDay: true,
      searchResult: false,
      cityDisplay: false,
      citySearch: "",
      weather: {
        cityName: "Bordeaux",
        country: "FR",
        temperature: 9,
        description: "Cloudy with a chance of meatballs",
        lowTemp: 0,
        highTemp: 15,
        feelsLike: "Death",
        humidity: 55,
      },
    };
  },
  methods: {
    getWeather: async function() {
      console.log(this.citySearch);
      const key = "deb94c66b660a028475b4c55b068dc12";
      const callURL = `https://api.openweathermap.org/data/2.5/weather?q=${this.citySearch}&appid=${key}&units=metric`;

      console.log("Ouais ouais ouais !");

      // appel a l'api avec un await dans un try catch
      try {
        const response = await fetch(callURL);
        const data = await response.json();
        this.citySearch = "";
        this.weather.cityName = data.name;
        this.weather.country = data.sys.country;
        this.weather.temperature = Math.round(data.main.temp);
        this.weather.description = data.weather[0].description;
        this.weather.lowTemp = Math.round(data.main.temp_min);
        this.weather.highTemp = Math.round(data.main.temp_max);
        this.weather.feelsLike = Math.round(data.main.feels_like);
        this.weather.humidity = Math.round(data.main.humidity);

        this.searchResult = true;
        console.log(data);

        const dayNight = data.weather[0].icon;
        if (dayNight.includes("d")) {
          this.isDay = false;
        } else {
          this.isDay = true;
        }
      } catch (error) {
        console.log(error);
        this.searchResult = false;
      }
    },
  },
};
</script>

<style>
body {
  background-image: url(./assets/misty-mountain.jpg);
  height: 100vh;
  background-repeat: no-repeat;
  background-size: cover;
  display: flex;
  justify-content: center;
}
.horloge {
  margin: 10px;
}
.container {
  background-color: white;
  border-radius: 30px;
  width: 70vh;
  height: 90vh;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}
img {
  width: 5vw;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.night {
  background-color: black;
  color: white;
}
</style>
