<template>
  <div class="edit">
    <div class="container">
      <form v-on:submit.prevent="submit()">
        <h1>Edit Trip Mode</h1>
        <ul>
          <li class="text-danger" v-for="error in errors">{{ error }}</li>
        </ul>
        <div class="form-group">
          <label>Mode:</label>
          <input type="text" class="form-control" v-model="trip.mode" />
        </div>
        <input type="submit" class="btn btn-primary" value="Submit" />
      </form>
    </div>
  </div>
</template>

<style></style>

<script>
import axios from "axios";

export default {
  data: function() {
    return {
      trip: {},
      errors: [],
    };
  },
  created: function() {
    axios.get("/api/trips/" + this.$route.params.id).then(response => {
      this.trip = response.data;
    });
  },
  methods: {
    submit: function() {
      var params = {
        mode: this.trip.mode,
      };
      axios
        .patch("/api/trips/" + this.$route.params.id, params)
        .then(response => {
          this.$router.push("/trips");
        })
        .catch(error => {
          this.errors = error.response.data.errors;
        });
    },
  },
};
</script>
