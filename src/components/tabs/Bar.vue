<template>
  <nav class="tabs">
    <div class="tabs__wrapper">
      <slot />
    </div>
  </nav>
</template>

<script>
export default {
  name: 'tabs'
}
</script>

<style lang="scss">
.tabs {
  position: relative;
  width: 100%;

  &__wrapper {
    display: flex;
    justify-content: start;
    align-items: center;
    max-width: none;
    overflow: auto;

    :not(:first-child) {
      margin-left: .3rem;
    }

    &::-webkit-scrollbar {
      width: 0;
      height: 0;
    }
  }
}
</style>