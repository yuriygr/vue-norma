<template>
  <div :class="[ 'modal', 'modal--' + size ]">
    <slot />
  </div>
</template>

<script>
export default {
  name: 'modal',
  props: {
    size: {
      default: 'normal',
      validator(value) {
        return ['small', 'normal', 'wide'].includes(value)
      }
    }
  }
}
</script>

<style lang="scss">
.modal {
  --modal--background: var(--x-background);
  --modal--box-shadow: 0 10.5px 21px rgba(0,0,0,.08);
  --modal--border-color: #e9e9e9;

  --modal__header--color: #444;
  --modal__header--border-bottom: 1px solid #e9e9e9;

  --modal__search--background: var(--x-background);
  --modal__search--color: #444;

  html[data-theme="black"] & {
    --modal--background: var(--x-background);
    --modal--box-shadow: 0 10.5px 21px rgba(0,0,0,.08);
    --modal--border-color: #1c1c1c;

    --modal__header--color: #ddd;
    --modal__header--border-bottom: 1px solid #1c1c1c;

    --modal__search--background: var(--x-background);
    --modal__search--color: #ddd;
  }
}

.modal {
  background: var(--modal--background);
  border: 1px solid var(--modal--border-color);
  border-radius: 12px;
  box-shadow: var(--modal--box-shadow);
  z-index: calc(var(--z-index) + 1);
  position: relative;
  margin: 5rem auto;
  overflow: hidden;

  &--wide {
    max-width: 890px;
    @media (max-width: 905px) {
      border-radius: 0;
      border-top: none;
      border-left: none;
      border-right: none;
      margin: 0 auto;
    }
  }

  &--normal {
    max-width: 650px;
    @media (max-width: 665px) {
      border-radius: 0;
      border-top: none;
      border-left: none;
      border-right: none;
      margin: 0 auto;
    }
  }

  &--small {
    max-width: 490px;
    @media (max-width: 505px) {
      border-radius: 0;
      border-top: none;
      border-left: none;
      border-right: none;
      margin: 0 auto;
    }
  }

  &__search {
    padding: .5rem var(--modal--padding);
    border-bottom: var(--modal__header--border-bottom);

    .search {
      background: var(--modal__search--background);
      color: var(--modal__search--color);
      position: relative;
      display: flex;
      align-items: center;
      flex-wrap: nowrap;

      &__icon {
        margin: 0;
        padding: 0;
        cursor: pointer;
        border: none;
        outline: none;
        user-select: none;

        width: 32px;
        height: 32px;
        display: flex;
        align-items: center;
        justify-content: center;
        font-size: 12px;

        transition: var(--x-transition);

        svg { fill: currentColor; flex-shrink: 0; opacity: .5;}
      }

      &__input {
        color: var(--modal__search--color);
        font-size: 14px;
        outline: 0;
        border: 0;
        margin: 0;
        padding: 0;
        background: transparent;
        position: relative;
        width: 100%;
        height: 100%;
      }
    }

  }

  &__footer {
    display: flex;
    justify-content: space-between;
    align-items: center;
    border-bottom-left-radius: inherit;
    border-bottom-right-radius: inherit;
    padding: calc(var(--modal--padding) * 1.5) var(--modal--padding) var(--modal--padding);
  }
}
</style>