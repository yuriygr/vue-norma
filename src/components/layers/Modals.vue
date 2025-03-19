<template>
  <div class="modal-layer" v-if="visible" @click.self="clickOnLayer">
    <component :is="component" v-bind="props" class="modal-layer__content" ref="modal"></component>
  </div>
</template>

<script>
export default {
  name: 'modals-layer',
  data() {
    return {
      component: false,
      props: [],
      onClose: false
    }
  },
  computed: {
    visible() { return this.component !== false }
  },
  methods: {
    // Handlers
    _show({ component, props, onClose }) {
      this.component = component
      this.props = props
      this.onClose = onClose
    },
    async _close() {
      if (!this.component) return

      await this.onClose()

      this.component = false
      this.props = []
      this.onClose = () => {}
    },
    // Other methods
    async closeModalOnEsc(event) {
      if (event.keyCode === 27) {
        if (!this.component) return
        await this.$refs.modal.closeModal()
      }
    },
    async clickOnLayer(event) {
      if (event.target.closest('.modal-layer__content')) return
      if (!this.component) return
      await this.$refs.modal.closeModal()
    }
  },
  created() {
    this.$modals.on('show', this._show)
    this.$modals.on('close', this._close)

    document.addEventListener('keydown', this.closeModalOnEsc)
  },
  beforeDestroy() {
    this.$modals.off('show', this._show)
    this.$modals.off('close', this._close)

    document.removeEventListener('keydown', this.closeModalOnEsc)
  }
}
</script>

<style lang="scss">
.modal-layer {
  --z-index: 663;

  --modal--padding: 12px;

  --modal__checklist-item-gap: .75rem;
}

.modal-layer {
  --modal-layer--background: rgba(255, 255, 255, 0.4);
  --modal-layer__footer--color: #333;

  --modal__checklist-item-background: #f9f9f9;
  --modal__checklist-item-checked-color: #6f619d;

  html[data-theme="black"] & {
    --modal-layer--background: rgba(0, 0, 0, 0.85);
    --modal-layer__footer--color: #888;


    --modal__checklist-item-background: #0e0e0e;
    --modal__checklist-item-checked-color: #9f8fd8;
  }
}


.modal-layer {
  background: var(--modal-layer--background);
  backdrop-filter: blur(14px);
  overflow: auto;
  position: fixed;
  left: 0;
  right: 0;
  top: 0;
  bottom: 0;
  z-index: calc(var(--z-index));

	&::-webkit-scrollbar {
		width: 16px
	}
	
	&::-webkit-scrollbar-thumb {
		height: 56px;
		border-radius: 8px;
		border: 4px solid transparent;
		background-clip: content-box;
		background-color: hsl(0,0%,67%);
	}

  &__container {
    margin: 0 auto;
    position: relative;
  }

  &__footer {
    text-align: center;
    padding: 1rem;
    font-size: 1.3rem;
    color: var(--modal-layer__footer--color);
    user-select: none;
  }
}
</style>
