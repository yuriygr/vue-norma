<template>
  <template v-if="isHasLink">
    <router-link v-if="isInnerLink" custom v-slot="{ isActive, href, navigate }" :to="to">
      <a
        :href="href"
        :target="target"
        @click="navigate"
        :class="[ 'tabs-item', 'tabs-item--size-' + this.size, { 'tabs-item--selected': selected ?? (isActive || selected), 'tabs-item--disabled': disabled } ]"
      >
        <span class="tabs-item__icon" v-if="icon">
          <icon :name="icon" :size="iconSize" />
        </span>
        <span class="tabs-item__label" v-if="hasSlot('default')"><slot/></span>
      </a>
    </router-link>

    <a v-else-if="isExternalLink"
      :href="to"
      :target="target"
      :class="[ 'tabs-item', 'tabs-item--size-' + this.size, { 'tabs-item--selected': selected, 'tabs-item--disabled': disabled } ]"
    >
      <span class="tabs-item__icon" v-if="icon">
        <icon :name="icon" :size="iconSize" />
      </span>
      <span class="tabs-item__label" v-if="hasSlot('default')"><slot /></span>
    </a>
  </template>

  <div v-else
    :class="[ 'tabs-item', 'tabs-item--size-' + this.size, { 'tabs-item--selected': selected, 'tabs-item--disabled': disabled } ]"
  >
    <span class="tabs-item__icon" v-if="icon">
      <icon :name="icon" :size="iconSize" />
    </span>
    <span class="tabs-item__label" v-if="hasSlot('default')"><slot /></span>
  </div>
</template>

<script>
import Icon from '@/components/Icon.vue'

export default {
  name: 'tabs-item',
  components: {
    Icon
  },
  props: {
    to: {
      type: [ Boolean, String, Object ],
      default: false
    },
    icon: {
      type: [ Boolean, String ],
      default: false
    },
    size: {
      type: String,
      default: 's',
      validator(value) {
        return ['xs', 's', 'm', 'l'].includes(value)
      }
    },
    selected: {
      type: Boolean,
      default: false
    },
    disabled: {
      type: Boolean,
      default: false
    },
    target: {
      type: String,
      default: '_self'
    }
  },
  computed: {
    isHasLink() {
      return typeof this.to === 'string' || typeof this.to === 'object'
    },
    isInnerLink() {
      return typeof this.to === 'object'
    },
    isExternalLink() {
      return typeof this.to === 'string' && this.to.startsWith('http')
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
  },
  methods: {
    hasSlot(name) { return !!this.$slots[name] }
  }
}
</script>

<style lang="scss">
.tabs-item {
  &--size-xs {
    --tabs-item--height: 24px;
    --tabs-item--font-size: 1.2rem;
    --tabs-item--padding: 5px;
  }
  &--size-s {
    --tabs-item--height: 28px;
    --tabs-item--font-size: 1.3rem;
    --tabs-item--padding: 10px;
  }
  &--size-m {
    --tabs-item--height: 32px;
    --tabs-item--font-size: 1.4rem;
    --tabs-item--padding: 12px;
  }
  &--size-l {
    --tabs-item--height: 38px;
    --tabs-item--font-size: 1.5rem;
    --tabs-item--padding: 16px;
  }
}

.tabs-item {
  --tabs-item--background: transparent;
  --tabs-item--color: #868e96;
  
  --tabs-item--background-hover: transparent;
  --tabs-item--color-hover: #212529;

  --tabs-item--background-selected: #f1f3f5;
  --tabs-item--color-selected: #212529;

  html[data-theme="black"] & {
    --tabs-item--background: transparent;
    --tabs-item--color: #828282;

    --tabs-item--background-hover: #transparent;
    --tabs-item--color-hover: #f2f2f0;

    --tabs-item--background-selected: #1f1f1f;
    --tabs-item--color-selected: #f2f2f0;
  }
}

.tabs-item {
  background: var(--tabs-item--background);
  color: var(--tabs-item--color);
  display: flex;
  flex-direction: row;
  align-items: center;
  justify-content: center;
  outline-style: none;
  cursor: pointer;
  user-select: none;
  list-style: none;
  position: relative;
  outline: none;
  border-radius: 8px;
  height: var(--tabs-item--height);
  transition: var(--x-transition);

  &__icon {
    padding-left: var(--tabs-item--padding);
    svg { fill: currentColor; flex-shrink: 0; display: block; }
  }

  &__label {
    white-space: nowrap;
    word-break: keep-all;
    font-size: var(--tabs-item--font-size);
    line-height: calc(1.2 * 1em);
    font-weight: 500;

    &:last-child {
      padding-right: var(--tabs-item--padding);
    }
    &:first-child {
      padding-left: var(--tabs-item--padding);
    }
  }


  &__icon:not(:last-child) + &__label {
    margin-left: 6px;
  }
  &__icon:only-child {
    padding-right: var(--tabs-item--padding);
  }

  &--selected {
    background: var(--tabs-item--background-selected);
    color: var(--tabs-item--color-selected);

    @media(hover: hover) {
      &:hover {
        background: var(--tabs-item--background-selected);
        color: var(--tabs-item--color-selected);
        text-decoration: none;
      }
    }
  }

  &--disabled {
    opacity: 0.4;
    pointer-events: none;
    cursor: default;

    @media(hover: hover) {
      &:hover {
        background: var(--tabs-item--background);
        color: var(--tabs-item--color);
        text-decoration: none;
      }
    }
  }

  @media(hover: hover) {
    &:not(&--selected):not(&--disabled):hover {
      background: var(--tabs-item--background-hover);
      color: var(--tabs-item--color-hover);
      text-decoration: none;
    }
  }
}
</style>