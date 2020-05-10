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
    // map.addControl(
    //   new MapboxDirections({
    //     accessToken: mapboxgl.accessToken,
    //     profile: "mapbox/cycling",
    //   }),
    //   "top-left"
    // );
    map.on("load", function() {
      map.addSource("route", {
        type: "geojson",
        data: {
          type: "Feature",
          properties: {},
          geometry: {
            type: "LineString",
            coordinates: [
              [-87.65532, 41.988179],
              [-87.655131, 41.98188],
              [-87.66843, 41.981675],
              [-87.668416, 41.976191],
              [-87.668085, 41.97339],
              [-87.671147, 41.97341],
              [-87.670887, 41.963399],
              [-87.674571, 41.963343],
              [-87.67426, 41.956051],
              [-87.674643, 41.955907],
            ],
          },
        },
      });
      map.addLayer({
        id: "route",
        type: "line",
        source: "route",
        layout: {
          "line-join": "round",
          "line-cap": "round",
        },
        paint: {
          "line-color": "#888",
          "line-width": 8,
        },
      });
    });
  },
};
</script>