<template>
  <div
    class="main-title-wrapper"
    ref="titleWrapper"
  >
    <h1 class="main-title" ref="titleRef">
      <span class="title-line" ref="titleLine1">{{ $t('mainTitle.line1') }}</span>
      <span class="title-line" ref="titleLine2">{{ $t('mainTitle.line2') }}</span>
    </h1>
  </div>
</template>

<script>
import { ref, onMounted, onUnmounted } from 'vue'
import { gsap } from 'gsap'

const PARALLAX_SENSITIVITY = 0.02
const MAX_OFFSET = 60
const SOFT_ZONE = 20
const ANIMATION_DURATION = 0.6
const RESET_DURATION = 1.2
const INITIAL_ANIMATION_DELAY = 0.5
const INITIAL_ANIMATION_DURATION = 1

export default {
  name: 'MainTitleComponent',
  setup() {
    const titleWrapper = ref(null)
    const titleRef = ref(null)
    const titleLine1 = ref(null)
    const titleLine2 = ref(null)

    let quickToLine1X = null
    let quickToLine1Y = null
    let quickToLine2X = null
    let quickToLine2Y = null

    const limitMovement = (value, max, softZone) => {
      const absValue = Math.abs(value)
      const sign = Math.sign(value)
      
      if (absValue <= max - softZone) {
        return value
      }
      
      if (absValue <= max) {
        const progress = (absValue - (max - softZone)) / softZone
        const easeOut = 1 - Math.pow(1 - progress, 3)
        const limitedValue = (max - softZone) + (softZone * (1 - easeOut))
        return sign * limitedValue
      }
      
      const excess = absValue - max
      const damping = Math.max(0.1, 1 / (1 + excess * 0.05))
      return sign * (max + excess * damping)
    }

    const handleMouseMove = (e) => {
      if (!titleWrapper.value || !titleLine1.value || !titleLine2.value) return

      const centerX = window.innerWidth / 2
      const centerY = window.innerHeight / 2
      const x = e.clientX - centerX
      const y = e.clientY - centerY

      let moveX = limitMovement(x * PARALLAX_SENSITIVITY, MAX_OFFSET, SOFT_ZONE)
      let moveY = limitMovement(y * PARALLAX_SENSITIVITY, MAX_OFFSET, SOFT_ZONE)

      if (!quickToLine1X) {
        const quickToConfig = { duration: ANIMATION_DURATION, ease: 'none' }
        quickToLine1X = gsap.quickTo(titleLine1.value, 'x', quickToConfig)
        quickToLine1Y = gsap.quickTo(titleLine1.value, 'y', quickToConfig)
        quickToLine2X = gsap.quickTo(titleLine2.value, 'x', quickToConfig)
        quickToLine2Y = gsap.quickTo(titleLine2.value, 'y', quickToConfig)
      }
      
      quickToLine1X(moveX)
      quickToLine1Y(moveY)
      quickToLine2X(moveX)
      quickToLine2Y(moveY)
    }

    const handleMouseLeave = () => {
      if (titleLine1.value && titleLine2.value) {
        gsap.to([titleLine1.value, titleLine2.value], {
          x: 0,
          y: 0,
          duration: RESET_DURATION,
          ease: 'expo.out'
        })
      }
    }

    onMounted(() => {
      if (titleRef.value) {
        gsap.from(titleRef.value, {
          opacity: 0,
          y: 20,
          duration: INITIAL_ANIMATION_DURATION,
          delay: INITIAL_ANIMATION_DELAY,
          ease: 'power2.out'
        })
      }

      window.addEventListener('mousemove', handleMouseMove)
      window.addEventListener('mouseleave', handleMouseLeave)
    })

    onUnmounted(() => {
      window.removeEventListener('mousemove', handleMouseMove)
      window.removeEventListener('mouseleave', handleMouseLeave)
    })

    return {
      titleWrapper,
      titleRef,
      titleLine1,
      titleLine2
    }
  }
}
</script>

<style scoped>
@font-face {
  font-family: 'Grtsk Giga';
  src: url('@/assets/fonts/grtsk/GrtskGiga-Bold.woff2') format('woff2'),
       url('@/assets/fonts/grtsk/GrtskGiga-Bold.woff') format('woff'),
       url('@/assets/fonts/grtsk/GrtskGiga-Bold.ttf') format('truetype');
  font-weight: bold;
  font-style: normal;
  font-display: swap;
}

.main-title-wrapper {
  position: relative;
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: var(--z-index-content);
}

.main-title {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 10px;
  margin: 0;
  padding: 0;
}

.title-line {
  display: block;
  font-family: var(--font-primary);
  font-size: clamp(var(--font-size-title-min), var(--font-size-title-vw), var(--font-size-title-max));
  font-weight: var(--font-weight-bold);
  color: var(--color-text-primary);
  text-transform: uppercase;
  letter-spacing: 0;
  line-height: var(--line-height-title);
  will-change: transform;
  transform: translateZ(0);
  backface-visibility: hidden;
  text-align: center;
}
</style>
