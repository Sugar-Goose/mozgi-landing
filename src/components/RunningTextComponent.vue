<template>
  <div 
    class="running-text-ticker" 
    :style="{ 
      transform: `translate(-50%, -50%) rotate(${computedRotation}deg)`,
      top: computedTop,
      width: computedWidth
    }" 
    ref="tickerRef"
  >
    <span class="running-text-ticker__item" :class="`ticker-items-${rotation}`">
      {{ text }}
    </span>
    <span class="running-text-ticker__item" :class="`ticker-items-${rotation}`">
      {{ text }}
    </span>
  </div>
</template>

<script>
import { ref, onMounted, onUnmounted, computed, watch } from 'vue'
import { gsap } from 'gsap'

const ANIMATION_SPEED = 100
const INITIALIZATION_DELAY = 200
const RESTART_DELAY = 100

const BREAKPOINTS = {
  MOBILE: 375,
  TABLET: 768,
  DESKTOP: 1024
}

const ROTATION_ANGLES = {
  MOBILE: { '-30': -53.2, '150': 126.8 },
  TABLET: { '-30': -36.61, '150': 141.29 }
}

const TOP_OFFSETS = {
  MOBILE: 32,
  TABLET: 768,
  DESKTOP: 1024,
  DEFAULT: 96
}

const WIDTHS = {
  MOBILE: '190%',
  TABLET: '160%',
  DESKTOP: '130%',
  DEFAULT: '120%'
}

export default {
  name: 'RunningTextComponent',
  props: {
    text: {
      type: String,
      default: 'FULL-CYCLE EVENT AGENCY / FULL-CYCLE EVENT AGENCY'
    },
    rotation: {
      type: Number,
      default: -30
    }
  },
  setup(props) {
    const windowWidth = ref(window.innerWidth)
    const tickerRef = ref(null)
    let animation = null
    let resizeHandler = null

    const updateWindowWidth = () => {
      windowWidth.value = window.innerWidth
    }

    const computedRotation = computed(() => {
      if (windowWidth.value <= BREAKPOINTS.TABLET) {
        return ROTATION_ANGLES.MOBILE[props.rotation] ?? props.rotation
      }
      if (windowWidth.value <= BREAKPOINTS.DESKTOP) {
        return ROTATION_ANGLES.TABLET[props.rotation] ?? props.rotation
      }
      return props.rotation
    })

    const computedTop = computed(() => {
      let offset = TOP_OFFSETS.DEFAULT
      
      if (windowWidth.value <= BREAKPOINTS.MOBILE) {
        offset = TOP_OFFSETS.MOBILE
      } else if (windowWidth.value <= BREAKPOINTS.TABLET) {
        offset = 64
      } else if (windowWidth.value <= BREAKPOINTS.DESKTOP) {
        offset = 78
      }
      
      return props.rotation === 150 ? `calc(50% - ${offset}px)` : '50%'
    })

    const computedWidth = computed(() => {
      if (windowWidth.value <= BREAKPOINTS.MOBILE) {
        return WIDTHS.MOBILE
      }
      if (windowWidth.value <= BREAKPOINTS.TABLET) {
        return WIDTHS.TABLET
      }
      if (windowWidth.value <= BREAKPOINTS.DESKTOP) {
        return WIDTHS.DESKTOP
      }
      return WIDTHS.DEFAULT
    })

    const initAnimation = () => {
      if (!tickerRef.value) return

      const tickerItems = tickerRef.value.querySelectorAll(`.ticker-items-${props.rotation}`)
      if (tickerItems.length === 0) return

      const tickerWidth = tickerItems[0].offsetWidth
      const initDuration = tickerWidth / ANIMATION_SPEED

      animation = gsap.to(`.ticker-items-${props.rotation}`, {
        duration: initDuration,
        x: -tickerWidth,
        ease: 'none',
        repeat: -1
      })
    }

    const restartAnimation = () => {
      if (animation) {
        animation.kill()
        animation = null
      }
      setTimeout(() => {
        initAnimation()
      }, RESTART_DELAY)
    }

    resizeHandler = () => {
      restartAnimation()
    }

    watch(() => props.text, () => {
      restartAnimation()
    })

    onMounted(() => {
      window.addEventListener('resize', updateWindowWidth)
      window.addEventListener('resize', resizeHandler)

      setTimeout(() => {
        initAnimation()
      }, INITIALIZATION_DELAY)
    })

    onUnmounted(() => {
      window.removeEventListener('resize', updateWindowWidth)
      if (animation) {
        animation.kill()
      }
      if (resizeHandler) {
        window.removeEventListener('resize', resizeHandler)
      }
    })

    return {
      tickerRef,
      computedRotation,
      computedTop,
      computedWidth
    }
  }
}
</script>

<style scoped>
@font-face {
  font-family: 'Grtsk Giga';
  src: url('@/assets/fonts/grtsk/GrtskGiga-BoldItalic.woff2') format('woff2'),
       url('@/assets/fonts/grtsk/GrtskGiga-BoldItalic.woff') format('woff'),
       url('@/assets/fonts/grtsk/GrtskGiga-BoldItalic.ttf') format('truetype');
  font-weight: bold;
  font-style: italic;
  font-display: swap;
}

.running-text-ticker {
  position: absolute;
  top: 50%;
  left: 50%;
  height: auto;
  overflow: hidden;
  white-space: nowrap;
  transform-origin: center center;
  z-index: 1;
  pointer-events: none;
}

.running-text-ticker__item {
  display: inline-block;
  padding: 0 1em 0 0;
  font-family: var(--font-primary);
  font-weight: var(--font-weight-bold);
  font-style: italic;
  font-size: var(--font-size-running-text-desktop);
  line-height: var(--line-height-title);
  color: transparent;
  -webkit-text-stroke: 1px var(--color-text-stroke);
  -webkit-text-stroke-width: 1px;
  -webkit-text-stroke-color: var(--color-text-stroke);
  opacity: 10%;
  paint-order: stroke fill;
  text-transform: uppercase;
  white-space: nowrap;
}

@media (max-width: 1024px) {
  .running-text-ticker__item {
    font-size: var(--font-size-running-text-tablet);
  }
}

@media (max-width: 768px) {
  .running-text-ticker__item {
    font-size: var(--font-size-running-text-tablet);
  }
}

@media (max-width: 375px) {
  .running-text-ticker__item {
    font-size: var(--font-size-running-text-mobile);
  }
}
</style>
