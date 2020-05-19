<template>
  <div>
    <!-- Masthead-->
    <div class="masthead-route-show">
      <div class="container d-flex h-100 align-items-center">
        <div class="mx-auto text-center">
          <h3 class="text-white-50 mx-auto mt-2 mb-5">
            Route
          </h3>
          <h6 class="text-white-50 mx-auto mt-2 mb-5">
            Updated: {{ relativeDate(trip.created_at) }}<br />
            Distance: {{ trip.distance }}<br />
            Duration: {{ trip.duration }}<br />
            Type: {{ trip.trip_type }}<br />
            Mode: {{ trip.mode }}<br />
          </h6>
          <!-- <h3 class="text-white-50 mx-auto mt-2 mb-5">
            Air Quality
          </h3> -->
          <div v-for="observation in observations">
            <h6 class="text-white-50 mx-auto mt-2 mb-5">
              {{ formatDate(observation.timestamp) }}<br />
              Sensor: {{ observation.sensor_path }}<br />
              Location: {{ observation.longitude }}, {{ observation.latitude }}<br />
              Value: {{ observation.value }} {{ observation.uom }}<br />
              Node: "{{ observation.node_vsn }}"<br />
            </h6>
          </div>
          <!-- <br />
          <a v-bind:href="`/trips/${trip.id}/edit`">Edit trip</a>
          <br />
          <br /> -->
          <button v-on:click="destroyTrip(trip)">Delete Route</button>
          <br />
          <br />
          <a href="/trips">Back to My Routes</a>
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
