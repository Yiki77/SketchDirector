<script setup lang="ts">
import { computed } from 'vue'

interface RefineProps {
  embedded?: boolean
  showTitle?: boolean
}

withDefaults(defineProps<RefineProps>(), {
  embedded: false,
  showTitle: true,
})

// Vite maps files in public/ to BASE_URL, including subpath deployments.
const refineImageSrc = computed(() => {
  const base = (import.meta.env.BASE_URL || '/').replace(/\/?$/, '/')
  return `${base}refine.jpg`
})
</script>

<template>
  <section class="refine-section" :class="{ 'refine-embedded': embedded }">
    <el-divider v-if="!embedded" />

    <h1 v-if="!embedded && showTitle" class="refine-title">
      Structure-aware Refinement
    </h1>
    <h2 v-if="embedded && showTitle" class="refine-title">
      Structure-aware Refinement
    </h2>

    <div class="refine-content">
      <p class="refine-desc">
        SketchDirector enhances coarse scene drafts through structure-aware detail refinement. Curve contour resampling first converts object outlines into fixed-topology Bézier primitives, preserving their original geometry. Progressive joint optimization then refines individual objects under both object-level and scene-level supervision, improving visual details.  
      </p>

      <img
        class="refine-image"
        :src="refineImageSrc"
        alt="Structure-aware refinement overview"
        loading="lazy"
      />
    </div>
  </section>
</template>

<style scoped>
.refine-section {
  width: 100%;
  max-width: 1600px;
  margin: 0 auto;
  background: transparent;
  color: #191d20;
  box-sizing: border-box;
}

.refine-title {
  margin: 0 auto 28px;
  color: #191d20;
  font-family: Inter, 'Helvetica Neue', Arial, sans-serif;
  font-size: clamp(24px, 2.2vw, 32px);
  font-weight: 700;
  line-height: 1.35;
  text-align: center;
}

.refine-content {
  width: 100%;
  max-width: 1600px;
  margin: 0 auto;
  padding: 0 20px;
  box-sizing: border-box;
}

.refine-desc {
  width: 100%;
  margin: 0 auto 28px;
  color: #62666a;
  font-family: Inter, 'Helvetica Neue', Arial, sans-serif;
  font-size: clamp(25px, 1.5vw, 19px);
  font-weight: 400;
  line-height: 1.75;
  text-align: left;
}

.refine-image {
  display: block;
  width: 100%;
  height: auto;
  max-width: 100%;
  margin: 0 auto 36px;
  object-fit: contain;
  background: #fff;
}

@media (max-width: 767px) {
  .refine-content { padding: 0 16px; }
  .refine-title { font-size: 24px; margin-bottom: 24px; }
  .refine-desc { font-size: 25px; line-height: 1.7; }
}
</style>
