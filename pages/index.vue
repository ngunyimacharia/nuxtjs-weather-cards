<template>
  <div class="bg-gray-100 min-h-screen text-center">

    <div class=" w-1/3 py-10 mx-auto">
        <input id="city-search" class="w-full mx-auto m-5 p-5 shadow-sm focus:ring-indigo-500 focus:border-indigo-500 block sm:text-sm border-gray-300 rounded-md" />

        <p v-if="status" class="text-gray-600 text-sm">
          {{status}}
        </p>

        <cld-image width="400" v-if="weather" class="mt-10 mx-auto w-full" public-id="nuxtjs-weather-cards/template">
          <cld-transformation
            :overlay="`fetch:${staticMapUrl}`"
            gravity="north"
          />
          <!-- Weather description  -->
          <cld-transformation
            :overlay="`text:Sacramento_100_normal:${weather.weather[0].description},co_rgb:000000`"
            gravity="center"
            y="130"
          />
          <!-- Feels like -->
          <cld-transformation
            :overlay="`text:Roboto_18_normal:Feels like ${weather.main.feels_like} °C,co_rgb:32465c`"
            gravity="center"
            y="220"
          />
          <!-- Main temp -->
          <cld-transformation
            :overlay="`text:Roboto_50_normal:${weather.main.temp} °C,co_rgb:000000`"
            gravity="center"
            y="300"
          />
          <!-- Min temp -->
          <cld-transformation
            :overlay="`text:Roboto_18_normal:MIN,co_rgb:31465d`"
            gravity="center"
            y="400"
            x="-170"
          />
          <cld-transformation
            :overlay="`text:Roboto_18_normal:${weather.main.temp_min} °C,co_rgb:31465d`"
            gravity="center"
            y="430"
            x="-170"
          />
          <!-- Max temp -->
          <cld-transformation
            :overlay="`text:Roboto_18_normal:MAX,co_rgb:31465d`"
            gravity="center"
            y="400"
            x="170"
          />
          <cld-transformation
            :overlay="`text:Roboto_18_normal:${weather.main.temp_max} °C,co_rgb:31465d`"
            gravity="center"
            y="430"
            x="170"
          />
          <!-- Pressure -->
          <cld-transformation
            :overlay="`text:Roboto_18_normal:PRESSURE,co_rgb:31465d`"
            gravity="center"
            y="510"
            x="-170"
          />
          <cld-transformation
            :overlay="`text:Roboto_18_normal:${weather.main.pressure} Pa,co_rgb:31465d`"
            gravity="center"
            y="540"
            x="-170"
          />
          <!-- Humidity -->
          <cld-transformation
            :overlay="`text:Roboto_18_normal:HUMIDITY,co_rgb:31465d`"
            gravity="center"
            y="510"
            x="170"
          />
          <cld-transformation
            :overlay="`text:Roboto_18_normal:${weather.main.humidity} g.kg-1,co_rgb:31465d`"
            gravity="center"
            y="540"
            x="170"
          />
        </cld-image>
    </div>
  </div>
</template>

<script>
export default {
  data(){
    return {
      city: null,
      weather: null,
      status: "Waiting for country selection.."
    };
  },
  computed:{
    staticMapUrl(){
      let url = "https://maps.googleapis.com/maps/api/staticmap?";
      url += `center=${this.city.geometry.location.lat()},${this.city.geometry.location.lng()}`;
      url += "&zoom=13";
      url += "&size=640x640";
      url += "&maptype=roadmap";
      url += `&key=${process.env.NUXT_ENV_GOOGLE_MAPS_API_KEY}`;

      return url;
    }
  },
  watch:{
    city(){
      this.weather = null;
      if(this.city){
        this.getWeatherData();
      }
    },
    weather(){
      this.$forceUpdate();
    }
  },
  mounted(){
      const input = document.getElementById("city-search");

      const options = {
        fields: ["geometry","name"],
        strictBounds: false,
        types: ["(cities)"],
      };

      const autocomplete = new google.maps.places.Autocomplete(input, options);

      google.maps.event.addListener(
        autocomplete,
        'place_changed',
        () => this.city = autocomplete.getPlace()
      );
  },
  methods:{
    getWeatherData(){
      this.status = "Loading weather data..."
      const requestOptions ={
        method: 'GET',
        redirect: 'follow'
      };

      const requestURL = "https://api.openweathermap.org/data/2.5/weather?";

      const queryParams = new URLSearchParams({
        lat: this.city.geometry.location.lat(),
        lon: this.city.geometry.location.lng(),
        appid: process.env.NUXT_ENV_OPEN_WEATHER_API_KEY,
        units: "metric"
      });

      fetch(requestURL + queryParams, requestOptions)
        .then(response => response.json())
        .then( response => this.weather = response)
        .then(() => this.status = null)
        .catch(error => alert('Error:' + error));
    }
  }
}
</script>
