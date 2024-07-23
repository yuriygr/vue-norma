<template>
  <div class="tabs-separator" />
</template>

<script>
export default {
  name: 'tabs-separator'
}
</script>

<style lang="scss" scoped>
.tabs-separator {
  height: 12px;
  width: 2px;
  background-color: var(--separator--color, #eee);
  border-radius: 10000px
}
</style>