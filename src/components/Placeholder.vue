<template>
  <div :class="$elClass">
    <div v-if="isHasIcon" class="placeholder__icon">
      <icon :name="icon" size="56"/>
    </div>
    <div v-if="isHasHeader" class="placeholder__header">{{ header }}</div>
    <div v-if="isHasText" class="placeholder__text">{{ text }}</div>
  </div>
</template>

<script lang="ts" setup>
import { defineProps, computed } from 'vue'

import Icon from '@/components/Icon.vue'

const props = defineProps({
  icon: {
    type: String,
    default: ''
  },
  header: {
    type: String,
    default: ''
  },
  text: {
    type: String,
    default: ''
  },
  size: {
    type: String,
    default: 'm',
    validator(value) {
      return ['s', 'm', 'l'].includes(value)
    }
  }
})

const isHasIcon = computed(() => {
  return props.icon != ''
})

const isHasHeader = computed(() => {
  return props.header != ''
})

const isHasText = computed(() => {
  return props.text != ''
})

const $elClass = computed(() => {
  return [
    'placeholder',
    'placeholder--size-' + props.size
  ]
})
</script>

<style lang="scss">
.placeholder {
  &--size-s {
    --padding: 5rem 1rem;
  }
  &--size-m {
    --padding: 10rem 1rem;
  }
  &--size-l {
    --padding: 15rem 1rem;
  }
}

.placeholder {
  --placeholder-text--color: #868e96;

  html[data-theme='black'] & {
    --placeholder-text--color: #aaa;
  }
}

.placeholder {
  display: flex;
  align-items: center;
  box-sizing: border-box;
  flex-direction: column;
  justify-content: center;
  color: inherit;
  text-align: center;
  padding: var(--padding);
  user-select: none;

  &__icon {
    display: inline-block;
    vertical-align: top;

    svg { fill: currentColor; flex-shrink: 0; display: block; }
  }

  &__header {
    font-weight: 400;
    margin: 0;
    padding: 0;
    font-size: 2.2rem;
    line-height: calc(1.6 * 1em);
  }

  &__text {
    color: var(--placeholder-text--color, inherit);
    font-weight: 400;
    margin: 0;
    padding: 0;
    font-size: 1.5rem;
    line-height: calc(1.6 * 1em);
  }

  &__header + &__text {
    margin-top: 7px;
  }
}
</style>