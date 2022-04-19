<template>
  <div class="bg-gray-100 text-center">
    <input id="city-search" class="mx-auto m-5"/>

    <div class="text-center m-5">
    <cld-image class="mx-auto" public-id="nuxtjs-weather-cards/template" />
    </div>
  </div>
</template>

<script>
export default {
  data(){
    return {
      city: null
    };
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
