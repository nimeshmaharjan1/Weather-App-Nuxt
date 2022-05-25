<script setup lang="ts">
interface IWeather {
  base: string;
  clouds: object;
  cod: number;
  coord: object;
  dt: number;
  id: number;
  main: object;
  name: string;
  sys: object;
  timezone: number;
  visibility: number;
  weather: Array<object>;
  wind: object;
}
let data = ref<IWeather>({
  base: "",
  clouds: {},
  cod: null,
  coord: {},
  dt: null,
  id: null,
  main: {},
  name: "",
  sys: {},
  timezone: null,
  visibility: null,
  weather: [{}],
  wind: {},
});
const cookie = useCookie("city");
const config = useRuntimeConfig();
if (!cookie.value) cookie.value = "Kathmandu";
const search = ref(cookie.value);
const input = ref("");
const background = ref("");
const { data: city, error } = useAsyncData(
  "city",
  async () => {
    let response: any;
    try {
      response = await $fetch(
        `https://api.openweathermap.org/data/2.5/weather?q=${search.value}`,
        {
          params: {
            units: "metric",
            appid: config.WEATHER_APP_SECRET,
          },
        },
      );
      cookie.value = search.value;
      const temp = response.main.temp;
      if (temp <= -10) {
        background.value =
          "https://images.unsplash.com/photo-1483664852095-d6cc6870702d?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=3540&q=80";
      } else if (temp > -10 && temp <= 0) {
        background.value =
          "https://images.unsplash.com/photo-1476820865390-c52aeebb9891?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=3540&q=80";
      } else if (temp > 0 && temp <= 10) {
        background.value =
          "https://images.unsplash.com/photo-1560258018-c7db7645254e?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=4032&q=80";
      } else {
        background.value =
          "https://images.unsplash.com/photo-1507525428034-b723cf961d3e?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=3546&q=80";
      }
    } catch (e) {}
    return response;
  },
  {
    watch: [search],
  },
);
let todayDate: any = new Date();
todayDate = todayDate.toLocaleDateString("en-US", {
  weekday: "long",
  year: "numeric",
  month: "long",
  day: "numeric",
});
const handleSearch = () => {
  const formatedSearch = input.value.trim().split(" ").join("+");
  search.value = formatedSearch;
  input.value = "";
};
const goBack = () => {
  search.value = cookie.value;
};
</script>
<template>
  <div v-if="city" class="h-screen relative overflow-hidden">
    <img :src="background" />
    <div class="absolute w-full h-full top-0 overlay"></div>
    <div class="absolute w-full h-full top-0 p-48">
      <section class="flex justify-between">
        <section class="location-section">
          <h1 class="text-7xl text-white">{{ city?.name }}</h1>
          <p class="font-extralight text-2xl text-white mt-2 ml-1">
            {{ todayDate }}
          </p>
          <img
            :src="`https://openweathermap.org/img/wn/${city?.weather[0]?.icon}@4x.png`"
            class="w-56 icon"
          />
        </section>
        <section class="degree-section">
          <p class="font-extralight text-9xl text-white">
            {{ Math.floor(city?.main?.temp) }}°
          </p>
        </section>
      </section>
      <section class="search-section mt-20">
        <input
          type="text"
          class="w-1/2 h-10"
          placeholder="search a city..."
          v-model="input"
        />
        <button class="bg-sky-400 w-20 text-white h-10" @click="handleSearch">
          Search
        </button>
      </section>
    </div>
  </div>
  <div v-else class="p-10">
    <h1 class="text-7xl">Oops, we cannot find that city!</h1>
    <button class="mt-5 bg-sky-400 px-10 w-50 text-white h-10" @click="goBack">
      Go Back
    </button>
  </div>
</template>

<style scoped>
.overlay {
  background-color: rgba(0, 0, 0, 0.5);
}
.icon {
  margin-left: -2.75rem;
  margin-top: -2.5rem;
}
</style>

export { };
