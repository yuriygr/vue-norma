<template>
  <div class="icons-sprite-layer" ref="container" />
</template>
<script>
export default {
  name: 'icons-sprite-layer',
  props: {
    path: {
      type: String,
      default: ''
    },
    cacheKey: {
      type: String,
      default: 'svg_icons'
    }
  },
  data() {
    return {
      svg: ''
    }
  },
  methods: {
    loadSVGSpriteFromCache() {
      let data = localStorage.getItem(this.cacheKey)
      if (data)
        this.svg = data
    },

    loadSVGSpriteFromURL() {
      var req = new XMLHttpRequest()

      req.onload = (e) => {
        this.svg = e.target.response
        localStorage.setItem(this.cacheKey,	e.target.response)
      }

      req.open('GET', this.path, true)
      req.responseType = 'text'
      req.send()
    }
    
  },
  mounted() {
    this.loadSVGSpriteFromCache()
    this.loadSVGSpriteFromURL()
  },
  watch: {
    svg(to) {
      let $svg = document.implementation.createHTMLDocument('')
      $svg.body.innerHTML = to

      this.$refs.container.appendChild($svg.body.firstElementChild)
      $svg = null
    }
  }
}
       // if ("caches" in window) {
       //   caches.open('static').then((cache) => {
       //     cache.put(this.path, new Response(
       //       e.target.response,
       //     ));
       //   })
       // }        if ("caches" in window) {
       //   caches.match(this.path).then(response => {
       //     console.log(response)
       //   })
       // }
</script>

<style lang="scss">
.icons-sprite-layer {
  position: absolute;
  width: 0;
  height: 0;
  overflow: hidden;
  z-index: -9999;
}
</style>