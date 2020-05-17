<template>
  <div class="login">
    <!-- Masthead-Login-->
    <div class="masthead-login">
      <div class="container d-flex h-100 align-items-center">
        <div class="col-lg-8 mx-auto">
          <h2 class="text-white mb-4">Login</h2>
          <form v-on:submit.prevent="submit()">
            <p class="text-danger" v-for="error in errors">{{ error }}</p>
            <div class="form-group">
              <p class="text-white-50">Email:</p>
              <input type="email" class="form-control" v-model="email" />
            </div>
            <div class="form-group">
              <p class="text-white-50">Password:</p>
              <input type="password" class="form-control" v-model="password" />
            </div>
            <br>
            <input type="submit" class="btn btn-primary" value="Submit" />
          </form>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data: function() {
    return {
      email: "",
      password: "",
      errors: [],
    };
  },
  methods: {
    submit: function() {
      var params = {
        email: this.email,
        password: this.password,
      };
      axios
        .post("/api/sessions", params)
        .then(response => {
          axios.defaults.headers.common["Authorization"] = "Bearer " + response.data.jwt;
          localStorage.setItem("jwt", response.data.jwt);
          localStorage.setItem("user_id", response.data.user_id);
          this.$parent.setJwt();
          this.$router.push("/map");
        })
        .catch(error => {
          this.errors = ["Invalid email or password."];
          this.email = "";
          this.password = "";
        });
    },
  },
};
</script>
