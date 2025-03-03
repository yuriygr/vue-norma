<template>
  <section :class="$elClass">
    <slot name="default" />
  </section>
</template>

<script lang="ts" setup>
import { defineProps, computed } from 'vue'

const props = defineProps({
  title: {
    type: [Boolean, String],
    default: false
  },
  mode: {
    type: String,
    default: 'plain',
    validator(value: string) {
      return ['plain', 'card'].includes(value)
    }
  }
})

const $elClass = computed(() => {
  return [
    'group',
    'group--mode-' + props.mode
  ]
})
</script>

<style lang="scss">
.group {
  &--mode-plain {

  }

  &--mode-card {
    --group--backgroud: #f1f3f5;
    --group--border-color: #e9ecef;

    html[data-theme="black"] & {
      --group--backgroud: #181818;
      --group--border-color: #222;
    }
  }
}

.group {
  &--mode-card {
    background: var(--group--backgroud);
    border: 1px solid var(--group--border-color);
    border-radius: 8px;
    padding: 1rem 1.4rem;
  }

  & > .header {
    margin-bottom: 1.5rem;
  }

  & + & {
    margin-top: 4.51rem;
  }

}
</style>