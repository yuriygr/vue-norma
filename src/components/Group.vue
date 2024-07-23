<template>
  <section :class="elClass">
    <slot name="default" />
  </section>
</template>

<script>
export default {
  name: 'group',
  props: {
    title: {
      type: [Boolean, String],
      default: false
    },
    mode: {
      type: String,
      default: 'plain',
      validator(value) {
        return ['plain', 'card'].includes(value)
      }
    }
  },
  computed: {
    elClass() {
      return [
        'group',
        'group--mode-' + this.mode
      ]
    }
  },
  methods: {
    hasSlot(name) { return !!this.$slots[name] },
  }
}
</script>

<style lang="scss">
.group {
  &--mode-plain {

  }

  &--mode-card {
    
  }
}

.group {
  & > .header {
    margin-bottom: 1rem;
  }

  & + & {
    margin-top: 4.51rem;
  }

}
</style>