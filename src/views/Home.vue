<template>
  <div class="home">
    <!-- Masthead-->
    <div class="masthead-home">
      <div class="container d-flex h-100 align-items-center">
        <div class="mx-auto text-center">
          <h1 class="mx-auto my-0">{{ message }}</h1>
          <h2 class="text-white-50 mx-auto mt-2 mb-5">
            For Every Ride
          </h2>
          <a class="btn btn-primary js-scroll-trigger" href="/map">Get Started</a>
        </div>
      </div>
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
export default {
  data: function() {
    return {
      message: "Clean Air",
    };
  },
  created: function() {},
  methods: {},
};
</script>
