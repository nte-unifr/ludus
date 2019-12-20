<template>
  <div>
    <nav class="navbar is-primary is-fixed-top" role="navigation" aria-label="main navigation">
      <div class="navbar-brand">
        <a class="navbar-item" href="https://www.unifr.ch">
          <img src="../assets/logo-unifr.png" width="28" height="28">
        </a>
        <a class="navbar-item" href="#">
          <h1><strong>{{ project }}</strong></h1>
        </a>

        <router-link to="/about" class="navbar-burger burger">
          <span aria-hidden="true"></span>
          <span aria-hidden="true"></span>
          <span aria-hidden="true"></span>
        </router-link>
      </div>

      <div id="main-menu" class="navbar-menu">
        <div class="navbar-start is-hidden-touch">
          <p class="navbar-item is-italic">A database of Ancient Boardgames</p>
        </div>

        <div class="navbar-end">
          <router-link to="/about" class="navbar-item">About</router-link>
        </div>
      </div>
    </nav>
  </div>
</template>

<script>
  export default {
    props: ['project'],
  }
</script>