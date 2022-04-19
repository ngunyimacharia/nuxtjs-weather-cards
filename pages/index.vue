<template>
  <input id="city-search" />
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
