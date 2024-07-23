<template>
  <header class="modal-header">
    <div v-if="title" class="modal-header__title">{{ title }}</div>
    <div v-if="hasSlot('after')" class="modal-header__buttons modal-header__buttons--after">
      <slot name="after" />
    </div>
  </header>
</template>

<script>
export default {
  name: 'modal-header',
  props: {
    title: false
  },
  methods: {
    hasSlot(name) { return !!this.$slots[name] }
  }
}
</script>

<style lang="scss">
.modal-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  border-top-left-radius: inherit;
  border-top-right-radius: inherit;
  border-bottom: var(--modal__header--border-bottom);
  padding: var(--modal--padding);

  &__title {
    min-width: 0;
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
    line-height: 32px;
    color: var(--modal__header--color, #444);
    font-size: 1.5rem;
    font-weight: 500;
    user-select: none;
  }

  &__buttons {
    display: flex;
    flex-direction: row;
    justify-content: end;
    gap: 5px;
  }
}
</style>