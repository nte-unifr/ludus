<template>
  <div>
    <div id="map"></div>
  </div>
</template>

<script>
  //import _ from 'lodash'
  import L from 'leaflet'
  import 'leaflet.markercluster'
  import directus from '../mixins/Directus.vue'

  delete L.Icon.Default.prototype._getIconUrl;
  L.Icon.Default.mergeOptions({
    iconRetinaUrl: require('leaflet/dist/images/marker-icon-2x.png'),
    iconUrl: require('leaflet/dist/images/marker-icon.png'),
    shadowUrl: require('leaflet/dist/images/marker-shadow.png'),
  });

  export default {
    mixins: [directus],
    data() {
      return {
        map: null,
        cluster: null,
        items: [],
        markers: []
      }
    },
    methods: {
      resizeMapContainer: function() {
        const navbarHeight = document.getElementsByClassName('navbar')[0].offsetHeight
        const footerHeight = 100
        const height = window.innerHeight || document.documentElement.clientHeight || document.body.clientHeight
        const mapContainer = document.getElementById('map')
        mapContainer.style.height = height - navbarHeight - footerHeight + 'px'
      },
      createMap: function(viewLng, viewLat, zoom) {
        this.map = L.map('map', {
          minZoom: 3
        }).setView([viewLng, viewLat], zoom)
      },
      setupLayers: function() {
        L.tileLayer('https://api.tiles.mapbox.com/v4/{id}/{z}/{x}/{y}.png?access_token={accessToken}', {
          attribution: 'Map data &copy; <a href="https://www.openstreetmap.org/">OpenStreetMap</a> contributors, <a href="https://creativecommons.org/licenses/by-sa/2.0/">CC-BY-SA</a>, Imagery © <a href="https://www.mapbox.com/">Mapbox</a>',
          maxZoom: 20,
          id: 'mapbox.streets',
          accessToken: process.env.VUE_APP_MAPBOX_TOKEN
        }).addTo(this.map)
        this.cluster = L.markerClusterGroup()
      },
      createMarker: function(lng, lat, content) {
        let marker = L.marker([lng, lat])
        let popup = L.popup().setContent(content)
        marker.bindPopup(popup)
        this.markers.push(marker)
      },
      buildTooltip: function(title, morphology, game) {
        let str = '<div class="title is-4">' + title + '</div>'
        if (game != null && game != '') {
          str += '<div class="subtitle is-5" style="margin-bottom: 1rem">' + game + '</div>'
        }
        if (morphology != null && morphology != '') {
          str += '<div class="subtitle is-6"><i class="fad fa-shapes"></i> ' + morphology + '</div>'
        }
        return str
      }
    },
    async created() {
      this.items = await this.fetchItems()
      for (const item of this.items) {
        if (item.lng != "NULL" && item.lat != "NULL" && item.lng != 0 && item.lat != 0) {
          this.createMarker(item.lng, item.lat, this.buildTooltip(item.title, item.morphology.name, item.game.name))
        }
      }
      this.cluster.addLayers(this.markers).addTo(this.map)
      this.map.fitBounds(this.cluster.getBounds())
    },
    mounted() {
      this.resizeMapContainer()
      this.createMap(51.505, -0.09, 4)
      this.setupLayers()
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
    color: #000;
  }
  .modal {
    z-index: 2;
  }
</style>