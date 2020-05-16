<template>
  <div class="trips">
    <!-- Masthead-->
    <header class="masthead-routes">
      <div class="container d-flex h-100 align-items-center">
        <div class="mx-auto text-center">
          <h2 class="text-white-50 mx-auto mt-2 mb-5">
            My Routes
          </h2>
        </div>
      </div>
    </header>
    <!-- <h1>My Routes</h1>
    <div v-for="trip in trips">
      <h3>Last Updated: {{ relativeDate(trip.created_at) }}</h3>
      <p>Duration: {{ trip.duration }}</p>
      <p>Distance: {{ trip.distance }}</p>
      <p>Mode: {{ trip.mode }}</p>
      <p>Type: {{ trip.trip_type }}</p>
      <div>
        <a v-bind:href="`/trips/${trip.id}`">More info</a>
      </div>
      <br />
    </div> -->
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
      message: "Routes",
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
