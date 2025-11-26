<template>
  <div
    class="showreel-button-wrapper"
    ref="buttonWrapper"
    @mouseenter="handleMouseEnter"
    @mouseleave="handleMouseLeave"
  >
    <div class="showreel-button-circle" ref="circleRef">
      <svg
        class="showreel-svg"
        viewBox="0 0 200 200"
        xmlns="http://www.w3.org/2000/svg"
      >
        <defs>
          <path
            id="showreel-path"
            d="M 100, 100 m -60, 0 a 60,60 0 1,1 120,0 a 60,60 0 1,1 -120,0"
          />
        </defs>
        <text class="showreel-text">
          <textPath href="#showreel-path" startOffset="0%">
            {{ showreelText }}
          </textPath>
        </text>
        <circle cx="100" cy="100" r="5" fill="black" class="showreel-dot" />
      </svg>
    </div>
  </div>
</template>

<script>
import { ref, computed } from 'vue'
import { gsap } from 'gsap'
import { useI18n } from 'vue-i18n'

const ROTATION_DURATION = 5
const RESET_DURATION = 0.5
const ROTATION_ANGLE = -360

export default {
  name: 'ShowreelButtonComponent',
  setup() {
    const { t } = useI18n()
    const buttonWrapper = ref(null)
    const circleRef = ref(null)
    let rotationTween = null

    const showreelText = computed(() => t('showreel'))

    const handleMouseEnter = () => {
      if (circleRef.value) {
        rotationTween = gsap.to(circleRef.value, {
          rotation: ROTATION_ANGLE,
          duration: ROTATION_DURATION,
          repeat: -1,
          ease: 'none',
          transformOrigin: 'center center'
        })
      }
    }

    const handleMouseLeave = () => {
      if (rotationTween) {
        rotationTween.kill()
        rotationTween = null
      }
      if (circleRef.value) {
        gsap.to(circleRef.value, {
          rotation: 0,
          duration: RESET_DURATION,
          ease: 'power2.out'
        })
      }
    }

    return {
      buttonWrapper,
      circleRef,
      showreelText,
      handleMouseEnter,
      handleMouseLeave
    }
  }
}
</script>

<style scoped>
.showreel-button-wrapper {
  position: absolute;
  bottom: 20%;
  left: 65%;
  z-index: var(--z-index-content);
  cursor: pointer;
}

.showreel-button-circle {
  width: 160px;
  height: 160px;
  transform-origin: center;
}

.showreel-svg {
  width: 100%;
  height: 100%;
}

.showreel-text {
  font-family: var(--font-fallback);
  font-size: var(--font-size-showreel-desktop);
  fill: var(--color-white);
  text-transform: uppercase;
  letter-spacing: 2px;
}

.showreel-dot {
  pointer-events: none;
}

@media (max-width: 1440px) {
  .showreel-button-wrapper {
    bottom: 15%;
    left: 60%;
  }
}

@media (max-width: 1024px) {
  .showreel-button-wrapper {
    bottom: 15%;
    left: 60%;
  }

  .showreel-button-circle {
    width: 140px;
    height: 140px;
  }

  .showreel-text {
    font-size: var(--font-size-showreel-tablet);
  }
}

@media (max-width: 768px) {
  .showreel-button-wrapper {
    bottom: 15%;
    left: 60%;
  }

  .showreel-button-circle {
    width: 120px;
    height: 120px;
  }

  .showreel-text {
    font-size: var(--font-size-showreel-mobile);
  }
}

@media (max-width: 375px) {
  .showreel-button-wrapper {
    bottom: 20%;
    left: 60%;
  }

  .showreel-button-circle {
    width: 100px;
    height: 100px;
  }

  .showreel-text {
    font-size: var(--font-size-showreel-small);
  }
}
</style>
