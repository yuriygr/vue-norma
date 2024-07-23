<template>
  <div class="alerts-layer" v-if="visible">
    <transition-group name="alerts-list" tag="div" class="alerts-layer__list">
      <div v-for="(alert, index) in alerts"
        class="alerts-item"
        :key="index+1"
        :class="{
          'alerts-item--success': alert.type == 'success',
          'alerts-item--info':    alert.type == 'info',
          'alerts-item--danger':  alert.type == 'error'
        }">
        <span style="white-space: pre-wrap">{{ alert.text }}</span>
      </div>
    </transition-group>
  </div>
</template>

<script>
export default {
  name: 'alerts-layer',
  data() {
    return {
      alerts: [],
    }
  },
  computed: {
    visible() { return this.alerts.length > 0 }
  },
  methods: {
    addAlert({ type, text, timeout }) {
      const alert = { type, text }
      this.alerts.unshift(alert)
      setTimeout(() => { this.closeAlert(alert) }, timeout)
    },
    closeAlert(alert) {
      const idx = this.alerts.indexOf(alert)
      if (idx > -1)
        this.alerts.splice(idx, 1)
    }
  },
  created() {
    this.$alerts.on(this.addAlert)
  },
  beforeUnmount() {
    this.$alerts.off()
  }
}
</script>

<style lang="scss">
.alerts-layer {
  --z-index: 800;

  --alerts-layer-offset-top: 1rem;
  --alerts-layer-offset-right: 1rem;

  --alerts-item-font-size: 13px;
  --alerts-item-border-radius: 12px;
  --alerts-item-gap: .75rem;
}

.alerts-layer {
  --alerts-item-tint-color: #f4f4f4;
  --alerts-item-tint-color--success: #afd4a9;
  --alerts-item-tint-color--info: #c1c9d9;
  --alerts-item-tint-color--danger: #f2ab99;

  html[data-theme="black"] & {
    --alerts-item-tint-color: #fffcea;
    --alerts-item-tint-color--success: #afd4a9;
    --alerts-item-tint-color--info: #c1c9d9;
    --alerts-item-tint-color--danger: #f2ab99;
  }
}


.alerts-layer {
  position: fixed;
  top: var(--alerts-layer-offset-top);
  right: var(--alerts-layer-offset-right);
  z-index: var(--z-index);

  &__list {
    display: flex;
    flex-direction: column;
    align-items: flex-end;
  }
}


.alerts-item {
  --tint-color: var(--alerts-item-tint-color);

  background: var(--x-background);
  color: var(--tint-color);
  border-radius: var(--alerts-item-border-radius);
  border: 1px solid var(--tint-color);
  font-size: var(--alerts-item-font-size);
  font-weight: 500;
  padding: .5rem .75rem;
  position: relative;
  display: inline-block;
  max-width: 80vw;
  transition: all 0.2s;
  z-index: calc(var(--z-index) + 1);

  &--success {
    --tint-color: var(--alerts-item-tint-color--success);
  }
  &--info {
    --tint-color: var(--alerts-item-tint-color--info);
  }
  &--danger {
    --tint-color: var(--alerts-item-tint-color--danger);
  }

  &:not(:last-child) {
    margin-bottom: var(--alerts-item-gap);
  }
}
.alerts-list-enter, .alerts-list-leave-to {
  opacity: 0;
  transform: translateX(50px);
}
</style>
