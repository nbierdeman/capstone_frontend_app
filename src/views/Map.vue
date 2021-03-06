<template>
  <div>
    <div id="map"></div>
    <div class="about-section">
      <div class="mx-auto text-center">
        <button v-if="waypoints" v-on:click="getAirQuality()">Get Air Quality</button>
        <button v-if="jwt && observations" v-on:click="saveRoute()">Save Route</button>
        <br />
        <br />
        <div v-if="route">
          <h3 class="text-white-50 mx-auto mt-2 mb-5">
            <strong>Route</strong>
          </h3>
          <h6 class="text-white-50 mx-auto mt-2 mb-5">
            <strong>Origin:</strong>
            <br />
            <br />
            {{ origin }}
            <br />
            <br />
            <strong>Destination:</strong>
            <br />
            <br />
            {{ destination }}
            <br />
            <br />
            <strong>Waypoints:</strong>
            <br />
            <br />
            {{ waypoints }}
            <br />
            <br />
            <strong>Duration:</strong>
            <br />
            <br />
            {{ duration }}
            <br />
            <br />
            <strong>Distance:</strong>
            <br />
            <br />
            {{ distance }}
          </h6>
        </div>
        <div v-if="observations">
          <h3 class="text-white-50 mx-auto mt-2 mb-5">
            <strong>Air Quality</strong><br />
            <br />
            <img v-bind:src="selectedImage" />
          </h3>
          <!-- <img v-bind:src="selectedImage" /> -->
          <h6 class="text-white-50 mx-auto mt-2 mb-5">
            <strong>Node VSN's:</strong>
            <br />
            <br />
            {{ node_vsns }}
            <br />
            <br />
            <strong>Node Coordinates:</strong>
            <br />
            <br />
            {{ closest_node_coordinates }}
            <br />
            <br />
            <strong>Observations:</strong>
            <br />
            <br />
            <div v-for="observation in observations">
              Sensor: {{ observation["sensor_path"] }}<br />
              Value: {{ observation["value"] }} {{ observation["uom"] }}<br />
              <br />
            </div>
          </h6>
        </div>
        <br />
      </div>
    </div>
    <!-- <p>Raw: {{ observation }}</p> -->
    <!-- <p>Route: {{ route }}</p> -->
  </div>
</template>

<style>
body {
  margin: 0;
  padding: 0;
}
#map {
  width: 100%;
  height: 668px;
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
      jwt: null,
      userId: null,
      nodes_geojson: null,
      map: null,
      images: ["../assets/img/good.png", "../assets/img/bad.png"],
      selectedImage: "",
    };
  },
  created: function() {
    this.setJwt();
    this.setUserId();
  },
  methods: {
    setJwt: function() {
      this.jwt = localStorage.jwt;
    },
    setUserId: function() {
      this.userId = parseInt(localStorage.user_id);
    },
    getAirQuality() {
      axios.get("/api/maps/").then(response => {
        console.log("Get air quality", response);
        this.observations = response.data.observations;
        this.closest_node_coordinates = response.data.closest_node_coordinates;
        this.node_vsns = response.data.node_vsns;
        this.nodes_geojson = response.data.nodes_geojson;
        // add markers to map
        this.nodes_geojson.features.forEach(feature => {
          new mapboxgl.Marker()
            .setLngLat(feature.geometry.coordinates)
            .setPopup(new mapboxgl.Popup().setHTML(feature.properties.title)) // add popup
            .addTo(this.map);
        });
        const image_id = Math.floor(Math.random() * this.images.length);
        this.selectedImage = this.images[image_id];
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

    this.map = map;

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
