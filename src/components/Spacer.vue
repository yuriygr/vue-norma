<template>
  <div class="spacer" :style="{ '--height': props.height + 'px' }" />
</template>

<script lang="ts" setup>
import { defineProps } from 'vue'

const props = defineProps({
  height: {
    type: Number,
    default: 20
  }
})
</script>

<style lang="scss" scoped>
.spacer {
  height: var(--height);
}
</style>