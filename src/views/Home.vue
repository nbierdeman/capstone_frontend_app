<template>
  <div class="home">
    <h1>{{ message }}</h1>
    <div id="map"></div>
  </div>
</template>

<style>
body {
  margin: 0;
  padding: 0;
}
#map {
  width: 100%;
  height: 500px;
}
</style>

<script>
export default {
  data: function() {
    return {
      message: "Welcome to Bikr!",
    };
  },
  created: function() {},
  methods: {},
  mounted: function() {
    mapboxgl.accessToken = process.env.VUE_APP_MAPBOX_API_KEY;
    var map = new mapboxgl.Map({
      container: "map", // container id
      style: "mapbox://styles/mapbox/dark-v10", // stylesheet location
      center: [-87.6298, 41.8781], // starting position [lng, lat]
      zoom: 12, // starting zoom
    });
    map.addControl(
      new MapboxDirections({
        accessToken: mapboxgl.accessToken,
        profile: "mapbox/cycling",
      }),
      "top-left"
    );
  },
};
</script>