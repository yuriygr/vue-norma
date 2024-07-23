<template>
  <svg :height="dimensions.height" :width="dimensions.width" :class="$elClass">
    <use :xlink:href="'#'+ name"></use>
  </svg>
</template>

<script lang="ts" setup>
import { defineProps, computed } from 'vue'

const props = defineProps({
  name: {
    type: String,
    default: "",
    requires: true
  },
  width: {
    type: [String, Number],
    default: 16
  },
  height: {
    type: [String, Number],
    default: 16
  },
  size: {
    type: [Boolean, String, Number],
    default: false
  }
})

const dimensions = computed(() => {
  return {
    height: props.size ? props.size : props.height,
    width: props.size ? props.size : props.width
  }
})

const $elClass = computed(() => {
  return [
    'icon',
    'icon--' + props.name
  ]
})
</script>