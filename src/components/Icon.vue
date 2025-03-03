<template>
  <svg :height="dimensions.height" :width="dimensions.width" :class="$elClass">
    <use :xlink:href="'#'+ name"></use>
  </svg>
</template>

<script lang="ts" setup>
import { withDefaults, defineProps, computed } from 'vue'

export interface IconProps {
  name: string
  width?: number
  height?: number
  size?: number
}

const props = withDefaults(defineProps<IconProps>(), {
  name: "",
  width: 16,
  height: 16
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