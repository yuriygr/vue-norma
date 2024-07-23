<template>
  <div
    :ref="'popover'"
    :class="['popover', {
      'popover--scrolled': !isScrolledTop
    }]"
    :style="popoverStyles"

    @keydown.prevent.down="navigate(Direction.Down)"
    @keydown.prevent.up="navigate(Direction.Up)"
    @keydown.prevent.enter="selectFocusedItem()"
  >

  <div v-if="title && !isTitleScrollable || isSearchEnabled" class="popover__header">
    <div v-if="title && !isTitleScrollable" class="popover-title">
      <div class="popover-title__label">{{ title }}</div>
    </div>
    <div v-if="isSearchEnabled" class="popover-search">
      @TODO
    </div>
  </div>
  <div class="popover__main">
    <div ref="popoverScrollable" class="popover__scrollable">
      <div v-if="sections.length" ref="popoverContent" class="popover__content">
        <span ref="popoverContentHorizon" class="popover__content-horizon"></span>
        <template v-for="(section, index) in sections" :key="'popover-item-' + index">
          <div class="popover__section">
            <div v-if="section.title" class="popover-title">
              <div class="popover-title__label">{{ section.title }}</div>
            </div>
            <div class="popover__list">
              <template v-for="(item, index) in section.items" :key="item.label + '-' + index">
                <component :is="item.to ? 'router-link' : item.url ? 'a' : 'div'"
                  :class="['popover-item', {
                    'popover-item--with-details': item.details,
                    'popover-item--selected':     isItemSelected(item),
                    'popover-item--focused':      isItemFocused(item) && isNavigationStarted,
                    'popover-item--disabled':     item.disabled || isItemDisabled(item)
                  }]"
                  :ref="isItemFocused(item) ? 'focusedItem' : null"
                  :refInFor="true"
                  v-bind="itemProps(item)"
                  @click="selectItem(item, $event)"
                >
                  <div v-if="item.image" class="popover-item__image" v-lazy:background-image="getImageUrl(item.image, 22)"></div>
                  <div v-if="item.icon" class="popover-item__icon">
                    <icon :name="item.icon" height="20" width="20" />
                  </div>
                  <div class="popover-item__main">
                    <div
                      class="popover-item__label"
                      :ref="item === lastVisibleItem ? 'lastVisibleNode' : null"
                      :refInFor="true"
                      :style="{ color: item.color }"
                      v-text="item.label"
                    ></div>
                  </div>
                </component>
              </template>
            </div>
          </div>
        </template>
      </div>
      <div v-else class="popover__content">
        <div class="popover-title popover-title--centered">
          <template v-if="isLoading">
            {{ $t("Loading...") }}
          </template>
          <template v-else>
            {{ $t("Nothing found") }}
          </template>
        </div>
      </div>
    </div>
  </div>
    
  </div>
</template>

<script>
const availableDirections = {
  Up: "up",
  Down: "down"
}

import Icon from '@/components/Icon.vue'

export default {
  name: 'Popover',
  components: {
    Icon
  },
  model: {
    prop: "data",
    event: "change"
  },
  props: {
    title: {
      type: String,
      default: ""
    },
    data: {
      type: Array,
      default: () => []
    },
    preSelected: {
      type: [String, Number, Array],
      default: () => []
    },
    disabled: {
      type: Array,
      default: () => []
    },
    maxVisibleItems: {
      type: Number,
      default: 5
    },
    isSelectable: {
      type: Boolean,
      default: true
    },
    isSearchEnabled: {
      type: Boolean,
      default: false
    },
    isMultiple: {
      type: Boolean,
      default: false
    },
    initialDataUrl: {
      type: String,
      default: ""
    },
    searchUrl: {
      type: String,
      default: ""
    },
    searchQueryHandler: {
      type: Function,
      default: e => ({
        query: e
      })
    }
  },
  data() {
    return {
      Direction: availableDirections,
      selected: this.isMultiple ? [...this.preSelected] : this.preSelected,
      focusedItem: null,
      searchValue: "",
      width: null,
      maxHeight: 0,
      dataFromSearch: [],
      isLoading: false,
      typingTimeout: null,
      scrollTimeout: null,
      scrollObserver: null,
      isScrolledTop: true,
      isNavigationStarted: false
    }
  },
  computed: {
    popoverStyles() {
      return {
        width: "number" == typeof this.width ? this.width + "px" : "auto",
        "max-height": "number" == typeof this.maxHeight ? this.maxHeight + "px" : "none"
      }
    },
    isSingleSection() {
      const e = this.dataFromSearch.length ? this.dataFromSearch : this.data;
      return !e.length || Object.prototype.hasOwnProperty.call(e[0], "label")
    },
    sections() {
      const e = this.dataFromSearch.length ? this.dataFromSearch : this.data;
      return e.length ? this.isSingleSection ? [{
        title: this.title && this.isTitleScrollable ? this.title : "",
        items: e
      }] : e : []
    },
    isTitleScrollable() {
      return !this.isSingleSection || !this.isSearchEnabled
    },
    items() {
      return this.sections.reduce((e, t) => [...e, ...t.items], [])
    },
    isOverflown() {
      return this.items.length > 10
    },
    lastVisibleItem() {
      return this.isOverflown ? this.items[this.maxVisibleItems - 1] : null
    }
  },
  watch: {
    preSelected() {
      this.selected = this.isMultiple ? [...this.preSelected] : this.preSelected
    },
    sections() {
      this.$nextTick(() => {
        this.updatePopoverSize()
      })
    }
  },
  created() {
    this.Direction = availableDirections
  },
  async mounted() {
    if (this.updatePopoverSize(), this.$refs.popoverScrollable.addEventListener("scroll", this.disableScrollablePointerEvents), this.$refs.popoverContent && (this.scrollObserver = new IntersectionObserver(e => {
      e.forEach(e => {
        this.isScrolledTop = e.isIntersecting
      })
    }, {
      root: this.$refs.popoverScrollable,
      threshold: [0, 1]
    }), this.scrollObserver.observe(this.$refs.popoverContentHorizon)), this.initialDataUrl) {
      this.isLoading = true;
      try {
        this.data = await Object(l.e)({
          type: "GET",
          url: this.initialDataUrl,
          ignore_error_notify: true
        }), this.$emit("change", this.data)
      } catch (e) {
        console.error("Error while fetching initial data for popover", e)
      }
      this.$nextTick(() => {
        this.updatePopoverSize()
      })
      this.isLoading = false
    }
    this.focusedItem = this.items.find(this.isItemSelected), setTimeout(() => {
      this.scrollToFocusedItem()
    }, 0)
  },
  beforeDestroy() {
    this.$refs.popoverScrollable.removeEventListener("scroll", this.disableScrollablePointerEvents)
    this.scrollObserver && this.$refs.popoverContentHorizon && (this.scrollObserver.unobserve(this.$refs.popoverContentHorizon), this.scrollObserver = null)
  },
  methods: {
    itemProps(item) {
      let props = {}
      if (item.to) props['to'] = item.to
      if (item.url) props['href'] = item.url
      if (item.urlTarget) props['target'] = item.urlTarget
      return props
    },
    getImageUrl(path, size) {
      return this.$leonardo.formImageUrl(path, size)
    },
    disableScrollablePointerEvents() {
      clearTimeout(this.scrollTimeout)
  
      this.$refs.popoverContent.style.pointerEvents = "none"

      this.scrollTimeout = setTimeout(() => {
        this.$refs.popoverContent.style.pointerEvents = ""
      }, 100)
    },
    isItemSelected(e) {
      const t = Object.keys(e).includes("value") ? e.value : e.label;
      return this.isMultiple && Array.isArray(this.selected) ? this.selected.includes(t) : this.selected === t
    },
    isItemFocused(e) {
      return e === this.focusedItem
    },
    isItemDisabled(e) {
      const t = Object.keys(e).includes("value") ? e.value : e.label;
      return this.disabled.includes(t)
    },
    selectItem(e, t = null) {
      if (this.isItemDisabled(e)) return;
      if (t && !e.to && !e.url && (t.preventDefault(), t.stopPropagation()), "function" == typeof e.action && e.action(e), !this.isSelectable)
        return this.$emit("select", this.selected, e);
      
      const n = Object.keys(e).includes("value") ? e.value : e.label;
      if (this.isMultiple) {
        Array.isArray(this.selected) || (this.selected = [this.selected]);
        const t = this.selected.indexOf(n);
        this.isItemSelected(e) && t > -1 ? this.selected.splice(t, 1) : this.selected.push(n)
      } else {
        this.selected = n
      }

      this.$emit("select", this.selected, e)
    },
    selectFocusedItem() {
      this.focusedItem && this.selectItem(this.focusedItem)
    },
    updatePopoverSize() {
      this.width = null, this.$nextTick(() => {
        const {
          lastVisibleNode: e,
          popover: t
        } = this.$refs;
        if (!t) return;
        const {
          top: n,
          width: i
        } = t.getBoundingClientRect();
        if (this.width = i, !e || !e.length) return void (this.maxHeight = null);
        const {
          top: o,
          height: r
        } = e[0].getBoundingClientRect(), s = o - n;
        this.maxHeight = s + .5 * r
      })
    },
    onSearchInput(e) {
      const t = e.trim().toLowerCase();
      if (t)
        if (this.searchUrl) {
          const e = 300;
          clearTimeout(this.typingTimeout), this.typingTimeout = setTimeout(() => {
            this.performServerSearch(t)
          }, e)
        } else this.performLocalSearch(t);
      else this.dataFromSearch = []
    },
    performLocalSearch(e) {
      const t = this.items.filter(t => {
        const n = !!Object.keys(t).includes("label") && t.label.toLowerCase().includes(e),
          i = !!Object.keys(t).includes("value") && t.value.toString().toLowerCase().includes(e);
        return n || i
      });
      this.dataFromSearch = this.sections.reduce((e, n) => {
        const i = n.items.filter(e => t.includes(e));
        return i.length && e.push({
          title: n.title,
          items: i
        }), e
      }, [])
    },
    performServerSearch(e) {
      this.isLoading || (this.isLoading = true, Object(l.e)({
        type: "GET",
        url: this.searchUrl,
        ignore_error_notify: true,
        data: this.searchQueryHandler(e)
      }).then(e => {
        this.dataFromSearch = e
      }).catch(e => {
        console.error("Error while fetching search results for popover", e), this.dataFromSearch = []
      }).finally(() => {
        this.isLoading = false
      }))
    },
    navigate(e) {
      let t = (this.focusedItem ? this.items.indexOf(this.focusedItem) : -1) + (e === availableDirections.Up ? -1 : 1);
      t > this.items.length - 1 ? t = 0 : t < 0 && (t = this.items.length - 1)

      this.isNavigationStarted = true
      this.focusedItem = this.items[t]
      
      this.$nextTick(() => {
        this.scrollToFocusedItem()
      })
    },
    scrollToFocusedItem() {
      if (!this.isOverflown) return;
      const e = this.$refs.focusedItem;
      e && e[0] && this.maxHeight && (this.$refs.popoverScrollable.scrollTop = e[0].offsetTop + e[0].offsetHeight - this.maxHeight / 2)
    }
  }
}

export { availableDirections as Direction }

</script>

<style lang="scss">
/** Popover and its containers */
.popover {
  background: var(--popover-background);
  color: var(--popover-color);
  box-shadow: var(--popover-box-shadow);
  font-size: var(--popover-item-font-size);
  line-height: 1.5em;
  border-radius: var(--popover-border-radius);
  overflow: hidden;
  user-select: none;
  position: absolute;
  display: flex;
  flex-direction: column;
  max-width: 400px;

  @media (max-width: 859px) {
    max-width: 320px;
  }

  &__header {
    padding: var(--popover-main-offset) var(--popover-main-offset) 0;
    box-shadow: 0 0 0 var(--popover-search-bottom-offset) #fff;
    position: relative;
  }

  &__main {
    flex: 1;
    display: flex;
    flex-direction: column;
  }
}

.popover--scrolled .popover__header {
  box-shadow: 0 0 0 var(--popover-search-bottom-offset) #fff, 0 2px 4px 0 rgba(0,0,0,0.4);
}
.popover__scrollable {
  -ms-flex: 1;
  flex: 1;
}
.popover__content {
  padding: var(--popover-main-offset);
  position: relative;
}
.popover__content-horizon {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: calc(var(--popover-search-bottom-offset) + var(--popover-main-offset));
  pointer-events: none;
}
.popover__section:first-of-type .popover-title {
  padding-top: 5px;
}
.popover .vb .vb-content {
  -webkit-overflow-scrolling: touch;
}
.popover .vb .vb-dragger {
  width: 2px;
  right: var(--popover-scrollbar-offset);
  display: none;
  padding: var(--popover-scrollbar-offset) 0;
  box-sizing: border-box;
}
@media (max-width: 859px) {
.popover .vb .vb-dragger {
    display: block;
}
}
.popover .vb .vb-dragger .vb-dragger-styler {
  backface-visibility: hidden;
  transform: rotate3d(0, 0, 0);
  background-color: rgba(0,0,0,0.1);
  border-radius: 3px;
  height: calc(100% - var(--popover-scrollbar-offset) * 2);
}
@media (min-width: 860px) {
.popover .vb:hover .vb-dragger {
    display: block;
}
}
/** Popover title */
.popover-title {
  display: flex;
  align-items: center;
  font-size: 14px;
  line-height: 20px;
  padding-top: 7px;
  padding-bottom: 7px;
  padding-left: var(--popover-item-offset-left);
  padding-right: var(--popover-item-offset-right);
  color: #595959;

  &--centered {
    justify-content: center;
    white-space: nowrap;
    padding-left: 8px;
    padding-right: 8px;
  }
}

/** Popover search container */
.popover-search {
  position: relative;
  height: 36px;
}
.popover-search .v-field {
  width: 100%;
  position: absolute;
  top: 0;
  left: 0;
}
.popover-search .v-field__icon {
  top: 7px;
}
.popover-search .v-field__icon--left {
  left: 10px;
}
.popover-search .v-text-input {
  height: 34px;
}
.popover-search .v-text-input__input {
  line-height: 1.3em;
}
.popover-search .v-text-input--icon-l {
  padding-left: 41px;
}

/** Popover item and its parts */
.popover-item {
  background: var(--popover-item-background);
  color: var(--popover-item-color);
  border-radius: var(--popover-item-border-radius);
  padding-top: 6px;
  padding-bottom: 6px;
  padding-left: var(--popover-item-offset-left);
  padding-right: var(--popover-item-offset-right);
  display: flex;
  align-items: center;
  cursor: pointer;

  &:not(:last-child) {
    margin-bottom: var(--popover-item-gap);
  }

  &:hover,
  &--focused {
    background: var(--popover-item-background--hover);
    color: var(--popover-item-color--hover)
  }

  &--selected {
    --popover-item-offset-right: 10px;
    background: var(--popover-item-background--selected);
    color: var(--popover-item-color--selected)
  }

  &--disabled {
    pointer-events: none;
    opacity: .6;
  }

  &__icon,
  &__image {
    flex-shrink: 0;
    width: var(--popover-item__icon-size);
    height: var(--popover-item__icon-size);
    margin-right: var(--popover-item__icon-offset);
  }
  &__icon {
    display: flex;
    align-items: center;
    justify-content: center;

    svg { fill: currentColor; flex-shrink: 0; }
  }
  &__image {
    background-color: var(--layout-item--background, #dedede);
    border-radius: var(--popover-item-border-radius);
    background-size: cover;
    background-repeat: no-repeat;
    box-shadow: inset 0 0 0 1px rgba(0,0,0,0.1);
    image-rendering: -webkit-optimize-contrast;
  }
}

.popover-item--selected:hover,
.popover-item--selected.popover-item--focused {
  background: var(--popover--item-background--selected-hover);
  color: var(--popover--item-color--selected-hover)
}
.popover-item--selected .popover-item__label {
  font-weight: 500;
}
.popover-item--selected .popover-item__slot {
  margin-right: 0;
}

.popover-item--with-details {
  -ms-flex-align: start;
  align-items: flex-start;
}
.popover-item--with-details .popover-item__main:not(:first-child) {
  margin-top: -2px;
}
.popover-item__main {
  min-width: 0;
}
.popover-item__main:not(:last-child) {
  margin-right: 10px;
}

.popover-item__details {
  font-size: 14px;
  line-height: 20px;
  color: #595959;
  margin-top: 1px;
  margin-bottom: -3px;
}
.popover-item__slot {
  margin-left: auto;
  margin-right: -8px;
}
.popover-item__slot .icon {
  display: block;
}
.popover-item .popover-title__label,
.popover-item .popover-item__label,
.popover-item .popover-item__details {
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

</style>