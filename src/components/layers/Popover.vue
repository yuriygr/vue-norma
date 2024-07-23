<template>
  <div class="popover-layer" v-if="visible" v-click-outside="close">
    <popover v-if="items.length > 0" ref="popover"
      :class="['popover--' + currentAlign, {
          'popover--fixed': 'fixed' === position
      }]"
      :style="popoverStyle"
      :data="items"
      :pre-selected="selected"
      :is-selectable="false"
      
      @select="$emit('select')"
      @updatePopoverPosition="updatePopoverPosition"
      @mouseleave="$emit('mouseleave')"
    />
  </div>
</template>

<script>
import Popover from '@/components/Popover.vue'

export default {
  name: 'popover-layer',
  components: { Popover },
  data() {
    return {
      items: [],
      selected: {
        type: [ String, Number, Array ],
        default: () => []
      },
      currentTarget: null,
      popoverPosition: {
        top: 0,
        left: 0
      },
      isPositionReady: false,
      position: "absolute",
      currentAlign: "center",
      maxWidth: ""
    }
  },
  computed: {
    visible() { return this.items.length > 0 },
    popoverStyle() {
      const { top, left } = this.popoverPosition
      return {
        "--top": top + "px",
        "--left": left + "px",
        "max-width": this.maxWidth ? this.maxWidth + "px" : "",
        "visibility": this.isPositionReady ? "visible" : "hidden"
      }
    }
  },
  created() {
    this.$popover.on('open', this.open)
    this.$popover.on('close', this.close)

    window.addEventListener("orientationchange", this.close)
    window.addEventListener("resize", this.close)
  },
  beforeDestroy() {
    this.$popover.off('open')
    this.$popover.off('close')

    window.removeEventListener("orientationchange", this.close)
    window.removeEventListener("resize", this.close)
  },
  methods: {
    open({ items, target, selected = [], position = 'absolute', align = 'center', maxWidth = '' }) {
      this.items = items
      this.currentTarget = target
      this.selected = selected
      this.position = position
      this.currentAlign = align
      this.maxWidth = maxWidth
      this.$emit("open")
      this.updatePopoverPosition()
    },
    close(e) {
      e && e.composedPath().includes(this.currentTarget) || this.items.length && (this.items = [], this.$emit("close"))
    },
    updatePopoverPosition() {
        this.isPositionReady = false

        // Наши любимые костыли
        const
          horizontalPadding = 0,
          verticalPadding = 5

        let
          e = this.currentTarget.getBoundingClientRect(),
          i = "absolute" === this.position ? window.pageYOffset : 0

        this.popoverPosition.top = i + e.top + e.height + verticalPadding
        
        setTimeout(() => {
            const {
                popover: i
            } = this.$refs;
            if (!i) return;

            const {
                clientWidth: o,
                clientHeight: r
            } = document.documentElement, s = i.$el.getBoundingClientRect()


            "left" === this.currentAlign
              ? this.popoverPosition.left = e.left - horizontalPadding
              : "right" === this.currentAlign
                ? this.popoverPosition.left = e.right - s.width + horizontalPadding
                : this.popoverPosition.left = e.left + .5 * e.width - .5 * s.width

            setTimeout(() => {
              const t = i.$el.getBoundingClientRect(),
                  s = t.right - o,
                  a = 0 - t.left,
                  c = t.bottom - r,
                  u = 0 - t.top;

              if (s > 0 && (this.popoverPosition.left -= s), a > 0 && (this.popoverPosition.left = 0), c > 0) {
                const i = this.popoverPosition.top - (t.height + e.height + 2 * verticalPadding);
                i > 0 && (this.popoverPosition.top = i)
              }
              u > 0 && (this.popoverPosition.top = 0)
              this.isPositionReady = true
          }, 0)
        }, 0)
    }
  }
}
</script>

<style lang="scss">
.popover-layer {
  --z-index: 800;
}


.popover-layer {
  --popover-border-radius: 6px;

  --popover-main-offset: 6px;
  --popover-search-bottom-offset: 4px;
  --popover-scrollbar-offset: 8px;
  --popover-item-font-size: 13px;
  --popover-item-border-radius: 6px;
  --popover-item-offset-left: 10px;
  --popover-item-offset-right: 18px;
  --popover-item-gap: 4px;
  --popover-item__icon-size: 22px;
  --popover-item__icon-offset: 10px;
}

.popover-layer {
  --popover-background: #fff;
  --popover-color: var(--x-color);
  --popover-box-shadow: 0 4px 8px rgba(0, 0, 0, 0.06), 0 0 1px rgba(0, 0, 0, 0.25);
  
  --popover-item-background: transparent;
  --popover-item-color: inherit;

  --popover-item-background--hover: #f0f2f5;
  --popover-item-color--hover: inherit;
  --popover-item-background--selected: #e7e1fe;
  --popover-item-color--selected: inherit;
  --popover-item-background--selected-hover: #d8d1f4;
  --popover-item-color--selected-hover: inherit;

  html[data-theme="black"] & {
    --popover-background: #181818;
    --popover-color: var(--x-color);
    --popover-box-shadow: 0 4px 8px rgb(0 0 0 / 25%), 0 0 1px rgba(255, 255, 255, 0.25);

    --popover-item-background: transparent;
    --popover-item-color: inherit;

    --popover-item-background--hover: #222;
    --popover-item-color--hover: inherit;
    --popover-item-background--selected: #332d46;
    --popover-item-color--selected: inherit;
    --popover-item-background--selected-hover: #3d3653;
    --popover-item-color--selected-hover: inherit;
  }
}


.popover-layer {
  width: 100%;
  position: absolute;
  top: 0;
  left: 0;

  .popover {
    position: absolute;
    top: var(--top, 0);
    left: var(--left, 0);
    z-index: var(--z-index);
  }
}
</style>