<template>
  <div class="navigation__footer">
    <slot />
  </div>
</template>

<script>
export default {
  name: 'navigation-footer'
}
</script>

<style lang="scss">
.navigation__footer {
  color: var(--navigation__footer--color, #999);
  margin-top: 1.5rem;
  padding: 10px 14px;
  text-align: center;
  font-size: 1.4rem;
  line-height: calc(1.4 * 1em);

  & > a {
    color: var(--navigation__footer--color, #999);
    text-decoration: underline;

    @media(hover: hover) {
      &:hover {
        color: var(--x-color, #111);
      }
    }
  }
}
</style>