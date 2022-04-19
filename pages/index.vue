<template>
  <div class="bg-gray-100 text-center">
    <input id="city-search" class="mx-auto m-5"/>

    <div class="text-center m-5">
      {{staticMapUrl}}
      <cld-image class="mx-auto" public-id="nuxtjs-weather-cards/template">
        <cld-transformation
          :overlay="`fetch:${staticMapUrl}`"
          gravity="north"
        />
        <!-- <cld-transformation effect="colorize:90" :color="color" /> -->
      </cld-image>
    </div>
  </div>
</template>

<script>
export default {
  data(){
    return {
      city: {
        name: "Amsterdam",
        geometry: {
          location: {
            lat: () => 52.3675734,
            lng: () => 4.9041389
          }
        }
      }
    };
  },
  computed:{
    staticMapUrl(){
      // return "https://www.learningcontainer.com/wp-content/uploads/2020/07/sample-JPG-File-Download-for-Testing.png";
      let url = "https://maps.googleapis.com/maps/api/staticmap?";
      url += `center=${this.city.geometry.location.lat()},${this.city.geometry.location.lng()}`;
      url += "&zoom=13";
      url += "&size=640x640";
      url += "&maptype=roadmap";
      url += `&key=${process.env.NUXT_ENV_GOOGLE_MAPS_API_KEY}`;

      return url;
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

      google.maps.event.addListener(autocomplete, 'place_changed', function() {
        this.city = autocomplete.getPlace();
        console.log(this.city);
      });
  },
}
</script>
