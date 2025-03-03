<template>
  <div class="header">
    <slot />
  </div>
</template>

<script>
export default {
  name: 'n-header'
}
</script>

<style lang="scss" scoped>
.header {
  white-space: pre-line;
  word-wrap: break-word;
  word-break: break-word;
  font-weight: var(--x-font-weight--bold);
  font-size: 2rem;
  line-height: calc(1.5 * 1em);
}
</style>