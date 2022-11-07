<template>
  <div id="app">
    <main>
      <select class="search-box" v-model="selectCity" @change="dataWeather">
        <option
          v-bind:value="cities.capital"
          v-for="cities of city"
          :key="cities.id"
        >
          {{ cities.name }}
        </option>
      </select>
      <div
        class="weather__wrapper"
        :class="{ Hot: hotWeather, cold: coldWeather }"
      >
        <div class="block__first">
          <p class="citySelected">{{ selectCity }}</p>
          <p class="temperature">{{ temperature }}</p>
        </div>
        <div class="block__second">
          <p class="wind">{{ wind }}</p>
          <p class="description">{{ description }}</p>
        </div>
      </div>
    </main>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "app",
  data() {
    return {
      weather: [],
      hotWeather: false,
      coldWeather: false,
      city: [],
      temperature: "",
      wind: "",
      description: "",
    };
    
  },

  mounted() {
    this.getCountry();
    this.dataWeather();
  },

  methods: {
    async getCountry() {
      const response = await axios.get(
        `https://restcountries.com/v2/all?fields=name,capital`
      );
      this.city = response.data;
    },

    async dataWeather() {
      const res = await axios.get(
        `http://api.openweathermap.org/data/2.5/weather?q=${this.selectCity}&units=metric&APPID=5e425b2d5954bd1f8a2d5eb116e4f4a5`
      );
      this.weather = res;
      
      //изменение цвета взависимости от температуры
      if (Math.round(this.weather.data.main.temp) > 20) {
        this.hotWeather = true;
        this.coldWeather = false;
      } else if (Math.round(this.weather.data.main.temp) < 0) {
        this.coldWeather = true;
        this.hotWeather = false;
      } else {
        this.coldWeather = false;
        this.hotWeather = false;
      }
      // Для удобства
      this.temperature = Math.round(this.weather.data.main.temp) + " °C";
      this.wind = this.weather.data.wind.speed + " м/с";
      this.description = this.weather.data.weather[0].description;
    },
  },
};
</script>

<style>
.weather__wrapper {
  width: 300px;
  height: 300px;
  text-align: center;
  margin: 15% auto;
  border: 1px solid black;
  border-radius: 10px;
}
.search-box {
  display: flex;
  justify-content: center;
  margin: 0 auto;
}
select {
  width: 300px;
  background: -webkit-linear-gradient(top, #c8ffc4, #afb8ff);
  border: 2px solid #999;
  border-radius: 3px;
  box-shadow: 0 0 5px #999;
  color: #111;
  font-size: 15px;
  padding: 10px;
}
.block__first {
  font-weight: 800;
  font-size: 28px;
  margin: 60px 0 30px;
}

.block__second {
  font-size: 18px;
}
.Hot {
  background: #ffffcc;
}
.cold {
  background: #ccffff;
}
</style>

