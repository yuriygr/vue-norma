<template>
  <div class="separator" />
</template>

<script>
export default {
  name: 'separator'
}
</script>

<style lang="scss" scoped>
.separator {
  --separator--color: #f1f3f5;

  html[data-theme='black'] & {
    --separator--color: #242424;
  }
}

.separator {
  height: 0;
  margin: 2rem 0;
  background: transparent;
  border-bottom: 1px solid var(--separator--color);
}
</style>