<template>
  <div class="flex flex-col xl:flex-row lg:flex-col w-full h-full">
    <div class="flex xl:w-1/3 lg:w-full w-full p-5">
      <div class="flex flex-col w-full">
        <div class="flex item-center justify-center h-2/3">
          <img id="weatherIconImg" src="" />
        </div>

        <div class="flex items-center justify-center h-1/3">
          <div class="lg:text-6xl md:text-4xl text-4xl font-thin">
            {{ currentData.weather ? currentData.feels_like : "0" }} °C
          </div>
        </div>
      </div>
    </div>

    <div class="xl:w-2/3 lg:w-full w-full p-5">
      <div class="flex flex-col w-full h-full border">
        <div
          class="
            flex flex-col
            items-center
            justify-center
            w-full
            h-1/2
            px-2
            xl:py-0
            lg:py-2
            py-2
            border
          "
        >
          <div class="text-center lg:text-2xl md:text-xl font-light">
            {{ location }}
          </div>
          <div class="text-center lg:text-2xl md:text-xl font-light">
            {{ day }}
          </div>
          <div class="text-center lg:text-2xl md:text-xl font-light">
            {{
              currentData.weather
                ? `${currentData.weather[0].main} - (${currentData.weather[0].description})`
                : ""
            }}
          </div>
        </div>

        <div class="flex w-full h-1/2 border">
          <div
            class="
              flex flex-col
              justify-center
              w-1/3
              h-full
              xl:py-0
              lg:py-2
              py-2
              border
            "
          >
            <div class="w-full h-full py-4 text-center font-semibold">Wind</div>
            <div
              class="
                w-full
                h-full
                text-center
                lg:text-2xl
                md:text-xl
                font-light
              "
            >
              {{
                currentData.weather ? (currentData.wind * 3.6).toFixed(0) : "0"
              }}
              km/hr
            </div>
          </div>
          <div
            class="
              flex flex-col
              justify-center
              w-1/3
              h-full
              xl:py-0
              lg:py-2
              py-2
              border
            "
          >
            <div class="w-full h-full py-4 text-center font-semibold">
              Pressure
            </div>
            <div
              class="
                w-full
                h-full
                text-center
                lg:text-2xl
                md:text-xl
                font-light
              "
            >
              {{ currentData.weather ? currentData.pressure : "0" }}
              <span class="text-sm">hPa</span>
            </div>
          </div>
          <div
            class="
              flex flex-col
              justify-center
              w-1/3
              h-full
              xl:py-0
              lg:py-2
              py-2
              border
            "
          >
            <div class="w-full h-full py-4 text-center font-semibold">
              Humidity
            </div>
            <div
              class="
                w-full
                h-full
                text-center
                lg:text-2xl
                md:text-xl
                font-light
              "
            >
              {{ currentData.weather ? currentData.humidity : "0" }} %
            </div>
          </div>
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
      location: "",
    };
  },
  props: ["currentData", "days"],
  computed: {
    day() {
      return this.days[moment(this.currentData.dt * 1000).day()];
    },
  },
  watch: {
    currentData(newValue, oldValue) {
      const weatherIconImg = document.getElementById("weatherIconImg");
      weatherIconImg.setAttribute(
        "src",
        `http://openweathermap.org/img/wn/${newValue.weather[0].icon}@2x.png`
      );

      if (newValue.name) this.location = newValue.name;
    },
  },
};
</script>
