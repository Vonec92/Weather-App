<template>
  <div class="text-white mb-8">
    <div class="places-input text-gray-800">
      <input type="text" class="w-full">
    </div>
    <div class="weather-container font-sans w-128 max-w-lg rounded-lg overflow-hidden bg-gray-900 shadow-lg mt-4">
      <div class="current-weather flex items-center justify-between px-6 py-8">
        <div class="flex items-center">
          <div>
            <div class="text-6xl font-semibold">{{ currentTemperature.actual }}°C</div>
            <div class="mt-5">Voelt als {{ currentTemperature.feels }}°C</div>
          </div>
          <div class="mx-5">
            <div class="font-semibold">{{ currentTemperature.summary }}</div>
            <div>{{ location.city }}</div>
          </div>
        </div>
        <div><img class="w-100" alt="icon"
                  v-bind:src="'http://openweathermap.org/img/wn/'+ currentTemperature.icon + '@2x.png' "/></div>
      </div> <!-- end current-weather -->

      <div class="future-weather text-sm bg-gray-800 px-6 py-8 overflow-hidden">
        <div
          v-for="(day, index) in daily"
          :key="day.dt"
          class="flex items-center"
          :class="{ 'mt-8' : index > 0}"
        >
          <div class="w-1/6 text-lg text-gray-200">{{ day.dt }}</div>
          <div class="w-4/6 px-5 flex items-center">
            <div>icon</div>
            <div class="ml-3">Cloudy bruh</div>
          </div>
          <div class="w-1/6 text-right">
            <div>5°C</div>
            <div>-2°C</div>
          </div>
        </div>
      </div>
      <!-- end future-weather -->

    </div> <!-- end weather-container -->
  </div>
</template>

<script>
export default {
  mounted() {
    this.fetchData()
  },
  data() {
    return {
      currentTemperature: {
        actual: '',
        feels: '',
        summary: '',
        icon: '',
      },
      location: {
        'city': 'Wilrijk',
      },
      daily: [],
    }
  },
  methods: {
    fetchData() {
      fetch(`/api/weather?city=${this.location.city}`)
        .then(response => response.json())
        .then(data => {
          console.log(data);
          this.currentTemperature.actual = Math.round(data.list[0].main.temp);
          this.currentTemperature.feels = Math.round(data.list[0].main.feels_like);
          this.currentTemperature.summary = data.list[0].weather[0].description;
          this.currentTemperature.icon = data.list[0].weather[0].icon;

          this.daily = data.list[0];

        })
    },
    toDayOfWeek(timestamp) {
      const newDate = new Date(timestamp * 1000)
      const days = ['ZO', 'MA', 'DI', 'WO', 'DO', 'VR', 'ZA'];

      return days[newDate.getDay()];
    }
  }
}
</script>
