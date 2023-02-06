<template>
  <div class="level-item">
    <div @click="toggle" class="dropdown" v-bind:class="{ 'is-active': isActive }">
      <div class="dropdown-trigger">
        <button class="button" aria-haspopup="true" aria-controls="dropdown-menu">
          <span>{{ title }}</span>
          <span class="icon is-small">
            <svg xmlns="http://www.w3.org/2000/svg" class="icon icon-tabler icon-tabler-chevron-down" width="24" height="24" viewBox="0 0 24 24" stroke-width="2" stroke="currentColor" fill="none" stroke-linecap="round" stroke-linejoin="round">
              <path stroke="none" d="M0 0h24v24H0z" fill="none"></path>
              <path d="M6 9l6 6l6 -6"></path>
            </svg>
          </span>
        </button>
      </div>
      <div class="dropdown-menu" id="dropdown-menu" role="menu">
        <div class="dropdown-content">
          <a v-for="choice in choices" :key="choice.id" href="#" class="dropdown-item" @click="pick(choice)">
            {{ choice.title }}
          </a>
        </div>
      </div>
    </div>
    <br>
    <span class="tag is-primary is-medium" v-if="choice">
      {{ choice.title }}
      <button @click="pick('')" class="delete is-small"></button>
    </span>
  </div>
</template>

<script>
  import _ from 'lodash'
  import directus from '../mixins/Directus.vue'

  export default {
    props: ['name'],
    mixins: [directus],
    data() {
      return {
        choices: [],
        isActive: false,
        choice: ''
      }
    },
    computed: {
      title: function() {
        return _.replace(this.name, '_', ' ')
      },
    },
    async created() {
      this.fetchFilters(this.name)
    },
    methods: {
      toggle: function() {
        this.isActive = !this.isActive
      },
      pick: function(value) {
        this.choice = value
        this.$emit('pick', this.name, this.choice)
      }
    }
  }
</script>

<style scoped>
  .dropdown-content {
    max-height: 50em;
    overflow: auto;
  }
</style>