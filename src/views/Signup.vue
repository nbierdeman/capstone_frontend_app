<template>
  <div class="signup">
    <!-- Masthead-Signup-->
    <header class="masthead-signup">
      <div class="container d-flex h-100 align-items-center">
        <div class="col-lg-8 mx-auto">
          <h2 class="text-white mb-4">Signup</h2>
          <form v-on:submit.prevent="submit()">
            <p class="text-danger" v-for="error in errors">{{ error }}</p>
            <div class="form-group">
              <p class="text-white-50">First Name:</p>
              <input type="text" class="form-control" v-model="firstName">
            </div>
            <div class="form-group">
              <p class="text-white-50">Last Name:</p>
              <input type="text" class="form-control" v-model="lastName">
            </div>
            <div class="form-group">
              <p class="text-white-50">Email:</p>
              <input type="email" class="form-control" v-model="email">
            </div>
            <div class="form-group">
              <p class="text-white-50">Password:</p>
              <input type="password" class="form-control" v-model="password">
            </div>
            <div class="form-group">
              <p class="text-white-50">Password confirmation:</p>
              <input type="password" class="form-control" v-model="passwordConfirmation">
            </div>
            <br>
            <input type="submit" class="btn btn-primary" value="Submit">
          </form>
        </div>
      </div>
    </header>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data: function() {
    return {
      firstName: "",
      lastName: "",
      email: "",
      password: "",
      passwordConfirmation: "",
      errors: [],
    };
  },
  methods: {
    submit: function() {
      var params = {
        first_name: this.firstName,
        last_name: this.lastName,
        email: this.email,
        password: this.password,
        password_confirmation: this.passwordConfirmation,
      };
      axios
        .post("/api/users", params)
        .then(response => {
          this.$router.push("/login");
        })
        .catch(error => {
          this.errors = error.response.data.errors;
        });
    },
  },
};
</script>