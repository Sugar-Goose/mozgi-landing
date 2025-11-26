<template>
  <div class="burger-menu-wrapper">
    <button
      class="burger-menu-button"
      @click="toggleMenu"
      :class="{ active: isOpen }"
      ref="buttonRef"
    >
      <span class="burger-line burger-line-1"></span>
      <span class="burger-line burger-line-2"></span>
    </button>
    <div
      class="burger-menu-overlay"
      :class="{ open: isOpen }"
      @click="closeMenu"
      ref="overlayRef"
    >
      <nav
        class="burger-menu-content"
        @click.stop
        ref="menuRef"
      >
        <router-link
          v-for="(link, index) in menuLinks"
          :key="link.to"
          :to="link.to"
          class="burger-menu-link"
          :ref="(el) => setLinkRef(el, index)"
          @click="closeMenu"
        >
          {{ link.text }}
        </router-link>
      </nav>
    </div>
  </div>
</template>

<script>
import { ref, computed, onBeforeUnmount } from 'vue'
import { useI18n } from 'vue-i18n'
import { gsap } from 'gsap'

const ANIMATION_CONFIG = {
  overlay: {
    show: { opacity: 1, duration: 0.3, ease: 'power2.out' },
    hide: { opacity: 0, duration: 0.3, ease: 'power2.in' }
  },
  menu: {
    show: { opacity: 1, y: 0, duration: 0.4, ease: 'power3.out' },
    hide: { opacity: 0, y: -20, duration: 0.3, ease: 'power2.in' },
    from: { opacity: 0, y: -20 }
  },
  link: {
    show: { opacity: 1, y: 0, duration: 0.5, ease: 'power3.out' },
    hide: { opacity: 0, y: 20, duration: 0.2, ease: 'power2.in' },
    from: { opacity: 0, y: 20 }
  }
}

const LINK_DELAY_STEP = 0.1

export default {
  name: 'BurgerMenuComponent',
  setup() {
    const { t } = useI18n()
    const isOpen = ref(false)
    const buttonRef = ref(null)
    const overlayRef = ref(null)
    const menuRef = ref(null)
    const linkRefs = ref([])

    const menuLinks = computed(() => [
      { text: t('navigation.where'), to: '/info?link=where' },
      { text: t('navigation.what'), to: '/info?link=what' },
      { text: t('navigation.who'), to: '/info?link=who' }
    ])

    const setLinkRef = (el, index) => {
      if (el) {
        linkRefs.value[index] = el
      }
    }

    const toggleMenu = () => {
      isOpen.value = !isOpen.value
      if (isOpen.value) {
        openMenu()
      } else {
        closeMenu()
      }
    }

    const openMenu = () => {
      document.body.style.overflow = 'hidden'
      
      if (overlayRef.value) {
        gsap.to(overlayRef.value, ANIMATION_CONFIG.overlay.show)
        gsap.set(overlayRef.value, { pointerEvents: 'auto' })
      }

      if (menuRef.value) {
        gsap.fromTo(
          menuRef.value,
          ANIMATION_CONFIG.menu.from,
          ANIMATION_CONFIG.menu.show
        )
      }

      linkRefs.value.forEach((link, index) => {
        if (link) {
          gsap.fromTo(
            link,
            ANIMATION_CONFIG.link.from,
            {
              ...ANIMATION_CONFIG.link.show,
              delay: index * LINK_DELAY_STEP
            }
          )
        }
      })
    }

    const closeMenu = () => {
      isOpen.value = false
      document.body.style.overflow = ''

      if (overlayRef.value) {
        gsap.to(overlayRef.value, {
          ...ANIMATION_CONFIG.overlay.hide,
          onComplete: () => {
            if (overlayRef.value) {
              gsap.set(overlayRef.value, { pointerEvents: 'none' })
            }
          }
        })
      }

      if (menuRef.value) {
        gsap.to(menuRef.value, ANIMATION_CONFIG.menu.hide)
      }

      linkRefs.value.forEach((link) => {
        if (link) {
          gsap.to(link, ANIMATION_CONFIG.link.hide)
        }
      })
    }

    onBeforeUnmount(() => {
      document.body.style.overflow = ''
    })

    return {
      isOpen,
      buttonRef,
      overlayRef,
      menuRef,
      linkRefs,
      menuLinks,
      setLinkRef,
      toggleMenu,
      closeMenu
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

.burger-menu-wrapper {
  display: none;
}

.burger-menu-button {
  position: fixed;
  top: var(--spacing-lg);
  right: var(--spacing-lg);
  width: 22px;
  height: 22px;
  background: transparent;
  border: none;
  cursor: pointer;
  z-index: var(--z-index-menu);
  display: flex;
  flex-direction: column;
  justify-content: center;
  gap: var(--spacing-sm);
  padding: 0;
  transition: opacity var(--transition-fast);
}

.burger-menu-button:hover {
  opacity: 0.7;
}

.burger-line {
  width: 100%;
  height: 2px;
  background-color: var(--color-text-primary);
  transition: all var(--transition-fast);
  transform-origin: center;
}

.burger-line-1 {
  transform: translateY(0);
}

.burger-line-2 {
  transform: translateY(0);
}

.burger-menu-button.active .burger-line-1 {
  transform: translateY(5px) rotate(45deg);
}

.burger-menu-button.active .burger-line-2 {
  transform: translateY(-5px) rotate(-45deg);
}

.burger-menu-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: var(--color-white-overlay-50);
  backdrop-filter: blur(var(--blur-menu));
  -webkit-backdrop-filter: blur(var(--blur-menu));
  z-index: var(--z-index-overlay);
  opacity: 0;
  pointer-events: none;
  display: flex;
  align-items: center;
  justify-content: center;
}

.burger-menu-overlay.open {
  pointer-events: auto;
}

.burger-menu-content {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: var(--spacing-xl);
  padding: var(--spacing-xl);
}

.burger-menu-link {
  font-family: var(--font-primary);
  font-size: var(--font-size-burger-menu);
  font-weight: var(--font-weight-semibold);
  line-height: var(--line-height-base);
  color: var(--color-text-primary);
  text-decoration: none;
  text-transform: lowercase;
  position: relative;
  padding-bottom: var(--spacing-sm);
  transition: color var(--transition-fast);
}

.burger-menu-link::after {
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

.burger-menu-link:hover {
  color: var(--color-white);
}

.burger-menu-link:hover::after {
  transform: scaleX(1);
}

@media (max-width: 768px) {
  .burger-menu-wrapper {
    display: block;
  }
}

@media (max-width: 375px) {
  .burger-menu-button {
    top: 15px;
    right: 15px;
    width: 38px;
    height: 38px;
    gap: 5px;
  }

  .burger-menu-content {
    gap: 32px;
  }

  .burger-menu-link {
    font-size: var(--font-size-burger-menu-mobile);
  }
}
</style>
