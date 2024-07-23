<template>
  <div class="modal-checklist">
    <template v-for="(item, index) in items" :key="'modal-checklist-item-' + index">
      <div
        :class="[
          'modal-checklist__item',
          { 'modal-checklist__item--checked': item.id === current }
        ]"
        @click="$emit('change', $event, item.id)"
      >
        <div class="label">{{ item.label }}</div>
        <div class="description" v-if="item.description">{{ item.description }}</div>
      </div>
    </template>
  </div>
</template>

<script>
export default {
  name: 'modal-checklist',
  props: {
    title: {
      type: String,
      default: false
    },
    items: {
      type: Array,
      default: () => []
    },
    current: {
      type: Number,
      default: 0
    }
  }
}
</script>

<style lang="scss">
.modal-checklist {
  border-radius: var(--x-border-radius--base);
  position: relative;
  margin: 2rem var(--modal--padding);

  &__item {
    background: var(--modal__checklist-item-background);
    border-radius: var(--x-border-radius--base);
    cursor: pointer;
    padding: .75rem;
    position: relative;

    .label {
      font-size: 1.3rem;
      padding: 0 0 0 2.35rem;
      position: relative;

      &::before {
        border-radius: 50%;
        border-style: solid;
        border-width: 1.5px;
        border-color: inherit;
        position: absolute;
        left: 0px;
        width: 1.6rem;
        height: 1.6rem;
        box-sizing: border-box;
        background-clip: padding-box;
        content: "";
        top: 50%;
        margin-top: -0.8rem;
      }
      &::after {
        border-radius: 50%;
        display: block;
        content: "";
        position: absolute;
        left: 0.4rem;
        width: 0.8rem;
        height: 0.8rem;
        top: 50%;
        transform: translate3d(0px, -50%, 0px);
      }
    }

    .description {
      padding: 0 0 0 2.35rem;
      font-size: 1.1rem;
      opacity: .7;
    }
    &:not(:last-child) {
      margin-bottom: var(--modal__checklist-item-gap, .5rem);
    }
    
    &--checked {
      .label::before {
        background-color: var(--modal__checklist-item-background);
        border-color: var(--modal__checklist-item-checked-color);
      }
      .label::after {
        background-color: var(--modal__checklist-item-checked-color);
      }
    }
  }
}
</style>