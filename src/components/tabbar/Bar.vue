<template>
  <nav class="tabbar">
    <slot />
  </nav>
</template>

<script>
export default {
  name: 'tabbar'
}
</script>

<style lang="scss">
.tabbar {
  display: grid;
  grid-template-columns: repeat(var(--tabbar-item-count), 20%);
  justify-content: space-between;
  margin: 0 auto;
  width: 100%;
}
</style>