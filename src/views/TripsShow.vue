<template>
  <div>
    <h1>Trip Info</h1>
    <h3>{{ relativeDate(trip.created_at) }}</h3>
    <p>Duration: {{ trip.duration }}</p>
    <p>Distance: {{ trip.distance }}</p>
    <p>Mode: {{ trip.mode }}</p>
    <p>Type: {{ trip.trip_type }}</p>
    <br />
    <h3>Air Quality Info</h3>
    <div v-for="observation in observations">
      <p>{{ formatDate(observation.timestamp) }}</p>
      <p>Value: {{ observation.value }} {{ observation.uom }}</p>
      <p>Type: {{ observation.sensor_path }}</p>
      <p>Node: {{ observation.node_vsn }}</p>
      <p>Location: {{ observation.longitude }}, {{ observation.latitude }}</p>
      <br />
    </div>
    <!-- <a v-bind:href="`/trips/${trip.id}/edit`">Edit trip</a> -->
    <!-- <br /> -->
    <button v-on:click="destroyTrip(trip)">Delete Trip</button>
    <a href="/trips">Back to All Trips</a>
  </div>
</template>

<style></style>

<script>
import axios from "axios";
import moment from "moment";

export default {
  data: function() {
    return {
      trip: {},
      observations: [],
    };
  },

  created: function() {
    axios.get("/api/trips/" + this.$route.params.id).then(response => {
      console.log("Get one trip", response);
      this.trip = response.data;
      this.observations = response.data.observations;
    });
  },
  methods: {
    destroyTrip(trip) {
      axios.delete("/api/trips/" + this.$route.params.id).then(response => {
        this.$router.push("/trips");
      });
    },
    relativeDate: function(date) {
      return moment(date).fromNow();
    },
    formatDate: function(date) {
      return moment(date).format("MMMM Do YYYY, h:mm:ss a");
    },
  },
};
</script>
