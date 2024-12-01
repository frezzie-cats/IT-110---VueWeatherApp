<template>
  <v-app>
    <v-container>
      <v-row justify="center">
        <v-col cols="12" md="6">
          <v-card class="pa-4 elevation-10" color="light-blue lighten-5">
            <v-card-title>
              <v-row justify="center" align="center" class="mb-4">
                <v-icon color="primary" class="mr-2">mdi-weather-cloudy</v-icon>
                <h2 class="text-center">Weather App</h2>
              </v-row>
            </v-card-title>

            <v-card-text>
              <v-text-field
                v-model="city"
                label="Enter city"
                @keyup.enter="addCity"
                outlined
                color="primary"
                prepend-inner-icon="mdi-city"
              ></v-text-field>

              <v-btn color="primary" @click="addCity" block>
                Add City
              </v-btn>

              <v-alert v-if="error" type="error" class="mt-4">{{ error }}</v-alert>

              <v-row v-for="cityWeather in weathers" :key="cityWeather.name" class="mt-4">
                <v-col>
                  <v-card outlined color="light-blue lighten-4">
                    <v-card-title class="text-h5">{{ cityWeather.name }}</v-card-title>
                    <v-card-subtitle class="pb-2">
                      {{ cityWeather.weather[0].description }}
                    </v-card-subtitle>
                    <v-divider></v-divider>
                    <v-card-text>
                      <v-row>
                        <v-col>
                          <p><strong>Temperature:</strong> {{ cityWeather.main.temp }} °C</p>
                          <p><strong>Humidity:</strong> {{ cityWeather.main.humidity }}%</p>
                          <p><strong>Pressure:</strong> {{ cityWeather.main.pressure }} hPa</p>
                          <p><strong>Wind Speed:</strong> {{ cityWeather.wind.speed }} m/s</p>
                          <p><strong>Sunrise:</strong> {{ convertTime(cityWeather.sys.sunrise) }}</p>
                          <p><strong>Sunset:</strong> {{ convertTime(cityWeather.sys.sunset) }}</p>
                        </v-col>
                        <v-col class="text-center">
                          <v-icon large color="primary">{{ weatherIcon(cityWeather) }}</v-icon>
                        </v-col>
                      </v-row>
                    </v-card-text>
                  </v-card>
                </v-col>
              </v-row>
            </v-card-text>
          </v-card>
        </v-col>
      </v-row>
    </v-container>
  </v-app>
</template>

<script>
export default {
  data() {
    return {
      city: '',
      weathers: [],
      error: ''
    };
  },
  methods: {
    convertTime(unixTime) {
      const date = new Date(unixTime * 1000);
      return date.toLocaleTimeString();
    },
    weatherIcon(weather) {
      const mainWeather = weather.weather[0].main.toLowerCase();
      if (mainWeather.includes('cloud')) return 'mdi-weather-cloudy';
      if (mainWeather.includes('rain')) return 'mdi-weather-rainy';
      if (mainWeather.includes('clear')) return 'mdi-weather-sunny';
      return 'mdi-weather-partly-cloudy';
    },
    async addCity() {
      if (this.city.trim() === '') {
        this.error = 'Please enter a city name.';
        return;
      }

      try {
        const apiKey = '35d0d6c62e35222a1b41ac6383ae5c7f'; // Replace with your OpenWeatherMap API key
        const response = await fetch(
          `https://api.openweathermap.org/data/2.5/weather?q=${this.city}&units=metric&appid=${apiKey}`
        );
        const data = await response.json();
        if (data.cod === 200) {
          this.weathers.push(data);
          this.city = '';
          this.error = '';
        } else {
          this.error = 'City not found.';
        }
      } catch (error) {
        this.error = 'Unable to fetch weather data.';
      }
    }
  }
};
</script>

<style>
body {
  font-family: 'Roboto', sans-serif;
}
</style>
