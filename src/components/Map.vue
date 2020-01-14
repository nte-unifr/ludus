<template>
  <section class="section">
    <div class="level">
      <div class="level-left">
        <div class="level-item">
          <p class="subtitle is-5">
            <strong>{{ konmariMarkers.length }} / {{ markers.length }}</strong> elements
          </p>
        </div>
        <div class="level-item">
          <Picker name="Games" @pick="setFilter" />
          <Picker name="Morphologies" @pick="setFilter" />
        </div>
      </div>
    </div>
    <div id="map"></div>
  </section>
</template>

<script>
  //import _ from 'lodash'
  import L from 'leaflet'
  import 'leaflet.markercluster'
  import _ from 'lodash'
  import directus from '../mixins/Directus.vue'
  import Picker from './Picker.vue'

  delete L.Icon.Default.prototype._getIconUrl;
  L.Icon.Default.mergeOptions({
    iconRetinaUrl: require('leaflet/dist/images/marker-icon-2x.png'),
    iconUrl: require('leaflet/dist/images/marker-icon.png'),
    shadowUrl: require('leaflet/dist/images/marker-shadow.png'),
  });

  export default {
    components: { Picker },
    mixins: [directus],
    data() {
      return {
        map: null,
        cluster: null,
        items: [],
        markers: [],
        game: '',
        morphology: ''
      }
    },
    computed: {
      konmariMarkers() {
        let konmari = this.markers

        if (this.game != '') {
          let game = this.game.id
          konmari = _.filter(konmari, function(i) {
            return i.game != null && i.game.id === game
          })
        }

        if (this.morphology != '') {
          let morphology = this.morphology.id
          konmari = _.filter(konmari, function(i) {
            return i.morphology != null && i.morphology.id === morphology
          })
        }

        return konmari
      }
    },
    watch: {
      konmariMarkers() {
        this.updateMarkers()
      }
    },
    methods: {
      resizeMapContainer: function() {
        const navbarHeight = document.getElementsByClassName('navbar')[0].offsetHeight
        const footerHeight = document.getElementsByClassName('footer')[0].offsetHeight
        const windowHeight = window.innerHeight || document.documentElement.clientHeight || document.body.clientHeight
        const mapContainer = document.getElementById('map')
        mapContainer.style.height = windowHeight - navbarHeight - footerHeight + 'px'
      },
      createMap: function(viewLat, viewLng, zoom) {
        this.map = L.map('map', {
          minZoom: 3
        }).setView([viewLat, viewLng], zoom)
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
      createMarker: function(lat, lng, content, game, morphology) {
        let lMarker = L.marker([lat, lng])
        let popup = L.popup().setContent(content)
        lMarker.bindPopup(popup)
        let m = {
          id: 1,
          game: game,
          morphology: morphology,
          lMarker: lMarker
        }
        this.markers.push(m)
      },
      buildTooltip: function(id, title, game, morphology) {
        let str = '<div class="title is-4" style="margin-bottom:0.6rem">' + title + '</div>'
        str += '<ul style="font-size:16px">'
        if (game != null && game != '') {
          str += '<li><i class="fad fa-game-board"></i> ' + game.name + '</li>'
        }
        if (morphology != null && morphology != '') {
          str += '<li><i class="fad fa-shapes"></i> ' + morphology.name + '</li>'
        }
        str += '<li><i class="fad fa-hashtag"></i> ' + id + '</li>'
        str += '</ul>'
        return str
      },
      updateMarkers: function() {
        let lMarkers = []
        for (const marker of this.konmariMarkers) {
          lMarkers.push(marker.lMarker)
        }
        this.cluster.clearLayers()
        this.cluster.addLayers(lMarkers)
        let clusterBounds = this.cluster.getBounds()
        if (!_.isEmpty(clusterBounds)) {
          if (clusterBounds._northEast.lat != clusterBounds._southWest.lat) {
            this.map.fitBounds(clusterBounds)
          } else {
            this.map.setView([clusterBounds._northEast.lat, clusterBounds._northEast.lng], 4)
          }
        } else {
          this.map.setView([51.505, -0.09], 4)
        }
      },
      setFilter(name, value) {
        if (name === 'Games') {
          this.game = value
        } else if (name === 'Morphologies') {
          this.morphology = value
        }
      }
    },
    async created() {
      this.items = await this.fetchItems()
      for (const item of this.items) {
        if (item.lng != "NULL" && item.lat != "NULL" && item.lng != 0 && item.lat != 0) {
          this.createMarker(item.lat, item.lng, this.buildTooltip(item.id, item.title, item.game, item.morphology), item.game, item.morphology)
        }
      }
      this.cluster.addTo(this.map)
      this.updateMarkers()
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