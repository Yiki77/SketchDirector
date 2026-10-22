<script lang="ts" setup>
const projectName = 'SketchDirector'
const paperUrl = './paper/_AAAI_27__SketchDirector.pdf'

// Make sure the section IDs in your main page match these anchors.
const navigation = [
  { label: 'Overview', href: '#overview' },
  { label: 'Method', href: '#method' },
  { label: 'Results', href: '#results' },
  // { label: 'Dataset', href: '#dataset' },
]
</script>

<template>
  <header class="project-header">
    <div class="header-inner">
      <a class="brand" href="#top" :aria-label="`${projectName} — back to top`">
        {{ projectName }}
      </a>

      <!-- All five buttons are aligned on the right and share one style. -->
      <nav class="header-actions" aria-label="Project navigation">
        <a
          v-for="item in navigation"
          :key="item.label"
          class="pill-button"
          :href="item.href"
        >
          {{ item.label }}
        </a>

      </nav>
    </div>
  </header>
</template>

<style scoped>
.project-header {
  width: 100%;
  background: #faf9f7;
  border-bottom: 1px solid #e9e8e6;
  box-sizing: border-box;
}

.header-inner {
  width: 100%;
  min-height: 112px;
  margin: 0 auto;
  padding: 18px clamp(20px, 3.5vw, 72px);
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 28px;
  box-sizing: border-box;
}

.brand {
  flex-shrink: 0;
  white-space: nowrap;
  color: #191d20;
  font-family: Arial, Helvetica, sans-serif;
  font-size: clamp(23px, 2vw, 35px);
  font-weight: 800;
  letter-spacing: -1.45px;
  line-height: 1.2;
  text-decoration: none;
}

.header-actions {
  margin-left: auto;
  display: flex;
  justify-content: flex-end;
  align-items: center;
  flex-wrap: wrap;
  gap: 12px;
}

/* Shared button style for Overview, Method, Results, Dataset and Read paper */
.pill-button {
  display: inline-flex;
  justify-content: center;
  align-items: center;
  gap: 9px;
  min-height: 54px;
  padding: 0 22px;
  border: 2px solid #25292b;
  border-radius: 999px;
  box-sizing: border-box;
  background: transparent;
  color: #191d20;
  font-family: Arial, Helvetica, sans-serif;
  font-size: 17px;
  font-weight: 700;
  line-height: 1.2;
  white-space: nowrap;
  text-decoration: none;
  transition: background 0.2s ease, color 0.2s ease, border-color 0.2s ease;
}

.pill-button:hover,
.pill-button:focus-visible {
  background: #191d20;
  border-color: #191d20;
  color: #fff;
}

.arrow { font-size: 1.12em; line-height: 1; }

@media (max-width: 1180px) {
  .header-inner { gap: 20px; padding: 18px 24px; }
  .header-actions { gap: 8px; }
  .pill-button { min-height: 46px; padding: 0 15px; font-size: 14px; }
  .brand { font-size: 25px; }
}

@media (max-width: 800px) {
  .header-inner {
    flex-direction: column;
    align-items: flex-start;
    gap: 16px;
    padding: 18px 16px;
  }
  .header-actions {
    width: 100%;
    margin-left: 0;
    justify-content: flex-start;
    gap: 9px;
  }
  .pill-button { min-height: 42px; padding: 0 14px; font-size: 14px; }
}

@media (prefers-reduced-motion: reduce) {
  .pill-button { transition: none; }
}
</style>
