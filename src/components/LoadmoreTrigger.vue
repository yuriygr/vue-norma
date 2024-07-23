<template>
  <div ref="trigger"><!-- Привет, как дела? Как погода? --></div>
</template>

<script>
export default {
  name: 'loadmore-trigger',
  props: {
    options: {
      type: Object,
      default: { root: null, threshold: 0 }
    }
  },
  data() {
    return { observer: null }
  },
  methods: {
    handleIntersect(entry) {
      if (entry.isIntersecting) {
        this.$emit('intersected')
      }
    }
  },
  mounted() {
    this.observer = new IntersectionObserver(entries => {
      this.handleIntersect(entries[0])
    }, this.options)

    this.observer.observe(this.$refs.trigger)
  },
  destroyed() {
    this.observer.disconnect()
  }
}
</script>