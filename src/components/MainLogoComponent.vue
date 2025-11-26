<template>
  <div class="main-logo-wrapper">
    <div class="main-logo-container" ref="logoContainer">
      <svg
        ref="logoSvg"
        class="main-logo-svg"
        viewBox="0 0 84 46"
        xmlns="http://www.w3.org/2000/svg"
      >
        <defs>
          <clipPath :id="`logoClip-${clipId}`">
            <rect
              x="0"
              :y="46 - fillProgress"
              width="84"
              :height="fillProgress"
            />
          </clipPath>
        </defs>
        <path
          d="M29.998 41.9541H27.3906V43.3691H29.4912V43.8936H27.3906V45.4756H30.1006V46H26.8145V41.4297H29.998V41.9541ZM34.6387 45.4111H34.959L36.208 41.4287H36.8037L35.3428 46H34.2412L32.7812 41.4287H33.3896L34.6387 45.4111ZM42.9443 41.9541H40.3369V43.3691H42.4385V43.8936H40.3369V45.4756H43.0469V46H39.7607V41.4297H42.9443V41.9541ZM49.2188 45.251H49.3594V41.4287H49.9229V46H49.0137L46.8867 42.1787H46.7461V46H46.1826V41.4287H47.0918L49.2188 45.251ZM56.4541 41.9414H54.9551V46H54.3789V41.9414H52.8672V41.4297H56.4541V41.9414ZM39.752 37.0771L24.9092 34.3682V2.70898L39.752 0V37.0771ZM10.7275 28.1719C10.7337 28.1439 10.7397 28.1158 10.7461 28.0879C10.7523 28.1159 10.7584 28.1439 10.7646 28.1719C12.6409 19.6453 14.5105 11.1526 16.3711 2.70703H21.4912V34.3496H18.0762V9.63379C18.0119 9.69742 12.41 34.2802 12.3906 34.3652H9.10059C9.07768 34.2647 3.4648 9.63379 3.41504 9.63379V34.3496H0V2.70703H5.12012C6.98088 11.1526 8.8513 19.6452 10.7275 28.1719ZM57.7676 6.12012L47.2793 30.9365H57.7676V34.3496H43.5762V30.9365L54.0645 6.12012H43.5762V2.70703H57.7676V6.12012ZM75.2207 11.1973H71.8047V6.12012H64.6055V30.9365H73.0156V21.6104H68.374V18.1973H76.4307V34.3496H61.1895V2.70703H75.2207V11.1973ZM83.2686 34.3496H79.8525V2.70703H83.2686V34.3496ZM28.3271 6.12402V30.9531L36.335 32.3076V4.76953L28.3271 6.12402Z"
          fill="transparent"
          stroke="black"
          stroke-width="0.5"
          class="logo-path-outline"
        />
        <g :clip-path="`url(#logoClip-${clipId})`">
          <path
            d="M29.998 41.9541H27.3906V43.3691H29.4912V43.8936H27.3906V45.4756H30.1006V46H26.8145V41.4297H29.998V41.9541ZM34.6387 45.4111H34.959L36.208 41.4287H36.8037L35.3428 46H34.2412L32.7812 41.4287H33.3896L34.6387 45.4111ZM42.9443 41.9541H40.3369V43.3691H42.4385V43.8936H40.3369V45.4756H43.0469V46H39.7607V41.4297H42.9443V41.9541ZM49.2188 45.251H49.3594V41.4287H49.9229V46H49.0137L46.8867 42.1787H46.7461V46H46.1826V41.4287H47.0918L49.2188 45.251ZM56.4541 41.9414H54.9551V46H54.3789V41.9414H52.8672V41.4297H56.4541V41.9414ZM39.752 37.0771L24.9092 34.3682V2.70898L39.752 0V37.0771ZM10.7275 28.1719C10.7337 28.1439 10.7397 28.1158 10.7461 28.0879C10.7523 28.1159 10.7584 28.1439 10.7646 28.1719C12.6409 19.6453 14.5105 11.1526 16.3711 2.70703H21.4912V34.3496H18.0762V9.63379C18.0119 9.69742 12.41 34.2802 12.3906 34.3652H9.10059C9.07768 34.2647 3.4648 9.63379 3.41504 9.63379V34.3496H0V2.70703H5.12012C6.98088 11.1526 8.8513 19.6452 10.7275 28.1719ZM57.7676 6.12012L47.2793 30.9365H57.7676V34.3496H43.5762V30.9365L54.0645 6.12012H43.5762V2.70703H57.7676V6.12012ZM75.2207 11.1973H71.8047V6.12012H64.6055V30.9365H73.0156V21.6104H68.374V18.1973H76.4307V34.3496H61.1895V2.70703H75.2207V11.1973ZM83.2686 34.3496H79.8525V2.70703H83.2686V34.3496ZM28.3271 6.12402V30.9531L36.335 32.3076V4.76953L28.3271 6.12402Z"
            fill="black"
            class="logo-path-filled"
          />
        </g>
      </svg>
    </div>
  </div>
</template>

<script>
import { ref, onMounted } from 'vue'
import { gsap } from 'gsap'

const LOGO_HEIGHT = 46
const ANIMATION_DURATION = 1.5
const ANIMATION_DELAY = 1.8

export default {
  name: 'MainLogoComponent',
  setup() {
    const logoContainer = ref(null)
    const logoSvg = ref(null)
    const fillProgress = ref(0)
    const clipId = ref(Math.random().toString(36).substr(2, 9))

    onMounted(() => {
      gsap.to(fillProgress, {
        value: LOGO_HEIGHT,
        duration: ANIMATION_DURATION,
        delay: ANIMATION_DELAY,
        ease: 'power2.out'
      })
    })

    return {
      logoContainer,
      logoSvg,
      fillProgress,
      clipId
    }
  }
}
</script>

<style scoped>
.main-logo-wrapper {
  position: absolute;
  top: 0;
  left: 50%;
  transform: translateX(-50%);
  z-index: var(--z-index-menu);
  padding: 24px;
}

.main-logo-container {
  display: flex;
  justify-content: center;
  align-items: center;
}

.main-logo-svg {
  width: 84px;
  height: auto;
  aspect-ratio: 84 / 46;
}

.logo-path-outline {
  fill: transparent;
  stroke: black;
  stroke-width: 0.5;
}

.logo-path-filled {
  fill: black;
}

@media (max-width: 1024px) {
  .main-logo-svg {
    width: 68px;
  }
}

@media (max-width: 375px) {
  .main-logo-svg {
    width: 45px;
  }
}
</style>
