<template>
  <div class="modal is-active">
    <div class="modal-background" @click="closeModal"></div>
    <div class="modal-content">
      <div class="card">
        <div class="card-image">
          <figure v-if="aboutImage" class="image">
            <img :src="aboutImage" alt="" />
          </figure>
        </div>
        <div class="card-content">
          <div v-html="about" class="content"></div>
          <hr>
          <div class="content">
            <div class="columns">
              <div class="column is-one-third">
                <img src="../assets/logo-erc.png">
              </div>
              <div class="column">
                This database is part of the <a href="https://locusludi.ch">Locus Ludi</a> project that has received funding from the European Research Council (ERC) under the European Union’s Horizon 2020 research and innovation programme (Grant agreement No. 741520)
              </div>
            </div>
          </div>
          <div class="content">
            <div class="columns">
              <div class="column is-one-third">
                <img src="../assets/unifr-full.png">
              </div>
              <div class="column">
                EDDB ou Educational Databases est un projet de <a href="https://nte.unifr.ch">DIT-NTE</a> permettant aux enseignants et aux chercheurs de mettre en place rapidement des bases de données pour la recherche et l'enseignement.
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
    <button class="modal-close is-large" aria-label="close" @click="closeModal"></button>
  </div>
</template>

<script>
  import utils from '../mixins/Utils.vue'
  import directus from '../mixins/Directus.vue'

  export default {
    mixins: [utils, directus],
    data() {
      return {
        about: '',
        aboutImage: ''
      }
    },
    created() {
      this.clipHtml()
      this.fetchAbout()
    }
  }
</script>