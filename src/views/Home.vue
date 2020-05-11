<template>
  <div class="home">
    <h1>{{ message }}</h1>
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
      message: "Welcome to Bikr!",
    };
  },
  created: function() {},
  methods: {},
};
</script>
