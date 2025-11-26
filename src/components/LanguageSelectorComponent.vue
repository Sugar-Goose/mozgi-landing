<template>
  <div
    class="language-selector-wrapper"
    ref="wrapperRef"
    @mouseleave="handleMouseLeave"
  >
    <div
      class="language-selector-icon"
      ref="iconRef"
      @mouseenter="handleMouseEnter"
    >
      <img
        src="@/assets/language_icon.png"
        alt="Language"
        class="language-icon-image"
      />
    </div>
    <div
      class="language-selector-options"
      ref="optionsRef"
      @mouseenter="handleOptionsEnter"
    >
      <button
        v-for="(lang, index) in languages"
        :key="lang.code"
        class="language-option"
        :class="{ active: locale === lang.code }"
        :ref="(el) => setOptionRef(el, index)"
        @click="selectLanguage(lang.code)"
      >
        {{ lang.label }}
      </button>
    </div>
  </div>
</template>

<script>
import { ref, onMounted, onBeforeUnmount } from 'vue'
import { gsap } from 'gsap'
import { useI18n } from 'vue-i18n'

const HIDE_DELAY = 100
const ANIMATION_DURATION = 0.5
const ANIMATION_DELAY_STEP = 0.08
const HIDE_ANIMATION_DURATION = 0.3

const ANIMATION_CONFIG = {
  show: {
    opacity: 1,
    x: 0,
    duration: ANIMATION_DURATION,
    ease: 'power3.out'
  },
  hide: {
    opacity: 0,
    x: -10,
    duration: HIDE_ANIMATION_DURATION,
    ease: 'power2.in'
  },
  optionShow: {
    opacity: 1,
    y: 0,
    scale: 1,
    duration: ANIMATION_DURATION,
    ease: 'power3.out'
  },
  optionHide: {
    opacity: 0,
    y: -10,
    scale: 0.95,
    duration: HIDE_ANIMATION_DURATION,
    ease: 'power2.in'
  }
}

const INITIAL_STATE = {
  options: {
    opacity: 0,
    x: -10
  },
  option: {
    opacity: 0,
    y: -10,
    scale: 0.95
  }
}

export default {
  name: 'LanguageSelectorComponent',
  setup() {
    const { locale } = useI18n()
    const wrapperRef = ref(null)
    const iconRef = ref(null)
    const optionsRef = ref(null)
    const optionRefs = ref([])
    let hideTimeout = null

    const languages = [
      { code: 'en', label: 'ENG' },
      { code: 'ua', label: 'UA' }
    ]

    const setOptionRef = (el, index) => {
      if (el) {
        optionRefs.value[index] = el
      }
    }

    const clearHideTimeout = () => {
      if (hideTimeout) {
        clearTimeout(hideTimeout)
        hideTimeout = null
      }
    }

    const showOptions = () => {
      if (!optionsRef.value) return

      gsap.killTweensOf(optionsRef.value)
      optionsRef.value.style.pointerEvents = 'auto'
      gsap.to(optionsRef.value, ANIMATION_CONFIG.show)

      optionRefs.value.forEach((option) => {
        gsap.killTweensOf(option)
      })

      optionRefs.value.forEach((option, index) => {
        gsap.fromTo(
          option,
          INITIAL_STATE.option,
          {
            ...ANIMATION_CONFIG.optionShow,
            delay: index * ANIMATION_DELAY_STEP
          }
        )
      })
    }

    const hideOptions = () => {
      if (!optionsRef.value) return

      gsap.to(optionsRef.value, {
        ...ANIMATION_CONFIG.hide,
        onComplete: () => {
          if (optionsRef.value) {
            optionsRef.value.style.pointerEvents = 'none'
          }
        }
      })

      optionRefs.value.forEach((option) => {
        gsap.to(option, ANIMATION_CONFIG.optionHide)
      })
    }

    const handleMouseEnter = () => {
      clearHideTimeout()
      showOptions()
    }

    const handleOptionsEnter = () => {
      clearHideTimeout()
    }

    const handleMouseLeave = () => {
      hideTimeout = setTimeout(() => {
        hideOptions()
        hideTimeout = null
      }, HIDE_DELAY)
    }

    const selectLanguage = (code) => {
      locale.value = code
      localStorage.setItem('locale', code)
    }

    onMounted(() => {
      if (optionsRef.value) {
        optionsRef.value.style.pointerEvents = 'none'
        gsap.set(optionsRef.value, INITIAL_STATE.options)
      }

      optionRefs.value.forEach((option) => {
        gsap.set(option, INITIAL_STATE.option)
      })
    })

    onBeforeUnmount(() => {
      clearHideTimeout()
    })

    return {
      locale,
      wrapperRef,
      iconRef,
      optionsRef,
      languages,
      setOptionRef,
      handleMouseEnter,
      handleOptionsEnter,
      handleMouseLeave,
      selectLanguage
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

.language-selector-wrapper {
  position: absolute;
  top: var(--spacing-lg);
  left: var(--spacing-lg);
  z-index: var(--z-index-menu);
  display: flex;
  align-items: center;
  gap: var(--spacing-md);
}

.language-selector-icon {
  width: 38px;
  height: 38px;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: var(--border-radius-circle);
  background-color: transparent;
  border: 1px solid var(--color-white-overlay-12);
  transition: background-color var(--transition-fast);
}

.language-selector-icon:hover {
  background-color: var(--color-white-overlay-12);
}

.language-icon-image {
  width: 15px;
  height: 21px;
  object-fit: contain;
}

.language-selector-options {
  display: flex;
  gap: var(--spacing-lg);
  align-items: center;
  pointer-events: none;
}

.language-option {
  position: relative;
  background: transparent;
  border: none;
  padding: 0;
  font-family: var(--font-primary);
  font-size: var(--font-size-base);
  font-weight: var(--font-weight-semibold);
  line-height: var(--line-height-base);
  color: var(--color-text-primary);
  cursor: pointer;
  text-transform: lowercase;
  transition: color var(--transition-fast);
  padding-bottom: var(--spacing-xs);
}

.language-option::after {
  content: '';
  position: absolute;
  bottom: 0;
  left: 0;
  width: 100%;
  height: 2px;
  background-color: var(--color-white);
  transform: scaleX(0);
  transform-origin: left center;
  transition: transform var(--transition-fast);
}

.language-option:hover {
  color: var(--color-white);
}

.language-option:hover::after {
  transform: scaleX(1);
}

.language-option.active {
  color: var(--color-white);
}

.language-option.active::after {
  transform: scaleX(1);
}

@media (max-width: 1024px) {
  .language-option {
    font-size: var(--font-size-base-tablet);
  }
}

@media (max-width: 768px) {
  .language-selector-wrapper {
    top: 15px;
    left: 15px;
    gap: 12px;
  }

  .language-selector-icon {
    width: 38px;
    height: 38px;
  }

  .language-icon-image {
    width: 15px;
    height: 21px;
  }

  .language-selector-options {
    gap: var(--spacing-md);
  }

  .language-option {
    font-size: var(--font-size-base-tablet);
  }
}

@media (max-width: 375px) {
  .language-selector-wrapper {
    top: 10px;
    left: 10px;
    gap: 10px;
  }

  .language-selector-icon {
    width: 38px;
    height: 38px;
  }

  .language-icon-image {
    width: 15px;
    height: 21px;
  }

  .language-selector-options {
    gap: 12px;
  }

  .language-option {
    font-size: var(--font-size-base-mobile);
  }
}
</style>
