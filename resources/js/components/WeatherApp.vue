<template>
  <div class="text-white mb-8">
    <div class=" w-full places-input text-gray-800">
      <input type="search" id="address" class=" w-full form-control" placeholder="Choose a city..."/>
      <p>Selected: <strong id="address-value">none</strong></p>
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
          :key="index"
          class="flex items-center"
          :class="{ 'mt-8' : index > 0}"
          v-if="index % 8 === 0"
        >
          <div class="w-1/6 text-2xl text-gray-200">{{ toDayOfWeek(day.dt) }}</div>
          <div class="w-4/6 px-5 flex items-center">
            <div><img class="w-100" alt="icon"
                      v-bind:src="'http://openweathermap.org/img/wn/'+ day.weather[0].icon + '@2x.png' "
                      style="width:80px;height:80px;"/></div>
            <div class="text-2xl ml-3">{{ day.weather[0].description }}</div>
          </div>
          <div class="w-1/6 text-right">
            <div class="text-2xl">{{ Math.ceil(day.main.temp_max) }}°C</div>
            <div class="text-2xl">{{ Math.floor(day.main.temp_min) }}°C</div>
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

    var placesAutocomplete = places({
      appId: 'plE8QTRLUV78',
      apiKey: 'd95f632a3775b7f61a1d9617a1525352',
      container: document.querySelector('#address')
    }).configure({
      type: 'city',
      aroundLatLngViaIP: false,
    });

    var $address = document.querySelector('#address-value')

    placesAutocomplete.on('change', (e) => {
      $address.textContent = e.suggestion.value

      this.location.city = `${e.suggestion.name}, ${e.suggestion.country}`
    });

    placesAutocomplete.on('clear', function () {
      $address.textContent = 'none';
    });
  },
  watch: {
    location: {
      handler(newValue, oldValue) {
        this.fetchData()
      },
      deep: true
    }
  },
  data() {
    return {
      currentTemperature: {
        actual: '',
        feels: '',
        summary: '',
        icon: '',
      },
      daily: [],
      location: {
        city: 'Wilrijk'
      }
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

          this.daily = data.list;

        })
    },
    toDayOfWeek(timestamp) {
      const newDate = new Date(timestamp * 1000)
      const days = ['ZO', 'MA', 'DI', 'WO', 'DO', 'VR', 'ZA'];

      return days[newDate.getDay()];
    }
  },
}
</script>
