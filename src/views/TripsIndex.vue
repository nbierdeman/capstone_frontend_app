<template>
  <div class="trips">
    <h1>All Trips</h1>
    <div v-for="trip in trips">
      <h3>{{ relativeDate(trip.created_at) }}</h3>
      <p>Duration: {{ trip.duration }}</p>
      <p>Distance: {{ trip.distance }}</p>
      <p>Mode: {{ trip.mode }}</p>
      <p>Type: {{ trip.trip_type }}</p>
      <div>
        <a v-bind:href="`/trips/${trip.id}`">More info</a>
      </div>
      <br />
    </div>
  </div>
</template>

<style></style>

<script>
import axios from "axios";
import moment from "moment";

export default {
  data: function() {
    return {
      trips: [],
    };
  },

  created: function() {
    axios.get("/api/trips").then(response => {
      console.log("All trips data", response);
      this.trips = response.data;
    });
  },
  methods: {
    relativeDate: function(date) {
      return moment(date).fromNow();
    },
  },
};
</script>
