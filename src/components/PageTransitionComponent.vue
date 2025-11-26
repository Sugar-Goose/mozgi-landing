<template>
  <div class="page-transition" ref="transitionRef"></div>
</template>

<script>
import { ref, onMounted } from 'vue'
import { useRouter } from 'vue-router'
import { gsap } from 'gsap'

const TRANSITION_DURATION = 0.6
const TRANSITION_DELAY = 50

export default {
  name: 'PageTransitionComponent',
  setup() {
    const transitionRef = ref(null)
    const router = useRouter()
    let isTransitioning = false
    let pendingNext = null

    const animateIn = (targetPath, next) => {
      if (!transitionRef.value || isTransitioning) {
        if (next) next()
        return
      }
      
      isTransitioning = true
      pendingNext = next

      gsap.set(transitionRef.value, {
        x: '-100%',
        opacity: 1
      })

      gsap.to(transitionRef.value, {
        x: '0%',
        duration: TRANSITION_DURATION,
        ease: 'power2.inOut',
        onComplete: () => {
          if (pendingNext) {
            pendingNext()
            pendingNext = null
          }
          setTimeout(() => {
            animateOut()
          }, TRANSITION_DELAY)
        }
      })
    }

    const animateOut = () => {
      if (!transitionRef.value) return

      gsap.to(transitionRef.value, {
        x: '100%',
        duration: TRANSITION_DURATION,
        ease: 'power2.inOut',
        onComplete: () => {
          isTransitioning = false
          gsap.set(transitionRef.value, {
            opacity: 0
          })
        }
      })
    }

    onMounted(() => {
      if (transitionRef.value) {
        gsap.set(transitionRef.value, {
          x: '-100%',
          opacity: 0
        })
      }

      router.beforeEach((to, from, next) => {
        if (from.name && !isTransitioning) {
          animateIn(to.fullPath, next)
        } else {
          next()
        }
      })
    })

    return {
      transitionRef
    }
  }
}
</script>

<style scoped>
.page-transition {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: var(--color-page-transition);
  z-index: var(--z-index-transition);
  pointer-events: none;
  will-change: transform;
  transform: translateX(-100%);
}
</style>
