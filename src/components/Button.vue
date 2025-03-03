<template>
  <component :is="component" :class="elClass">
    <div class="button__container">
      <span class="button__icon button__icon--before" v-if="icon_before">
        <icon :name="icon_before" :size="iconSize" />
      </span>
      <span class="button__label" v-if="hasSlot('default')"><slot /></span>
      <span class="button__icon button__icon--after" v-if="icon_after">
        <icon :name="icon_after" :size="iconSize" />
      </span>
    </div>
    <span v-if="isHasBadge" class="button__badge"></span>
  </component>
</template>

<script>
import Icon from '@/components/Icon.vue'

export default {
  name: 'n-button',
  components: {
    Icon
  },
  props: {
    icon_before: {
      type: [ String, Boolean ],
      default: false
    },
    icon_after: {
      type: [ String, Boolean ],
      default: false
    },
    // Mods
    size: {
      type: String,
      default: 'm',
      validator(value) {
        return ['xs', 's', 'm', 'l'].includes(value)
      }
    },
    stretched: {
      type: Boolean,
      default: false
    },
    // State
    loading: {
      type: Boolean,
      default: false
    },
    disabled: {
      type: Boolean,
      default: false
    },
    active: {
      type: Boolean,
      default: false
    },
    // Other
    component: {
      type: String,
      default: 'button'
    },
    mode: {
      type: String,
      default: 'primary',
      validator(value) {
        return ['primary', 'secondary', 'tertiary', 'outline', 'link'].includes(value)
      }
    },
    badge: {
      type: Boolean,
      default: false
    }
  },
  computed: {
    elClass() {
      return [
        'button',
        'button--size-' + this.size,
        'button--mode-' + this.mode,
        {
          'button--singe-icon': (!!this.icon_before ^ !!this.icon_after) && !this.hasSlot('default'),
          'button--has-icon': (!!this.icon_before || !!this.icon_after),
          'button--stretched': this.stretched,
          'button--loading': this.loading,
          'button--disabled': this.disabled,
          'button--active': this.active
        }
      ]
    },
    iconSize() {
      switch (this.size) {
        case 'xs':
          return 12;
        case 's':
          return 14;
        case 'm':
          return 16;
        case 'l':
          return 18;
        default:
          return 16;
      }
    },
    isHasBadge() { return this.badge }
  },
  methods: {
    hasSlot(name) { return !!this.$slots[name] }
  }
}
</script>

<style lang="scss">
.button {
  &--size-xs {
    --button--button-size: 24px;
    --button--font-size: 1.2rem;
    --button--padding: 5px;
  }
  &--size-s {
    --button--button-size: 28px;
    --button--font-size: 1.3rem;
    --button--padding: 10px;
  }
  &--size-m {
    --button--button-size: 32px;
    --button--font-size: 1.4rem;
    --button--padding: 12px;
  }
  &--size-l {
    --button--button-size: 38px;
    --button--font-size: 1.4rem;
    --button--padding: 16px;
  }
}

.button {
  --button__badge--color: #e03131;

  &--mode-primary {
    --button--background: #212529;
    --button--color: #f8f9fa;
    --button--background-hover: #343a40;
    --button--color-hover: #f8f9fa;

    html[data-theme='black'] & {
      --button--background: #f2f2f0;
      --button--color: #1d1d1d;
      --button--background-hover: #e5e5e3;
      --button--color-hover: #1d1d1d;
    }
  }

  &--mode-secondary {
    --button--background: #f1f3f5;
    --button--color: #343a40;
    --button--background-hover: #e9ecef;
    --button--color-hover: #212529;

    html[data-theme='black'] & {
      --button--background: #1f1f1f;
      --button--color: #f0f0f0;
      --button--background-hover: #242424;
      --button--color-hover: #f0f0f0;
    }
  }

  &--mode-tertiary {
    --button--background: transparent;
    --button--color: #495057;
    --button--background-hover: #e9ecef;
    --button--color-hover: #212529;

    html[data-theme='black'] & {
      --button--background: transparent;
      --button--color: #999;
      --button--background-hover: #242424;
      --button--color-hover: #f0f0f0;
    }
  }

  &--mode-outline {
    --button--background: transparent;
  }
}

.button {
  $parent: &;

  background: var(--button--background);
  color: var(--button--color);
  margin: 0;
  padding: 0;
  cursor: pointer;
  user-select: none;
  border: none;
  outline: none;
  appearance: none;
  position: relative;
  box-shadow: none;
  max-width: 100%;
  min-height: var(--button--button-size);
  min-width: var(--button--button-size);
  display: inline-block;
  border-radius: 8px;
  transition: var(--x-transition);
  font-family: var(--x-font-family);

  @media(hover: hover) {
    &:hover {
      background: var(--button--background-hover);
      color: var(--button--color-hover);
      text-decoration: none;
    }
  }

  &__badge {
    position: absolute;
    right: 4px;
    top: 4px;

    background-color: var(--button__badge--color);
    border-radius: 50%;
    display: block;
    height: 6px;
    width: 6px;
  }

  &__container {
    position: relative;
    align-items: center;
    box-sizing: border-box;
    display: flex;
    justify-content: center;
    min-height: inherit;
    text-align: center;
    width: 100%;
  }

  &__icon {
    svg { fill: currentColor; flex-shrink: 0; display: block; }
    &--before {
      padding-left: var(--button--padding);
    }
    &--after {
      padding-right: var(--button--padding);
    }
  }
  
  &__label {
    white-space: nowrap;
    word-break: keep-all;
    font-size: var(--button--font-size);
    line-height: calc(1.2 * 1em);
    font-weight: 500;

    &:last-child {
      padding-right: var(--button--padding);
    }
    &:first-child {
      padding-left: var(--button--padding);
    }
  }

  &--singe-icon {
    #{$parent}__icon--after,
    #{$parent}__icon--before {
      margin-left: auto;
      margin-right: auto;
      padding-left: 0;
      padding-right: 0;
    }
  }

  &__icon--after:not(:first-child),
  &__icon--before:not(:last-child) + &__label {
    margin-left: 6px;
  }

  &--stretched {
    display: block;
    flex-basis: 0;
    flex-grow: 1;
    width: 100%;
  }

  &--active {
    background: var(--button--background-hover);
    box-shadow: 0 1px 1px rgba(32,35,51,.04), 0 2px 4px rgba(32,35,51,.02);
  }

  &--disabled {
    opacity: 0.4;
    pointer-events: none;
    cursor: default;
    @media(hover: hover) {
      &:hover {
        background: var(--button--background);
        color: var(--button--color);
      }
    }
  }
}
</style>