<template>
  <div>
    <div id="map"></div>
    <button v-on:click="getAirQuality()">Air Quality</button>
    <button v-on:click="saveRoute()">Save Route</button>
    <!-- <p>Origin: {{ origin }}</p> -->
    <!-- <p>Destination: {{ destination }}</p> -->
    <!-- <p>Waypoints: {{ waypoints }}</p> -->
    <!-- <p>Node VSN: {{ node_vsns }}</p> -->
    <!-- <p>Closest Nodes: {{ closest_node_coordinates }}</p> -->
    <!-- <p>Route: {{ route }}</p> -->
    <div v-for="observation in observations">
      <h3>Sensor: {{ observation["sensor_path"] }}</h3>
      <p>Value: {{ observation["value"] }} {{ observation["uom"] }}</p>
      <!-- <p>Raw: {{ observation }}</p> -->
    </div>
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
import axios from "axios";
import moment from "moment";

export default {
  data: function() {
    return {
      origin: null,
      destination: null,
      route: null,
      observations: null,
      closest_node_coordinates: null,
      node_vsns: null,
      waypoints: null,
      duration: null,
      distance: null,
      mode: null,
      trip_type: null,
    };
  },
  created: function() {},
  methods: {
    getAirQuality() {
      axios.get("/api/maps/").then(response => {
        console.log("Get air quality", response);
        this.observations = response.data.observations;
        this.closest_node_coordinates = response.data.closest_node_coordinates;
        this.node_vsns = response.data.node_vsns;
      });
    },
    saveRoute() {
      var params1 = {
        duration: this.duration,
        distance: this.distance,
        mode: this.mode,
        trip_type: this.trip_type,
        closest_node_coordinates: this.closest_node_coordinates,
        observations: this.observations,
      };
      axios
        .post("/api/trips", params1)
        .then(response => {
          console.log("Trip saved!", response);
        })
        .catch(error => {
          this.errors = error.response.data.errors;
        });
    },
  },
  mounted: function() {
    mapboxgl.accessToken = process.env.VUE_APP_MAPBOX_API_KEY;
    var map = new mapboxgl.Map({
      hash: true,
      container: "map", // container id
      style: "mapbox://styles/mapbox/dark-v10", // stylesheet location
      center: [-87.6298, 41.8781], // starting position [lng, lat]
      zoom: 12, // starting zoom
    });

    // remove all waypoints
    // var removeWaypointsButton = document.body.appendChild(document.createElement("button"));
    // removeWaypointsButton.style = "z-index:10;position:absolute;top:30px;right:10px;";
    // removeWaypointsButton.textContent = "Remove all directions";

    var directions = new MapboxDirections({
      accessToken: mapboxgl.accessToken,
      profile: "mapbox/cycling",
      controls: {
        instructions: false,
        profileSwitcher: false,
      },
    });
    window.directions = directions;

    map.addControl(directions, "bottom-left");

    // map.on("load", () => {
    //   removeWaypointsButton.addEventListener("click", function() {
    //     directions.removeRoutes();
    //   });
    // });

    directions.on("origin", feature => {
      console.log(feature);
      this.origin = feature["feature"]["geometry"]["coordinates"];
    });

    directions.on("destination", feature => {
      console.log(feature);
      this.destination = feature["feature"]["geometry"]["coordinates"];
    });

    directions.on("route", feature => {
      console.log(feature);
      this.route = feature;
      var params = {
        route: this.route,
      };
      axios.post("/api/maps", params).then(response => {
        console.log(response.data);
        this.waypoints = response.data.waypoints;
        this.duration = response.data.duration;
        this.distance = response.data.distance;
        this.mode = response.data.mode;
        this.trip_type = response.data.trip_type;
      });
    });
    // map.on("load", function() {
    //   map.addSource("route", {
    //     type: "geojson",
    //     data: {
    //       type: "Feature",
    //       properties: {},
    //       geometry: {
    //         type: "LineString",
    //         coordinates: [
    //           // [-87.620893, 41.883379],
    //           // [-87.620849, 41.884202],
    //           // [-87.620638, 41.884203],
    //           // [-87.620674, 41.887885],
    //           // [-87.617432, 41.887675],
    //           // [-87.617598, 41.887925],
    //           // [-87.617364, 41.887972],
    //           // [-87.615837, 41.887859],
    //           // [-87.615713, 41.887922],
    //           // [-87.615451, 41.887907],
    //           // [-87.615355, 41.887816],
    //           // [-87.614895, 41.887793],
    //           // [-87.613499, 41.887781],
    //           // [-87.613053, 41.887441],
    //           // [-87.612962, 41.886645],
    //           // [-87.613355, 41.886576],
    //           // [-87.613489, 41.886834],
    //           // [-87.613567, 41.887276],
    //           // [-87.613805, 41.887569],
    //           // [-87.613896, 41.887992],
    //           // [-87.613921, 41.888895],
    //           // [-87.613617, 41.890315],
    //           // [-87.613443, 41.890608],
    //           // [-87.612903, 41.890803],
    //           // [-87.611543, 41.890826],
    //           // [-87.61136, 41.890801],
    //           // [-87.611165, 41.890508],
    //           // [-87.61102, 41.890646],
    //           // [-87.61073, 41.890488],
    //           // [-87.610327, 41.89052],
    //           // [-87.610095, 41.890668],
    //           // [-87.610021, 41.890803],
    //           // [-87.610055, 41.892128],
    //           // [-87.60656, 41.89218],
    //           // [-87.60643, 41.892089],
    //           // [-87.604734, 41.892114],
    //           // [-87.604733, 41.892063],
    //         ],
    //       },
    //     },
    //   });
    //   map.addLayer({
    //     id: "route",
    //     type: "line",
    //     source: "route",
    //     layout: {
    //       "line-join": "round",
    //       "line-cap": "round",
    //     },
    //     paint: {
    //       "line-color": "#888",
    //       "line-width": 8,
    //     },
    //   });
    // });
  },
};
</script>
