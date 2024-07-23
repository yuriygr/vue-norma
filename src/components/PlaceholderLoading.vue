<template>
  <div class="placeholder-loading">
    <transition>
      <svg class="spinner" :class="{ show: loading }" v-if="loading" width="44px" height="44px" viewBox="0 0 44 44">
        <circle class="path" fill="none" stroke-width="4" stroke-linecap="round" cx="22" cy="22" r="20"></circle>
      </svg>
    </transition>
  </div>
</template>

<script>
export default {
  name: 'placeholder-loading',
  props: {
    loading: {
      type: Boolean,
      default: true
    }
  }
}
</script>

<style lang="scss">
.placeholder-loading {
  --loading-spiner--color: #aaa;

  html[data-theme='black'] & {
    --loading-spiner--color: #aaa;
  }
}

.placeholder-loading {
  align-items: center;
  box-sizing: border-box;
  display: flex;
  flex-direction: column;
  justify-content: center;
  text-align: center;
  padding: 10rem 1rem;
}

.spinner {
  color: var(--loading-spiner--color, #ddd);
  transition: opacity 0.15s ease;
  animation: rotator 1.4s linear infinite;
  animation-play-state: paused;
}

.spinner.show {
  animation-play-state: running;
}

.spinner.v-enter,
.spinner.v-leave-active {
  opacity: 0;
}

.spinner.v-enter-active,
.spinner.v-leave {
  opacity: 1;
}

.spinner .path {
  stroke: currentColor;
  stroke-dasharray: 126;
  stroke-dashoffset: 0;
  transform-origin: center;
  animation: dash 1.4s ease-in-out infinite;
}

@-moz-keyframes rotator {
  0% {
    transform: scale(0.5) rotate(0deg);
  }

  100% {
    transform: scale(0.5) rotate(270deg);
  }
}

@-webkit-keyframes rotator {
  0% {
    transform: scale(0.5) rotate(0deg);
  }

  100% {
    transform: scale(0.5) rotate(270deg);
  }
}

@-o-keyframes rotator {
  0% {
    transform: scale(0.5) rotate(0deg);
  }

  100% {
    transform: scale(0.5) rotate(270deg);
  }
}

@keyframes rotator {
  0% {
    transform: scale(0.5) rotate(0deg);
  }

  100% {
    transform: scale(0.5) rotate(270deg);
  }
}

@-moz-keyframes dash {
  0% {
    stroke-dashoffset: 126;
  }

  50% {
    stroke-dashoffset: 63;
    transform: rotate(135deg);
  }

  100% {
    stroke-dashoffset: 126;
    transform: rotate(450deg);
  }
}

@-webkit-keyframes dash {
  0% {
    stroke-dashoffset: 126;
  }

  50% {
    stroke-dashoffset: 63;
    transform: rotate(135deg);
  }

  100% {
    stroke-dashoffset: 126;
    transform: rotate(450deg);
  }
}

@-o-keyframes dash {
  0% {
    stroke-dashoffset: 126;
  }

  50% {
    stroke-dashoffset: 63;
    transform: rotate(135deg);
  }

  100% {
    stroke-dashoffset: 126;
    transform: rotate(450deg);
  }
}

@keyframes dash {
  0% {
    stroke-dashoffset: 126;
  }

  50% {
    stroke-dashoffset: 63;
    transform: rotate(135deg);
  }

  100% {
    stroke-dashoffset: 126;
    transform: rotate(450deg);
  }
}
</style>