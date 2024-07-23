<template>
  <router-link :class="elClass" :to="to" active-class="navigation-item--active" exact-active-class="">
    <div v-if="icon" class="navigation-item__icon"><icon :name="icon" size="22" /></div>
    <div class="navigation-item__content">
      <div class="navigation-item__label"><slot /></div>
    </div>
    <div v-if="chevron" class="navigation-item__chevron">
      <icon name="chevron-line" size="18" />
    </div>
  </router-link>
</template>

<script>
import Icon from '@/components/Icon.vue'

export default {
  name: 'navigation-item',
  components: { Icon },
  props: {
    to: {
      type: [ Object ],
      default: false 
    },
    icon: {
      type: [ String, Boolean ],
      default: false 
    },
    chevron: {
      type: [ Boolean ],
      default: true 
    },
    disabled: {
      type: Boolean,
      default: false
    }
  },
  computed: {
    elClass() {
      return [
        'navigation-item',
        {
          'navigation-item--disabled': this.disabled
        }
      ]
    },
    isHasLink() {
      return typeof this.to === 'string' || typeof this.to === 'object'
    },
    isInnerLink() {
      return typeof this.to === 'object'
    },
    isExternalLink() {
      return typeof this.to === 'string' && this.to.startsWith('http')
    }
  }
}
</script>

<style lang="scss">
.navigation-item {
  --navigation-item--color: #343a40;
  --navigation-item--color-hover: #212529;
  --navigation-item__icon--color: #343a40;
  --navigation-item__icon--color-hover: #212529;
  --navigation-item__chevron--color: #adb5bd;
  --navigation-item__chevron--color-hover: #212529;

  html[data-theme='black'] & {
    --navigation-item--color: #aaa;
    --navigation-item--color-hover: #fffcea;
    --navigation-item__icon--color:  #aaa;
    --navigation-item__icon--color-hover: #fffcea;
    --navigation-item__chevron--color: #666;
    --navigation-item__chevron--color-hover: #888;
  }
}

.navigation-item {
  background: transparent;
  color: var(--navigation-item--color);
  display: flex;
  align-items: center;
  user-select: none;
  padding: 8px;
  border-radius: 8px;

  & + & {
    margin-top: .25rem;
  }

  &--active {
    color: var(--navigation-item--color-hover);
  }

  &--disabled {
    opacity: 0.4;
    pointer-events: none;
    cursor: default;
    @media(hover: hover) {
      &:hover {
        background: transparent;
        color: var(--navigation-item--color);
      }
    }
  }

  @media(hover: hover) {
    &:hover {
      color: var(--navigation-item--color-hover);
      text-decoration: none;
    }
  }

  &__icon {
    margin-right: 10px;
    padding: 7px;
    color: var(--navigation-item__icon--color);

    svg { display: block; fill: currentColor; }

    .navigation-item--active & {
      color: var(--navigation-item__icon--color-hover);
    }

    @media(hover: hover) {
      :hover > & {
        color: var(--navigation-item__icon--color-hover);
      }
    }
  }

  &__content {
    flex: 1;
  }

  &__label { }

  &__chevron {
    display: flex;
    justify-content: center;
    align-items: center;
    color: var(--navigation-item__chevron--color);
    transition: var(--x-transition);

    svg { display: block; fill: currentColor; }

    .navigation-item--active & {
      color: var(--navigation-item__chevron--color-hover);
    }

    @media(hover: hover) {
      :hover > & {
        color: var(--navigation-item__chevron--color-hover);
      }
    }
  }
}
</style>