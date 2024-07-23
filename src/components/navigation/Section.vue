<template>
  <div class="navigation__section">
    <slot />
  </div>
</template>

<script>
export default {
  name: 'navigation-section'
}
</script>

<style lang="scss">
.navigation__section {
  & + & {
    margin-top: 1.75rem;
  }
}
</style>