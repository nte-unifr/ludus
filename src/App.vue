<template>
  <div id="app">
    <Navbar :project="project" />
    <Map />
    <Footer />
    <router-view></router-view>
  </div>
</template>

<script>
import Navbar from './components/Navbar.vue'
import Map from './components/Map.vue'
import Footer from './components/Footer.vue'

export default {
  name: 'app',
  components: { Navbar, Map, Footer },
  data() {
    return {
      project: process.env.VUE_APP_TITLE
    }
  }
}
</script>