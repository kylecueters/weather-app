<template>
  <div
    class="
      h-full
      w-full
      flex
      xl:flex-row
      lg:flex-col
      flex-col
      items-center
      justify-center
    "
  >
    <div
      class="w-full h-full"
      v-for="(data, index) in forecastedData"
      :key="data.dt"
    >
      <div
        :id="`day-picker-${index}`"
        class="
          day-pick-row
          flex flex-col
          h-full
          w-full
          border-solid border-4 border-light-blue-500
          cursor-pointer
        "
        @mouseover="hoverElement(true, `day-picker-${index}`)"
        @mouseleave="hoverElement(false, `day-picker-${index}`)"
        @click="loadDataToDashboard(index)"
      >
        <div class="flex item-center justify-center h-1/2">
          <img
            id="weatherIconImg"
            :src="
              `http://openweathermap.org/img/wn/` +
              data.weather[0].icon +
              `@2x.png`
            "
          />
        </div>

        <div class="flex flex-col items-center justify-center h-1/2">
          <div class="">
            {{ getDay(data.dt) }}
          </div>

          <div class="">
            {{ data.weather[0].main }}
          </div>

          <div class="">({{ data.weather[0].description }})</div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import moment from "moment";

export default {
  props: ["forecastedData", "days"],
  data() {
    return {};
  },
  methods: {
    hoverElement(hover, elementId) {
      const el = document.getElementById(elementId);
      if (hover) {
        el.classList.add("bg-blue-300");
      } else {
        el.classList.remove("bg-blue-300");
      }
    },
    loadDataToDashboard(index) {
      this.$emit("loadToDashboard", {
        dayIndex: index,
        dataToLoad: this.forecastedData[index],
      });
    },
    getDay(dt) {
      return this.days[moment(dt * 1000).day()];
    },
  },
};
</script>