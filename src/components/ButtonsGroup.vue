<template>
  <div :class="$elClass">
    <slot />
  </div>
</template>

<script lang="ts" setup>
import { defineProps, computed } from 'vue'

const props = defineProps({
  withGap: {
    type: Boolean,
    default: true
  }
})

const $elClass = computed(() => {
  return [
    'buttons-group',
    {
      'buttons-group--with-gap': props.withGap
    }
  ]
})
</script>

<style lang="scss">
.buttons-group {
  display: flex;
  justify-content: center;
  align-items: center;

  &--with-gap {
    > :not(:first-child) {
      margin-left: .5rem;
    }
  }
}
</style>