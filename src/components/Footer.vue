<template>
  <footer class="footer">
    <div class="container">
      <div class="columns is-variable is-8">
        <div class="column is-6">
          <aside class="menu">
            <p class="menu-label">
              About EDDB
            </p>
            <span v-html="eddbAbout"></span>
          </aside>
        </div>
        <div class="column is-6">
          <aside class="menu">  
            <p class="menu-label">
              EDDB databases
            </p>
            <div class="columns is-multiline is-variable is-2">
              <div class="column is-one-third" v-for="project of eddbProjects" :key="project.id">
                <a class="button is-primary is-outlined is-fullwidth" :href="project.url">{{ project.title }}</a>
              </div>
            </div>
          </aside>
        </div>
      </div>
      <hr>
      <div class="columns is-variable is-8">
        <div class="column is-3">
          <img src="../assets/unifr-full.png">
        </div>
        <div class="column has-text-right">
          © 2019 Centre NTE, Université de Fribourg
        </div>
      </div>
    </div>
  </footer>
</template>

<script>
  import directus from '../mixins/Directus.vue'

  export default {
    mixins: [directus],
    data() {
      return {
        eddbProjects: [],
        eddbAbout: ''
      }
    },
    created() {
      this.fetchEddbProjects()
      this.fetchEddbAbout()
    }
  }
</script>