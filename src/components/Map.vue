<template>
  <div>
    <div id="map"></div>
  </div>
</template>

<script>
  import L from 'leaflet'
  import 'leaflet.markercluster'

  export default {
    data() {
      return {
        map: null
      }
    },
    methods: {
      resizeMap: function() {
        const navbarHeight = document.getElementsByClassName('navbar')[0].offsetHeight
        const footerHeight = 100
        const height = window.innerHeight || document.documentElement.clientHeight || document.body.clientHeight
        const mapContainer = document.getElementById('map')
        mapContainer.style.height = height - navbarHeight - footerHeight + 'px'
      }
    },
    mounted() {
      this.resizeMap()
      this.map = L.map('map').setView([51.505, -0.09], 4)

      L.tileLayer('https://api.tiles.mapbox.com/v4/{id}/{z}/{x}/{y}.png?access_token={accessToken}', {
        attribution: 'Map data &copy; <a href="https://www.openstreetmap.org/">OpenStreetMap</a> contributors, <a href="https://creativecommons.org/licenses/by-sa/2.0/">CC-BY-SA</a>, Imagery © <a href="https://www.mapbox.com/">Mapbox</a>',
        maxZoom: 20,
        id: 'mapbox.streets',
        accessToken: process.env.VUE_APP_MAPBOX_TOKEN
      }).addTo(this.map)
    }
  }
</script>

<style scoped>
  @import '~leaflet/dist/leaflet.css';
  @import '~leaflet.markercluster/dist/MarkerCluster.css';
  @import '~leaflet.markercluster/dist/MarkerCluster.Default.css';
  #map {
    height: 800px;
    z-index: 1;
  }
  .modal {
    z-index: 2;
  }
</style>