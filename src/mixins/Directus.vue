<script>
  import axios from 'axios'
  import _ from 'lodash'

  export default {
    data() {
      return {
        directus: {
          api: 'https://eddb.unifr.ch/api',
          path: '/items/Entries',
          project: 'ludus',
          about: '/items/about?fields=content,media.*',
          map: {
            fields: 'id,title,morphology.*,game.*,lat,lng',
            sort: 'id',
            limit: '-1',
            status: 'published'
          },
          eddb: {
            project: '_',
            about: '/items/about',
            projects: '/items/projects?sort=title'
          }
        }
      }
    },
    methods: {
      async fetchItems() {
        try {
          const response = await axios.get(this.directus.api + '/' + this.directus.project + this.directus.path, {
            params: {
              fields: this.directus.map.fields,
              sort: this.directus.map.sort,
              limit: this.directus.map.limit,
              status: this.directus.map.status
            }
          })
          return response.data.data
        } catch (error) {
          return []
        }
      },
      async fetchFilters(filter) {
        axios.get(this.directus.api + '/' + this.directus.project + '/items/' + filter + '?fields=*&sort=name').then(result => {
          this.choices = result.data.data
        })
      },
      fetchAbout() {
        axios.get(this.directus.api + '/' + this.directus.project + this.directus.about).then(result => {
          let data = result.data.data[0]
          this.about = data.content
          if (_.has(data, 'media.data.full_url')) {
            this.aboutImage = data.media.data.full_url
          }
        })
      },
      fetchEddbAbout() {
        axios.get(this.directus.api + '/' + this.directus.eddb.project + this.directus.eddb.about).then(result => {
          this.eddbAbout = result.data.data[0].content
        })
      },
      fetchEddbProjects() {
        axios.get(this.directus.api + '/' + this.directus.eddb.project + this.directus.eddb.projects).then(result => {
          this.eddbProjects = result.data.data
        })
      },
      getThumbnail(file, size) {
        let x = 400
        let type = 'crop'
        if (size === 800 || size === 2400) {
          x = size
          type = 'contain'
        }
        return this.directus.api + '/thumbnail/' + this.directus.project + '/' + x + '/' + x + '/' + type + '/best/' + file
      }
    }
  }
</script>