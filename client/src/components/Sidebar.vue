<template>
  <button
    class="mobile-toggle"
    type="button"
    title="Toggle menu"
    aria-label="Toggle menu"
    @click="isMobileOpen = !isMobileOpen"
  >
    <svg width="20" height="20" viewBox="0 0 20 20" fill="none">
      <path d="M3 5H17M3 10H17M3 15H17" stroke="currentColor" stroke-width="1.5" stroke-linecap="round"/>
    </svg>
  </button>

  <div
    v-if="isMobileOpen"
    class="sidebar-backdrop"
    @click="isMobileOpen = false"
  ></div>

  <aside class="sidebar" :class="{ 'sidebar-open': isMobileOpen, 'sidebar-collapsed': isCollapsed }">
    <div class="sidebar-brand">
      <div>
        <h1>{{ t('nav.companyName') }}</h1>
        <span class="sidebar-subtitle">{{ t('nav.subtitle') }}</span>
      </div>
      <button
        class="sidebar-collapse-toggle"
        type="button"
        :title="isCollapsed ? 'Expand sidebar' : 'Collapse sidebar'"
        :aria-label="isCollapsed ? 'Expand sidebar' : 'Collapse sidebar'"
        @click="toggleCollapsed"
      >
        <svg width="16" height="16" viewBox="0 0 16 16" fill="none" :style="{ transform: isCollapsed ? 'rotate(180deg)' : 'none' }">
          <path d="M10 3L5 8L10 13" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
        </svg>
      </button>
    </div>

    <nav class="sidebar-nav">
      <router-link to="/" :class="{ active: $route.path === '/' }" :title="t('nav.overview')">
        <svg class="nav-icon" width="18" height="18" viewBox="0 0 18 18" fill="none">
          <path d="M3 9L9 3L15 9" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
          <path d="M5 8V15C5 15.5523 5.44772 16 6 16H12C12.5523 16 13 15.5523 13 15V8" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
        </svg>
        <span class="nav-label">{{ t('nav.overview') }}</span>
      </router-link>

      <router-link to="/inventory" :class="{ active: $route.path === '/inventory' }" :title="t('nav.inventory')">
        <svg class="nav-icon" width="18" height="18" viewBox="0 0 18 18" fill="none">
          <path d="M2.5 5.5L9 2L15.5 5.5V12.5L9 16L2.5 12.5V5.5Z" stroke="currentColor" stroke-width="1.5" stroke-linejoin="round"/>
          <path d="M2.5 5.5L9 9L15.5 5.5" stroke="currentColor" stroke-width="1.5" stroke-linejoin="round"/>
          <path d="M9 9V16" stroke="currentColor" stroke-width="1.5"/>
        </svg>
        <span class="nav-label">{{ t('nav.inventory') }}</span>
      </router-link>

      <router-link to="/orders" :class="{ active: $route.path === '/orders' }" :title="t('nav.orders')">
        <svg class="nav-icon" width="18" height="18" viewBox="0 0 18 18" fill="none">
          <path d="M12 3H14C14.5523 3 15 3.44772 15 4V15C15 15.5523 14.5523 16 14 16H4C3.44772 16 3 15.5523 3 15V4C3 3.44772 3.44772 3 4 3H6" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
          <path d="M6 2H12V4.5H6V2Z" stroke="currentColor" stroke-width="1.5" stroke-linejoin="round"/>
          <path d="M6.5 9.5H11.5M6.5 12H11.5" stroke="currentColor" stroke-width="1.5" stroke-linecap="round"/>
        </svg>
        <span class="nav-label">{{ t('nav.orders') }}</span>
      </router-link>

      <router-link to="/spending" :class="{ active: $route.path === '/spending' }" :title="t('nav.finance')">
        <svg class="nav-icon" width="18" height="18" viewBox="0 0 18 18" fill="none">
          <path d="M9 2V16" stroke="currentColor" stroke-width="1.5" stroke-linecap="round"/>
          <path d="M12.5 5.2C12.5 5.2 11.1 3.8 9 3.8C6.9 3.8 5.5 4.9 5.5 6.5C5.5 8.1 7 8.6 9 9.2C11 9.8 12.5 10.3 12.5 11.9C12.5 13.5 11.1 14.6 9 14.6C6.9 14.6 5.5 13.2 5.5 13.2" stroke="currentColor" stroke-width="1.5" stroke-linecap="round"/>
        </svg>
        <span class="nav-label">{{ t('nav.finance') }}</span>
      </router-link>

      <router-link to="/demand" :class="{ active: $route.path === '/demand' }" :title="t('nav.demandForecast')">
        <svg class="nav-icon" width="18" height="18" viewBox="0 0 18 18" fill="none">
          <path d="M2.5 13L7 8.5L10 11.5L15.5 5" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
          <path d="M11 5H15.5V9.5" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
        </svg>
        <span class="nav-label">{{ t('nav.demandForecast') }}</span>
      </router-link>

      <router-link to="/reports" :class="{ active: $route.path === '/reports' }" title="Reports">
        <svg class="nav-icon" width="18" height="18" viewBox="0 0 18 18" fill="none">
          <path d="M4 16V9.5" stroke="currentColor" stroke-width="1.5" stroke-linecap="round"/>
          <path d="M9 16V3.5" stroke="currentColor" stroke-width="1.5" stroke-linecap="round"/>
          <path d="M14 16V7" stroke="currentColor" stroke-width="1.5" stroke-linecap="round"/>
        </svg>
        <span class="nav-label">Reports</span>
      </router-link>
    </nav>

    <div class="sidebar-footer">
      <LanguageSwitcher />
      <ProfileMenu
        @show-profile-details="$emit('show-profile-details')"
        @show-tasks="$emit('show-tasks')"
      />
    </div>
  </aside>
</template>

<script setup>
import { ref, watch } from 'vue'
import { useRoute } from 'vue-router'
import { useI18n } from '../composables/useI18n'
import LanguageSwitcher from './LanguageSwitcher.vue'
import ProfileMenu from './ProfileMenu.vue'

defineEmits(['show-profile-details', 'show-tasks'])

const { t } = useI18n()
const route = useRoute()

const isMobileOpen = ref(false)

// Manual desktop collapse toggle, persisted across sessions via localStorage.
// Separate from the tablet/mobile viewport-driven modes below.
const isCollapsed = ref(false)
try {
  isCollapsed.value = localStorage.getItem('sidebar-collapsed') === 'true'
} catch (e) {
  // localStorage can throw in some environments (private mode, etc.) — fall back to expanded
}

function toggleCollapsed() {
  isCollapsed.value = !isCollapsed.value
  try {
    localStorage.setItem('sidebar-collapsed', String(isCollapsed.value))
  } catch (e) {
    // ignore — collapse still works for this session, just won't persist
  }
}

watch(
  () => route.path,
  () => {
    isMobileOpen.value = false
  }
)
</script>

<style scoped>
.mobile-toggle {
  display: none;
}

.sidebar-backdrop {
  display: none;
}

.sidebar {
  width: var(--sidebar-width);
  height: 100vh;
  position: sticky;
  top: 0;
  flex-shrink: 0;
  background: var(--color-surface);
  border-right: 1px solid var(--color-border);
  display: flex;
  flex-direction: column;
  overflow-y: auto;
  z-index: 100;
  transition: width 0.2s ease;
}

.sidebar-brand {
  padding: var(--space-6) var(--space-5);
  border-bottom: 1px solid var(--color-border);
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.sidebar-collapse-toggle {
  display: none;
}

.sidebar-brand h1 {
  font-size: 1.375rem;
  font-weight: 700;
  color: var(--color-ink);
  letter-spacing: -0.025em;
}

.sidebar-subtitle {
  display: block;
  margin-top: var(--space-1);
  font-size: 0.813rem;
  color: var(--color-text-secondary);
  font-weight: 400;
}

.sidebar-nav {
  display: flex;
  flex-direction: column;
  gap: var(--space-2);
  padding: var(--space-4);
}

.sidebar-nav a {
  display: flex;
  align-items: center;
  gap: var(--space-3);
  padding: var(--space-3) var(--space-4);
  color: var(--color-text-secondary);
  text-decoration: none;
  font-weight: 500;
  font-size: 0.938rem;
  border-radius: var(--radius-control);
  transition: all 0.2s ease;
}

.sidebar-nav a:hover {
  color: var(--color-ink);
  background: var(--color-border-soft);
}

.sidebar-nav a.active {
  color: var(--color-accent);
  background: var(--color-accent-soft);
}

.nav-icon {
  flex-shrink: 0;
}

.nav-label {
  white-space: nowrap;
}

.sidebar-footer {
  margin-top: auto;
  padding: var(--space-4);
  border-top: 1px solid var(--color-border);
  display: flex;
  flex-direction: column;
  gap: var(--space-3);
}

/* Desktop: manual collapse toggle button, icon-rail styling for .sidebar-collapsed.
   Kept separate from the tablet media query block below (which is viewport-driven
   and already tested) to avoid any risk of regressing that behavior. Scoped to
   min-width: 1024px so a .sidebar-collapsed state persisted from a prior desktop
   session (via localStorage) can't leak its higher-specificity rules into the
   tablet/mobile layouts if the window is later resized down or reloaded narrow. */
@media (min-width: 1024px) {
  .sidebar-collapse-toggle {
    display: flex;
    align-items: center;
    justify-content: center;
    flex-shrink: 0;
    width: 24px;
    height: 24px;
    background: transparent;
    border: 1px solid var(--color-border);
    border-radius: var(--radius-control);
    color: var(--color-text-secondary);
    cursor: pointer;
  }

  .sidebar-collapse-toggle:hover {
    color: var(--color-ink);
    background: var(--color-border-soft);
  }

  .sidebar-collapse-toggle svg {
    transition: transform 0.2s ease;
  }

  .sidebar.sidebar-collapsed {
    width: var(--sidebar-width-collapsed);
    min-width: var(--sidebar-width-collapsed);
    overflow: hidden;
  }

  .sidebar.sidebar-collapsed .sidebar-brand {
    padding: var(--space-4) var(--space-2);
    text-align: center;
  }

  .sidebar.sidebar-collapsed .sidebar-brand h1 {
    display: none;
  }

  .sidebar.sidebar-collapsed .sidebar-subtitle {
    display: none;
  }

  .sidebar.sidebar-collapsed .sidebar-nav a {
    justify-content: center;
    padding: var(--space-3);
  }

  .sidebar.sidebar-collapsed .nav-label {
    display: none;
  }

  .sidebar.sidebar-collapsed .sidebar-footer {
    align-items: center;
    padding: var(--space-4) var(--space-2);
  }

  .sidebar.sidebar-collapsed .sidebar-footer :deep(.profile-name),
  .sidebar.sidebar-collapsed .sidebar-footer :deep(.language-label),
  .sidebar.sidebar-collapsed .sidebar-footer :deep(.chevron) {
    display: none;
  }
}

/* Tablet: collapse to icon-only rail */
@media (max-width: 1023px) and (min-width: 641px) {
  .sidebar {
    width: var(--sidebar-width-collapsed);
    /* Flex children default to min-width: auto, so unwrapped brand text could
       still force the sidebar wider than this fixed width and leak into
       page-level horizontal scroll. Pin min-width and clip anything that
       still tries to overflow. */
    min-width: var(--sidebar-width-collapsed);
    overflow: hidden;
  }

  .sidebar-brand {
    padding: var(--space-4) var(--space-2);
    text-align: center;
  }

  .sidebar-brand h1 {
    display: none;
  }

  .sidebar-subtitle {
    display: none;
  }

  .sidebar-nav a {
    justify-content: center;
    padding: var(--space-3);
  }

  .nav-label {
    display: none;
  }

  /* LanguageSwitcher/ProfileMenu are child components; scoped CSS here can't
     reach their internal text without :deep(), so hide it explicitly to avoid
     overflow off the collapsed 64px rail. */
  .sidebar-footer {
    align-items: center;
    padding: var(--space-4) var(--space-2);
  }

  .sidebar-footer :deep(.profile-name),
  .sidebar-footer :deep(.language-label),
  .sidebar-footer :deep(.chevron) {
    display: none;
  }
}

/* Mobile: off-canvas drawer */
@media (max-width: 640px) {
  .mobile-toggle {
    display: flex;
    align-items: center;
    justify-content: center;
    position: fixed;
    top: 12px;
    left: 12px;
    z-index: 250;
    width: 40px;
    height: 40px;
    background: var(--color-surface);
    border: 1px solid var(--color-border);
    border-radius: var(--radius-control);
    color: var(--color-ink);
    cursor: pointer;
  }

  .sidebar-backdrop {
    display: block;
    position: fixed;
    inset: 0;
    background: rgba(15, 23, 42, 0.4);
    z-index: 290;
  }

  .sidebar {
    position: fixed;
    left: 0;
    top: 0;
    width: var(--sidebar-width);
    height: 100vh;
    transform: translateX(-100%);
    transition: transform 0.25s ease;
    z-index: 300;
  }

  .sidebar.sidebar-open {
    transform: translateX(0);
  }
}
</style>
