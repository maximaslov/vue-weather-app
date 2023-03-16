<script>
import { ref, computed } from "vue";
import axios from "axios";

export default {
  name: "Weather",
  setup() {
    const city = ref("");
    const error = ref("");
    const weather = ref(null);

    const getWeather = () => {
      if (city.value.trim().length < 3) {
        error.value = "Too few characters";
        city.value = "";
        weather.value = "";
        return false;
      } else {
        return axios.get(`https://api.openweathermap.org/data/2.5/weather?q=${city.value}&units=metric&appid=c40ad0a49abf7f15d606b0bce470a069`)
        .then((res)  => {
          weather.value = res.data;
          city.value = "";
          error.value = "";
        })
        .catch ((err) => {
          city.value = "";
          error.value = err.message;
        });
      }
    };

    const cityName = computed(() => {
      return weather.value ? weather.value.name : "";
    });

    const temp = computed(() => {
      return  weather.value.main.temp > 0
          ? `Temp: +${Math.round(weather.value.main.temp)} °C`
          : `Temp: ${Math.round(weather.value.main.temp)} °C`
    });

    const feelsLike = computed(() => {
      return weather.value.main.feels_like > 0
          ? `Feels like: +${Math.round(weather.value.main.feels_like)} °C`
          : `Feels like: ${Math.round(weather.value.main.feels_like)} °C`
    });

    const description = computed(() => {
      return weather.value ? weather.value.weather[0].description : "";
    });

    return { city, error, weather, getWeather, cityName, temp, feelsLike, description };
  },
};
</script>

<template>
  <div class="wrapper">
    <h1>Weather App</h1>
    <p class="app-description">Find out the weather in your city</p>
    <input type="text" v-model="city" placeholder="Enter city" />
    <button :disabled="city === ''" @click="getWeather">Get weather</button>
    <p class="error" v-if="error">{{ error }}</p>
    <div class="weather" v-if="weather && !error">
      <p>{{ cityName }}</p>
      <p>{{ temp }}</p>
      <p>{{ feelsLike }}</p>
      <p>{{ description }}</p>
    </div>
  </div>
</template>

<style scoped>
  .wrapper {
    width: 900px;
    height: 500px;
    border-radius: 50px;
    background: rgb(4, 21, 79);
    background: linear-gradient(
      90deg,
      rgba(4, 21, 79, 1) 0%,
      rgba(1, 1, 1, 0.9009978991596639) 68%,
      rgba(0, 0, 0, 1) 100%
    );
    padding: 20px;
    text-align: center;
    color: white;
  }
  .wrapper h1 {
    font-size: 40px;
  }
  .app-description {
    font-size: 20px;
    margin-bottom: 20px;
  }
  .wrapper input {
    margin: 30px 0 30px 0;
    background: transparent;
    border: none;
    border-bottom: 2px solid blueviolet;
    font-size: 20px;
    padding: 5px 8px;
    outline: none;
    color: white;
  }
  .wrapper input:focus {
    border-bottom-color: #f6eef8;
  }
  .wrapper button {
    background-color: #e3bc4b;
    color: fff;
    border-radius: 10px;
    border: solid 2px #b99935;
    padding: 10px 15px;
    cursor: pointer;
    transition: transform 500ms ease;
    margin-left: 20px;
  }
  .wrapper button:hover {
    transform: scale(1.1) translateY(-5px);
  }
  .wrapper button:disabled {
    cursor: not-allowed;
  }
  .wrapper button:disabled:hover {
    transform: none;
  }
  .error {
    color: red;
    font-size: 20px;
  }

  .weather p {
    font-size: 25px;
    margin-bottom: 10px;
  }
</style>