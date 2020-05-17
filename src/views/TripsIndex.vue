<template>
  <div class="trips">
    <!-- Masthead-->
    <div class="masthead-routes">
      <div class="container d-flex h-100 align-items-center">
        <div class="mx-auto text-center">
          <h3 class="text-white-50 mx-auto mt-2 mb-5">
            My Routes
          </h3>
          <div v-for="trip in trips">
            <h6 class="text-white-50 mx-auto mt-2 mb-5">
              Updated: {{ relativeDate(trip.created_at) }}<br />
              Distance: {{ trip.distance }}<br />
              Duration: {{ trip.duration }}<br />
              Type: {{ trip.trip_type }}<br />
              Mode: {{ trip.mode }}<br />
              <br />
              <a v-bind:href="`/trips/${trip.id}`">More info</a>
            </h6>
          </div>
        </div>
      </div>
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
