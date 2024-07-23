<template>
  <div class="modal-body">
    <slot />
  </div>
</template>

<script>
export default {
  name: 'modal-body'
}
</script>

<style lang="scss">
.modal-body {
  padding: var(--modal--padding);
  position: relative;
}
</style>