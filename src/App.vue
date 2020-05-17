<template>
  <div id="app">
    <!-- Navigation-->
    <nav class="navbar navbar-expand-lg navbar-light fixed-top" id="mainNav">
      <div class="container">
        <a class="navbar-brand js-scroll-trigger" href="/">Bikr</a>
        <button
          class="navbar-toggler navbar-toggler-right"
          type="button"
          data-toggle="collapse"
          data-target="#navbarResponsive"
          aria-controls="navbarResponsive"
          aria-expanded="false"
          aria-label="Toggle navigation"
        >
          Menu
          <i class="fas fa-bars"></i>
        </button>
        <div class="collapse navbar-collapse" id="navbarResponsive">
          <ul class="navbar-nav ml-auto">
            <li class="nav-item"><a class="nav-link js-scroll-trigger" href="/map">Map</a></li>
            <li v-if="!jwt" class="nav-item"><a class="nav-link js-scroll-trigger" href="/login">Login</a></li>
            <li v-if="!jwt" class="nav-item"><a class="nav-link js-scroll-trigger" href="/signup">Signup</a></li>
            <li v-if="jwt" class="nav-item"><a class="nav-link js-scroll-trigger" href="/trips">My Routes</a></li>
            <li class="nav-item"><a class="nav-link js-scroll-trigger" href="/about">About</a></li>
            <li v-if="jwt" class="nav-item"><a class="nav-link js-scroll-trigger" href="/logout">Logout</a></li>
          </ul>
        </div>
      </div>
    </nav>

    <router-view />

    <!-- Footer-->
    <footer class="footer bg-black small text-center text-white-50">
      <div class="container">Copyright © Bikr 2020</div>
    </footer>

  </div>
</template>

<style>
</style>

<script>
export default {
  data: function() {
    return {
      jwt: null,
      userId: null,
    };
  },
  created: function() {
    this.setJwt();
    this.setUserId();
  },
  methods: {
    setJwt: function() {
      this.jwt = localStorage.jwt;
    },
    setUserId: function() {
      this.userId = parseInt(localStorage.user_id);
    },
  },
  mounted: function() {
    setupTheme();
  },
};
</script>