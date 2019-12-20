<template>
  <div class="level-item">
    <div @click="toggle" class="dropdown" v-bind:class="{ 'is-active': isActive }">
      <div class="dropdown-trigger">
        <button class="button" aria-haspopup="true" aria-controls="dropdown-menu">
          <span>{{ title }}</span>
          <span class="icon is-small">
            <i class="fas fa-angle-down" aria-hidden="true"></i>
          </span>
        </button>
      </div>
      <div class="dropdown-menu" id="dropdown-menu" role="menu">
        <div class="dropdown-content">
          <a v-for="choice in choices" :key="choice.id" href="#" class="dropdown-item" @click="pick(choice)">
            {{ choice.name }}
          </a>
        </div>
      </div>
    </div>
    <br>
    <span class="tag is-primary is-medium" v-if="choice">
      {{ choice.name }}
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