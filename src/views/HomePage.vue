<template>
  <div class="home-page" ref="homePageRef">
    <div class="home-page-background-gradient" ref="backgroundRef"></div>
    <RunningTextComponent 
      :rotation="-30" 
      :text="runningText" 
      ref="runningText1Ref"
    />
    <RunningTextComponent 
      :rotation="150" 
      :text="runningText" 
      ref="runningText2Ref"
    />
    <MainLogoComponent ref="logoRef" />
    <LanguageSelectorComponent ref="languageRef" />
    <BurgerMenuComponent ref="burgerRef" />
    <MainTitleComponent ref="titleRef" />
    <ShowreelButtonComponent ref="showreelRef" />
    <NavigationLinkComponent
      :text="t('navigation.where')"
      to="/info?link=where"
      :is-vertical="true"
      :reverse-direction="true"
      class="nav-link-left"
      ref="navLeftRef"
    />
    <NavigationLinkComponent
      :text="t('navigation.what')"
      to="/info?link=what"
      :is-vertical="true"
      class="nav-link-right"
      ref="navRightRef"
    />
    <NavigationLinkComponent
      :text="t('navigation.who')"
      to="/info?link=who"
      :is-vertical="false"
      class="nav-link-bottom"
      ref="navBottomRef"
    />
  </div>
</template>

<script>
import { computed, ref, onMounted, nextTick } from 'vue'
import { useI18n } from 'vue-i18n'
import { gsap } from 'gsap'
import MainLogoComponent from '@/components/MainLogoComponent.vue'
import MainTitleComponent from '@/components/MainTitleComponent.vue'
import NavigationLinkComponent from '@/components/NavigationLinkComponent.vue'
import ShowreelButtonComponent from '@/components/ShowreelButtonComponent.vue'
import LanguageSelectorComponent from '@/components/LanguageSelectorComponent.vue'
import RunningTextComponent from '@/components/RunningTextComponent.vue'
import BurgerMenuComponent from '@/components/BurgerMenuComponent.vue'

const INITIALIZATION_DELAY = 300
const POINTER_EVENTS_DELAY = 1500

const ANIMATION_TIMELINE = {
  background: { duration: 1.2 },
  runningText: { duration: 1, overlap: 0.8 },
  logo: { duration: 0.8, overlap: 0.6 },
  language: { duration: 0.7, overlap: 0.4 },
  burger: { duration: 0.7, overlap: 0.7 },
  title: { duration: 0.5, overlap: 0.3 },
  titleLine: { duration: 0.8, overlap: { first: 0.5, rest: 0.6 } },
  showreel: { duration: 0.9, overlap: 0.4 },
  nav: { duration: 0.7, overlap: 0.5, step: 0.1 }
}

const INITIAL_STATES = {
  background: { opacity: 0, scale: 0.8 },
  runningText1: { opacity: 0, x: -100 },
  runningText2: { opacity: 0, x: 100 },
  logo: { opacity: 0, scale: 0.5, y: -30 },
  language: { opacity: 0, x: -50, y: -50 },
  burger: { opacity: 0, x: 50, y: -50 },
  title: { opacity: 0 },
  titleLine: { opacity: 0, y: 50, rotationX: 90 },
  showreel: { opacity: 0, scale: 0, rotation: -180 },
  navLeft: { opacity: 0, x: -100, y: 0 },
  navRight: { opacity: 0, x: 100, y: 0 },
  navBottom: { opacity: 0, x: 0, y: 100 }
}

const SELECTORS = {
  logo: '.main-logo-wrapper',
  language: '.language-selector-wrapper',
  burger: '.burger-menu-button',
  title: '.main-title',
  titleLine: '.title-line',
  showreel: '.showreel-button-circle'
}

export default {
  name: 'HomePage',
  components: {
    MainLogoComponent,
    MainTitleComponent,
    NavigationLinkComponent,
    ShowreelButtonComponent,
    LanguageSelectorComponent,
    RunningTextComponent,
    BurgerMenuComponent
  },
  setup() {
    const { t } = useI18n()
    const runningText = computed(() => t('runningText'))
    
    const homePageRef = ref(null)
    const backgroundRef = ref(null)
    const runningText1Ref = ref(null)
    const runningText2Ref = ref(null)
    const logoRef = ref(null)
    const languageRef = ref(null)
    const burgerRef = ref(null)
    const titleRef = ref(null)
    const showreelRef = ref(null)
    const navLeftRef = ref(null)
    const navRightRef = ref(null)
    const navBottomRef = ref(null)

    const getElement = (componentRef, selector) => {
      if (!componentRef.value?.$el) return null
      return componentRef.value.$el.querySelector(selector) || componentRef.value.$el
    }

    const hideAllElements = async () => {
      await nextTick()
      
      if (backgroundRef.value) {
        gsap.set(backgroundRef.value, INITIAL_STATES.background)
      }
      
      if (runningText1Ref.value?.$el) {
        gsap.set(runningText1Ref.value.$el, INITIAL_STATES.runningText1)
      }
      
      if (runningText2Ref.value?.$el) {
        gsap.set(runningText2Ref.value.$el, INITIAL_STATES.runningText2)
      }
      
      const logoEl = getElement(logoRef, SELECTORS.logo)
      if (logoEl) {
        gsap.set(logoEl, INITIAL_STATES.logo)
      }
      
      const langEl = getElement(languageRef, SELECTORS.language)
      if (langEl) {
        gsap.set(langEl, INITIAL_STATES.language)
      }
      
      const burgerEl = getElement(burgerRef, SELECTORS.burger)
      if (burgerEl) {
        gsap.set(burgerEl, INITIAL_STATES.burger)
      }
      
      const titleEl = getElement(titleRef, SELECTORS.title)
      if (titleEl) {
        const titleLines = titleEl.querySelectorAll(SELECTORS.titleLine)
        gsap.set(titleEl, INITIAL_STATES.title)
        titleLines.forEach((line) => {
          gsap.set(line, INITIAL_STATES.titleLine)
        })
      }
      
      const showreelEl = getElement(showreelRef, SELECTORS.showreel)
      if (showreelEl) {
        gsap.set(showreelEl, INITIAL_STATES.showreel)
      }
      
      const navLinks = [
        { ref: navLeftRef, state: INITIAL_STATES.navLeft },
        { ref: navRightRef, state: INITIAL_STATES.navRight },
        { ref: navBottomRef, state: INITIAL_STATES.navBottom }
      ]
      
      navLinks.forEach((nav) => {
        if (nav.ref.value?.$el) {
          gsap.set(nav.ref.value.$el, nav.state)
        }
      })
    }

    const initAnimations = async () => {
      await nextTick()
      
      if (homePageRef.value) {
        homePageRef.value.style.pointerEvents = 'none'
      }
      document.body.style.pointerEvents = 'none'
      
      setTimeout(() => {
        if (homePageRef.value) {
          homePageRef.value.style.pointerEvents = 'auto'
        }
        document.body.style.pointerEvents = 'auto'
      }, POINTER_EVENTS_DELAY)

      const tl = gsap.timeline({ defaults: { ease: 'power3.out' } })

      if (backgroundRef.value) {
        tl.to(backgroundRef.value, {
          opacity: 1,
          scale: 1,
          ...ANIMATION_TIMELINE.background
        })
      }

      if (runningText1Ref.value?.$el) {
        tl.to(
          runningText1Ref.value.$el,
          {
            opacity: 1,
            x: 0,
            ...ANIMATION_TIMELINE.runningText
          },
          `-=${ANIMATION_TIMELINE.runningText.overlap}`
        )
      }

      if (runningText2Ref.value?.$el) {
        tl.to(
          runningText2Ref.value.$el,
          {
            opacity: 1,
            x: 0,
            ...ANIMATION_TIMELINE.runningText
          },
          '-=1'
        )
      }

      const logoEl = getElement(logoRef, SELECTORS.logo)
      if (logoEl) {
        tl.to(
          logoEl,
          {
            opacity: 1,
            scale: 1,
            y: 0,
            ...ANIMATION_TIMELINE.logo
          },
          `-=${ANIMATION_TIMELINE.logo.overlap}`
        )
      }

      const langEl = getElement(languageRef, SELECTORS.language)
      if (langEl) {
        tl.to(
          langEl,
          {
            opacity: 1,
            x: 0,
            y: 0,
            ...ANIMATION_TIMELINE.language
          },
          `-=${ANIMATION_TIMELINE.language.overlap}`
        )
      }

      const burgerEl = getElement(burgerRef, SELECTORS.burger)
      if (burgerEl) {
        tl.to(
          burgerEl,
          {
            opacity: 1,
            x: 0,
            y: 0,
            ...ANIMATION_TIMELINE.burger
          },
          `-=${ANIMATION_TIMELINE.burger.overlap}`
        )
      }

      const titleEl = getElement(titleRef, SELECTORS.title)
      if (titleEl) {
        const titleLines = titleEl.querySelectorAll(SELECTORS.titleLine)

        tl.to(titleEl, {
          opacity: 1,
          duration: ANIMATION_TIMELINE.title.duration
        }, `-=${ANIMATION_TIMELINE.title.overlap}`)

        titleLines.forEach((line, index) => {
          tl.to(
            line,
            {
              opacity: 1,
              y: 0,
              rotationX: 0,
              ...ANIMATION_TIMELINE.titleLine,
              ease: 'back.out(1.7)'
            },
            `-=${index === 0 ? ANIMATION_TIMELINE.titleLine.overlap.first : ANIMATION_TIMELINE.titleLine.overlap.rest}`
          )
        })
      }

      const showreelEl = getElement(showreelRef, SELECTORS.showreel)
      if (showreelEl) {
        tl.to(
          showreelEl,
          {
            opacity: 1,
            scale: 1,
            rotation: 0,
            ...ANIMATION_TIMELINE.showreel,
            ease: 'back.out(1.7)'
          },
          `-=${ANIMATION_TIMELINE.showreel.overlap}`
        )
      }

      const navLinks = [
        { ref: navLeftRef },
        { ref: navRightRef },
        { ref: navBottomRef }
      ]

      navLinks.forEach((nav, index) => {
        if (nav.ref.value?.$el) {
          tl.to(
            nav.ref.value.$el,
            {
              opacity: 1,
              x: 0,
              y: 0,
              ...ANIMATION_TIMELINE.nav,
              ease: 'power2.out'
            },
            `-=${ANIMATION_TIMELINE.nav.overlap - index * ANIMATION_TIMELINE.nav.step}`
          )
        }
      })
    }

    onMounted(async () => {
      await hideAllElements()
      setTimeout(() => {
        initAnimations()
      }, INITIALIZATION_DELAY)
    })

    return {
      t,
      runningText,
      homePageRef,
      backgroundRef,
      runningText1Ref,
      runningText2Ref,
      logoRef,
      languageRef,
      burgerRef,
      titleRef,
      showreelRef,
      navLeftRef,
      navRightRef,
      navBottomRef
    }
  }
}
</script>

<style scoped>
.home-page {
  position: relative;
  width: 100%;
  min-height: 100vh;
  overflow: hidden;
  background-color: var(--color-background);
  display: flex;
  justify-content: center;
  align-items: center;
}

.home-page-background-gradient {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  width: 800px;
  height: 800px;
  background-color: var(--color-background-gradient);
  border-radius: var(--border-radius-circle);
  filter: blur(var(--blur-background));
  z-index: var(--z-index-background);
  pointer-events: none;
}

.nav-link-left {
  position: absolute;
  left: 19px;
  top: 50%;
  transform: translateY(-50%) rotate(180deg);
  transform-origin: center;
  z-index: var(--z-index-content);
}

.nav-link-right {
  position: absolute;
  right: 19px;
  top: 50%;
  transform: translateY(-50%);
  z-index: var(--z-index-content);
}

.nav-link-bottom {
  position: absolute;
  bottom: 19px;
  left: 50%;
  transform: translateX(-50%);
  z-index: var(--z-index-content);
}

@media (max-width: 1440px) {
  .home-page-background-gradient {
    width: 578px;
    height: 578px;
  }
}

@media (max-width: 1024px) {
  .home-page-background-gradient {
    width: 482px;
    height: 482px;
  }

  .nav-link-left,
  .nav-link-right {
    font-size: var(--font-size-base);
  }
}

@media (max-width: 768px) {
  .home-page-background-gradient {
    width: 482px;
    height: 482px;
  }

  .nav-link-left {
    left: 3%;
  }

  .nav-link-right {
    right: 3%;
  }

  .nav-link-bottom {
    bottom: 3%;
  }

  .nav-link-left,
  .nav-link-right,
  .nav-link-bottom {
    display: none;
  }
}

@media (max-width: 375px) {
  .home-page-background-gradient {
    width: 294px;
    height: 294px;
  }

  .nav-link-left {
    left: 2%;
  }

  .nav-link-right {
    right: 2%;
  }

  .nav-link-bottom {
    bottom: 2%;
  }
}
</style>
