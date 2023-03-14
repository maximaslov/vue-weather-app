<script>
  import { defineComponent } from "vue";
  import axios from "axios";

  export default defineComponent({
    name: "Weather",
    data() {
      return {
        city: "",
        error: "",
        weather: null,
      };
    },
    methods: {
      getWeather() {
        if (this.city.trim().length < 3) {
          this.error = "Too few characters";
          this.city = "";
          this.weather = "";
          return false;
        } else {
          return axios
            .get(
              `https://api.openweathermap.org/data/2.5/weather?q=${this.city}&units=metric&appid=c40ad0a49abf7f15d606b0bce470a069`
            )
            .then((res) => (this.weather = res.data))
            .then(() => {
              this.city = "";
              this.error = "";
            })
            .catch((err) => {
              this.city = "";
              this.error = err.message;
            });
        }
      },
    },
    computed: {
      cityName() {
        return this.weather.name;
      },

      temp() {
        return this.weather.main.temp > 0
          ? `Temp: +${Math.round(this.weather.main.temp)} °C`
          : `Temp: ${Math.round(this.weather.main.temp)} °C`;
      },
      feelsLike() {
        return this.weather.main.feels_like > 0
          ? `Feels like: +${Math.round(this.weather.main.feels_like)} °C`
          : `Feels like: ${Math.round(this.weather.main.feels_like)} °C`;
      },

      description() {
        return this.weather.weather[0].description;
      },
    },
  });
</script>

<template>
  <div class="wrapper">
    <h1>Weather App</h1>
    <p class="app-description">Find out the weather in your city</p>
    <input type="text" v-model="city" placeholder="Enter city" />
    <button :disabled="city === ''" @click="getWeather">Get weather</button>
    <p class="error" v-if="error">{{ error }}</p>
    <div class="weather" v-if="weather">
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