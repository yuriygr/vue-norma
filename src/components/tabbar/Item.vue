<template>
  <template v-if="isHasLink">
    <router-link v-if="isInnerLink" custom v-slot="{ isActive, href, navigate }" :to="to">
      <a
        :href="href"
        :target="target"
        @click="navigate"
        :class="[ 'tabbar-item', { 'tabbar-item--active': preActive || (preActive || isActive), 'tabbar-item--disabled': disabled } ]"
        :title="title"
      >
        <div class="tabbar-item__icon">
          <span v-if="isHasBadge" class="tabbar-item__badge"></span>
          <slot :isActive="isActive" />
        </div>
        <div v-if="showTitle" class="tabbar-item__title">{{ title }}</div>
      </a>
    </router-link>

    <a v-else-if="isExternalLink"
      :href="to"
      :class="[ 'tabbar-item', { 'tabbar-item--active': preActive, 'tabbar-item--disabled': disabled } ]"
      :title="title"
      :target="target"
    >
      <div class="tabbar-item__icon">
        <span v-if="isHasBadge" class="tabbar-item__badge"></span>
        <slot />
      </div>
      <div v-if="showTitle" class="tabbar-item__title">{{ title }}</div>
    </a>
  </template>

  <div v-else
    :class="[ 'tabbar-item', { 'tabbar-item--active': preActive, 'tabbar-item--disabled': disabled } ]"
    :title="title"
    @click="clickEvent"
  >
    <div class="tabbar-item__icon">
      <span v-if="isHasBadge" class="tabbar-item__badge"></span>
      <slot />
    </div>
    <div v-if="showTitle" class="tabbar-item__title">{{ title }}</div>
  </div>
</template>

<script>
export default {
  name: 'tabbar-item',
  props: {
    title: {
      type: [ Boolean, String ],
      default: false
    },
    to: {
      type: [ String, Object ],
      default: false
    },
    disabled: {
      type: Boolean,
      default: false
    },
    preActive: {
      type: Boolean,
      default: false
    },
    badge: {
      type: [ Boolean, String, Number ],
      default: false
    },
    showTitle: {
      type: Boolean,
      default: false
    },
    target: {
      type: String,
      default: '_self'
    }
  },
  emits: [ 'click' ],
  computed: {
    isHasBadge() {
      return this.badge
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
  },
  methods: {
    clickEvent(e) {
      this.$emit('click', e)
    }
  }
}
</script>

<style lang="scss">
.tabbar-item {
  --tabbar-item--color: #868e96;
  --tabbar-item--color-hover: #212529;
  --tabbar-item--color-active: #212529;
  --tabbar-item__badge--color: #e03131;
  --tabbar-item__badge--border: var(--x-background);

  html[data-theme='black'] & {
    --tabbar-item--color: #9f9f9f;
    --tabbar-item--color-hover: #fffcea;
    --tabbar-item--color-active: #fffcea;

    --tabbar-item__badge--color: #e03131;
    --tabbar-item__badge--border: var(--x-background);
  }
}

.tabbar-item {
  $parent: &;

  background: transparent;
  color: var(--tabbar-item--color);
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  outline-style: none;
  cursor: pointer;
  user-select: none;
  list-style: none;
  position: relative;
  margin: 4px 2px;
  padding: 10px 16px;
  outline: none;
  transition: var(--x-transition);

  &--active {
    color: var(--tabbar-item--color-active);
  }

  @media(hover: hover) {
    &:hover {
      color: var(--tabbar-item--color-hover);
      text-decoration: none;
    }
  }

  &__icon {
    position: relative;
    svg { fill: currentColor; flex-shrink: 0; display: block; }
  }

  &__title {
    font-size: 1rem;
    line-height: calc(1.2 * 1em);
    word-break: break-all;
    user-select: none;
  }

  &__badge {
    position: absolute;
    right: -5px;
    top: -5px;

    background-color: var(--tabbar-item__badge--color);
    border-radius: 50%;
    border: 2px solid var(--tabbar-item__badge--border);
    display: block;
    flex-grow: 0;
    flex-shrink: 0;
    height: 10px;
    width: 10px;
    user-select: none;
  }
}
</style>