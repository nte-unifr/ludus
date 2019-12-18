<script>
  import he from 'he'
  import st from 'striptags'

  export default {
    methods: {
      closeModal() {
        this.$router.push('/')
        document.documentElement.classList.remove("is-clipped")
      },
      clipHtml() {
        document.documentElement.classList.add("is-clipped")
      },
      pretty(text) {
        let pretty = ''
        if (text) {
          pretty = he.decode(text)
          pretty = st(pretty)
        }
        return pretty
      }
    }
  }
</script>