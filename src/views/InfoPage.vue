<template>
  <div class="info-page">
    <div class="info-page-container">
      <MainLogoComponent />
      <h1 class="info-page-title">{{ currentLink }} {{ $t('infoPage.title') }}</h1>
      <p class="info-page-description">
        {{ $t('infoPage.description') }}
      </p>
      <router-link to="/" class="info-page-back-link">
        {{ $t('infoPage.backToHome') }}
      </router-link>
    </div>
  </div>
</template>

<script>
import { computed } from 'vue'
import { useRoute } from 'vue-router'
import MainLogoComponent from '@/components/MainLogoComponent.vue'

const DEFAULT_LINK = 'Info'

export default {
  name: 'InfoPage',
  components: {
    MainLogoComponent
  },
  setup() {
    const route = useRoute()
    
    const currentLink = computed(() => {
      const query = route.query.link || DEFAULT_LINK
      return query.charAt(0).toUpperCase() + query.slice(1)
    })

    return {
      currentLink
    }
  }
}
</script>

<style scoped>
.info-page {
  position: relative;
  width: 100%;
  min-height: 100vh;
  background-color: var(--color-info-page-bg);
  display: flex;
  justify-content: center;
  align-items: center;
  padding: var(--spacing-lg);
}

.info-page-container {
  max-width: 800px;
  width: 100%;
  text-align: center;
  z-index: var(--z-index-content);
}

.info-page-title {
  font-family: var(--font-fallback);
  font-size: 48px;
  font-weight: var(--font-weight-bold);
  color: var(--color-text-primary);
  margin: var(--spacing-xl) 0 var(--spacing-lg);
  text-transform: uppercase;
}

.info-page-description {
  font-family: var(--font-fallback);
  font-size: 18px;
  color: var(--color-text-secondary);
  margin-bottom: 30px;
  line-height: 1.6;
}

.info-page-back-link {
  display: inline-block;
  font-family: var(--font-fallback);
  font-size: var(--font-size-base);
  color: var(--color-text-primary);
  text-decoration: none;
  padding: 12px 24px;
  border: 2px solid var(--color-text-primary);
  border-radius: var(--border-radius-small);
  transition: all var(--transition-fast);
}

.info-page-back-link:hover {
  background-color: var(--color-text-primary);
  color: var(--color-white);
}

@media (max-width: 768px) {
  .info-page-title {
    font-size: 36px;
  }

  .info-page-description {
    font-size: var(--font-size-base);
  }
}

@media (max-width: 375px) {
  .info-page-title {
    font-size: 28px;
  }

  .info-page-description {
    font-size: var(--font-size-base-tablet);
  }
}
</style>
