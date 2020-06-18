<template>
  <div id="app">
    <Navbar :project="project" />
    <Map />
    <router-view></router-view>
  </div>
</template>

<script>
import Navbar from './components/Navbar.vue'
import Map from './components/Map.vue'

export default {
  name: 'app',
  components: { Navbar, Map },
  data() {
    return {
      project: process.env.VUE_APP_TITLE
    }
  }
}
</script>