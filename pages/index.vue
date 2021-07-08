<template>
  <div
    class="
      xl:h-screen
      lg:h-full
      h-full
      xl:w-screen
      lg:w-full
      w-full
      xl:py-0
      lg:py-5
      py-5
    "
  >
    <div
      id="mainContainer"
      class="h-full w-full flex items-center justify-center"
    >
      <div class="h-4/5 w-5/6 border-solid border-4 border-light-blue-500">
        <div
          class="
            w-full
            xl:h-3/5
            lg:h-auto
            h-auto
            border-solid border-4 border-light-blue-500
          "
        >
          <Dashboard :days="days" :current-data="loadedDataToDashboard" />
        </div>

        <div class="w-full h-2/5 border-solid border-4 border-light-blue-500">
          <DayPicker
            :days="days"
            :forecasted-data="forecastedData"
            @loadToDashboard="loadToDashboard"
          />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import moment from "moment";

export default {
  data() {
    return {
      apiKey: "5f27e6de016a45e5d58cf3cf874bd3be",
      days: [
        "Sunday",
        "Monday",
        "Tuesday",
        "Wednesday",
        "Thursday",
        "Friday",
        "Saturday",
      ],
      geoLocation: {},
      currentData: {},
      forecastedData: {},
      loadedDataToDashboard: {},
    };
  },
  async mounted() {
    this.getData();
  },
  methods: {
    async getLocation() {
      const pos = await new Promise((resolve, reject) => {
        navigator.geolocation.getCurrentPosition(resolve, reject);
      });

      return {
        long: pos.coords.longitude,
        lat: pos.coords.latitude,
      };
    },
    getData() {
      this.getLocation().then(async (res) => {
        this.geoLocation = res;

        const currentDataApiResponse = await this.$axios({
          method: "get",
          baseURL: "https://api.openweathermap.org",
          url: "data/2.5/weather",
          params: {
            appid: this.apiKey,
            lat: res.lat,
            lon: res.long,
            units: "metric",
          },
        });

        const forecastedDataApiResponse = await this.$axios({
          method: "get",
          baseURL: "https://api.openweathermap.org",
          url: "data/2.5/onecall",
          params: {
            appid: this.apiKey,
            lat: res.lat,
            lon: res.long,
            exclude: "minutely,hourly,alerts",
            units: "metric",
          },
        });

        this.currentData = currentDataApiResponse.data;
        this.loadedDataToDashboard = {
          dt: this.currentData.dt,
          name: `${this.currentData.name}, ${this.currentData.sys.country}`,
          weather: this.currentData.weather,
          feels_like: this.currentData.main.feels_like,
          temp_max: this.currentData.main.temp_max,
          temp_min: this.currentData.main.temp_min,
          pressure: this.currentData.main.pressure,
          humidity: this.currentData.main.humidity,
          wind: this.currentData.wind.speed,
        };
        this.forecastedData = forecastedDataApiResponse.data.daily.slice(0, 5);
      });
    },
    loadToDashboard({ dayIndex, dataToLoad }) {
      if (dayIndex == 0) {
        this.loadedDataToDashboard = {
          dt: this.currentData.dt,
          name: `${this.currentData.name}, ${this.currentData.sys.country}`,
          weather: this.currentData.weather,
          feels_like: this.currentData.main.feels_like,
          temp_max: this.currentData.main.temp_max,
          temp_min: this.currentData.main.temp_min,
          pressure: this.currentData.main.pressure,
          humidity: this.currentData.main.humidity,
          wind: this.currentData.wind.speed,
        };
      } else {
        this.loadedDataToDashboard = {
          dt: dataToLoad.dt,
          weather: dataToLoad.weather,
          feels_like: ((dataToLoad.temp.min + dataToLoad.temp.max) / 2).toFixed(
            2
          ),
          temp_max: dataToLoad.temp.max,
          temp_min: dataToLoad.temp.min,
          pressure: dataToLoad.pressure,
          humidity: dataToLoad.humidity,
          wind: dataToLoad.wind_speed,
        };
      }
    },
  },
};
</script>