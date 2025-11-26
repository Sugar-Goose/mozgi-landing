<template>
  <router-link
    :to="to"
    class="navigation-link"
    :class="{ 'is-vertical': isVertical }"
    @mouseenter="handleMouseEnter"
    @mouseleave="handleMouseLeave"
  >
    <span
      class="navigation-link-text"
      :class="{ 'navigation-link-text-reversed': reverseDirection && isVertical }"
    >
      {{ text }}
    </span>
    <span class="navigation-link-underline" ref="underlineRef"></span>
  </router-link>
</template>

<script>
import { ref } from 'vue'
import { gsap } from 'gsap'

const ANIMATION_CONFIG = {
  enter: {
    duration: 0.4,
    ease: 'power2.out'
  },
  leave: {
    duration: 0.3,
    ease: 'power2.in'
  }
}

export default {
  name: 'NavigationLinkComponent',
  props: {
    text: {
      type: String,
      required: true
    },
    to: {
      type: String,
      required: true
    },
    isVertical: {
      type: Boolean,
      default: false
    },
    reverseDirection: {
      type: Boolean,
      default: false
    }
  },
  setup() {
    const underlineRef = ref(null)

    const animateUnderline = (scale) => {
      if (!underlineRef.value) return

      const config = scale === 1 ? ANIMATION_CONFIG.enter : ANIMATION_CONFIG.leave
      
      gsap.to(underlineRef.value, {
        scaleX: scale,
        transformOrigin: 'left center',
        ...config
      })
    }

    const handleMouseEnter = () => {
      animateUnderline(1)
    }

    const handleMouseLeave = () => {
      animateUnderline(0)
    }

    return {
      underlineRef,
      handleMouseEnter,
      handleMouseLeave
    }
  }
}
</script>

<style scoped>
@font-face {
  font-family: 'Grtsk Giga';
  src: url('@/assets/fonts/grtsk/GrtskGiga-Semibold.woff2') format('woff2'),
       url('@/assets/fonts/grtsk/GrtskGiga-Semibold.woff') format('woff'),
       url('@/assets/fonts/grtsk/GrtskGiga-Semibold.ttf') format('truetype');
  font-weight: 600;
  font-style: normal;
  font-display: swap;
}

.navigation-link {
  position: relative;
  display: inline-block;
  text-decoration: none;
  color: var(--color-text-primary);
  font-family: var(--font-primary);
  font-size: var(--font-size-base);
  line-height: var(--line-height-base);
  font-weight: var(--font-weight-semibold);
  text-transform: lowercase;
  transition: color var(--transition-fast);
  padding: 10px 0;
}

.navigation-link:hover {
  color: var(--color-white);
}

.navigation-link.is-vertical {
  writing-mode: vertical-rl;
  text-orientation: mixed;
  transform: rotate(0deg);
}

.navigation-link-text {
  position: relative;
  z-index: 2;
  display: inline-block;
}

.navigation-link-text-reversed {
  transform: rotate(180deg);
  transform-origin: center;
  display: inline-block;
}

.navigation-link-underline {
  position: absolute;
  bottom: 0;
  left: 0;
  width: 100%;
  height: 2px;
  background-color: var(--color-white);
  transform: scaleX(0);
  transform-origin: left center;
}

.navigation-link.is-vertical .navigation-link-underline {
  width: 100%;
  height: 2px;
  bottom: 0;
  left: 0;
  right: auto;
  top: auto;
  transform: scaleX(0);
  transform-origin: left center;
}

@media (max-width: 1024px) {
  .navigation-link {
    font-size: var(--font-size-base-tablet);
  }
}

@media (max-width: 768px) {
  .navigation-link {
    font-size: var(--font-size-base-tablet);
  }
}

@media (max-width: 375px) {
  .navigation-link {
    display: none;
  }
}
</style>
