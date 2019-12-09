<template>
  <v-container ma-0 pa-0 id="app">
    <PlacesSearch />
  </v-container>
</template>

<script>
import PlacesSearch from "./components/PlacesSearch.vue";
export default {
  components: {
    PlacesSearch
  }
};
</script>

<style>
body {
  font-family: "Oswald", Sans-serif;
  font-weight: 300;
}
</style>
