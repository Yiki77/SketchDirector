<script setup lang="ts">
import { computed, ref } from 'vue'
import { ArrowLeft, ArrowRight } from '@element-plus/icons-vue'

interface LayoutExample {
  id: number
  caption: string
  images: string[]
}

// Original scene descriptions, order preserved.
const captions: string[] = [
  'A camel crosses the desert oasis. Palm trees surround the water. A small tent stands beside the pond.',
  'A submarine travels beneath the ocean. Coral reefs grow around the seabed.',
  'A robot steps into the open museum hall. Paintings line the walls. A marble statue stands in the center.',
  'A fox walking through the forest.',
  'Two pandas sit beside a bamboo forest. A small wooden cabin stands nearby, with fallen bamboo leaves covering the ground.',
  'A coral reef teems with colorful fish, with a sea turtle gliding gracefully overhead through crystal-clear water.',
  'A staggered city skyline of spires, domes, and clock towers; street trees and lampposts line the foreground, and flocks of birds fly in the sky.',
  'A massive old banyan tree, its aerial roots drooping, sits beneath a stone table and several stone benches.',
  'A space station is located at the center, with several satellites orbiting in a single shared orbital ring.',
  'A flock of flamingos gathers around a lake, with hills stretching into the distance.',
  'A dog runs through a park. A ball lies near the dog, with a bench standing under a tree nearby.',
  'A penguin stands on a frozen lake. Ice rocks surround the penguin, with snowy mountains rising in the distance.'
]

// Asset URLs work with both Vite dev and GitHub Pages subpath deployment.
const base = (import.meta.env.BASE_URL || '/').replace(/\/?$/, '/')
const allExamples: LayoutExample[] = captions.map((caption, index) => {
  const id = index + 1
  return {
    id,
    caption,
    images: Array.from({ length: 4 }, (_, imageIndex) =>
      `${base}layouts/${id}/${imageIndex + 1}.jpg`,
    ),
  }
})

const sameExamples = allExamples.slice(0, 6)
const differentExamples = allExamples.slice(6, 12)
const sameIndex = ref(0)
const differentIndex = ref(0)
const currentSameExample = computed(() => sameExamples[sameIndex.value])
const currentDifferentExample = computed(() => differentExamples[differentIndex.value])

function previousSame() {
  sameIndex.value = (sameIndex.value - 1 + sameExamples.length) % sameExamples.length
}
function nextSame() {
  sameIndex.value = (sameIndex.value + 1) % sameExamples.length
}
function previousDifferent() {
  differentIndex.value = (differentIndex.value - 1 + differentExamples.length) % differentExamples.length
}
function nextDifferent() {
  differentIndex.value = (differentIndex.value + 1) % differentExamples.length
}
function handleSameKeydown(event: KeyboardEvent) {
  if (event.key === 'ArrowLeft') { event.preventDefault(); previousSame() }
  if (event.key === 'ArrowRight') { event.preventDefault(); nextSame() }
}
function handleDifferentKeydown(event: KeyboardEvent) {
  if (event.key === 'ArrowLeft') { event.preventDefault(); previousDifferent() }
  if (event.key === 'ArrowRight') { event.preventDefault(); nextDifferent() }
}
</script>

<template>
  <div class="layouts-section">
    <el-divider />
    <div class="layout-container">
      <section class="layout-highlight">
        <div class="layout-kicker" aria-hidden="true">
          <span class="layout-kicker-colors">
            <i class="layout-kicker-color layout-kicker-orange" />
            <i class="layout-kicker-color layout-kicker-yellow" />
            <i class="layout-kicker-color layout-kicker-green" />
            <i class="layout-kicker-color layout-kicker-purple" />
          </span>
        </div>
        <h1 class="layout-main-title">Diverse Layouts</h1>
        <p class="layout-main-description">SketchDirector supports diverse scene compositions through flexible object-level manipulation. Given the same textual description, the agent can generate alternative spatial arrangements of identical sketch assets or compose scenes using different object representations, demonstrating the flexibility of executable layout reasoning.</p>

      </section>
      <div class="stacked-layout-gallery">
    <section class="layout-group same-panel" tabindex="0" aria-label="Diverse layouts with the same objects" @keydown="handleSameKeydown">
      <div class="section-heading">
        <h2 class="section-heading-label">Diverse layouts with the same objects</h2>

      </div>
      <p class="comparison-helper">
          Using the same set of vector assets, SketchDirector generates multiple compositions by varying object positions, scales, and spatial relationships while preserving their individual representations.
      </p>
      <div class="example-content">
        <div class="example-caption-area">
          <div class="example-heading-row">
            <span class="example-label">Example {{ currentSameExample.id }}</span>
            <span class="example-progress">{{ String(sameIndex + 1).padStart(2, '0') }} / {{ String(sameExamples.length).padStart(2, '0') }}</span>
          </div>
          <p class="example-caption">“{{ currentSameExample.caption }}”</p>
        </div>
        <div class="image-navigation">
          <button type="button" class="nav-arrow nav-arrow-left" aria-label="Previous same objects example" @click="previousSame">
            <el-icon><ArrowLeft /></el-icon>
          </button>
          <div class="image-row-viewport">
            <div class="image-row">
              <figure v-for="(imageUrl, imageIndex) in currentSameExample.images" :key="`same-${currentSameExample.id}-${imageIndex}`" class="layout-figure">
                <el-image
                  :src="imageUrl"
                  :preview-src-list="currentSameExample.images"
                  :initial-index="imageIndex"
                  :alt="`same Objects example ${currentSameExample.id}, layout ${imageIndex + 1}`"
                  class="layout-image"
                  fit="contain"
                  preview-teleported
                >
                  <template #error><div class="image-error">Unable to load</div></template>
                </el-image>
                <figcaption class="image-label">Layout {{ imageIndex + 1 }}</figcaption>
              </figure>
            </div>
          </div>
          <button type="button" class="nav-arrow nav-arrow-right" aria-label="Next same objects example" @click="nextSame">
            <el-icon><ArrowRight /></el-icon>
          </button>
        </div>
        <div class="example-dots">
          <button
            v-for="(example, index) in sameExamples"
            :key="`same-dot-${example.id}`"
            type="button"
            class="example-dot"
            :class="{ 'example-dot-active': index === sameIndex }"
            :aria-label="`Show same objects example ${index + 1}`"
            :aria-current="index === sameIndex ? 'true' : undefined"
            @click="sameIndex = index"
          />
        </div>
      </div>
    </section>
    <section class="layout-group different-panel" tabindex="0" aria-label="Diverse layouts with the different objects" @keydown="handleDifferentKeydown">
      <div class="section-heading">
        <h2 class="section-heading-label">Diverse layouts with the different objects</h2>
      </div>
      <p class="comparison-helper">
        Given the same scene description, SketchDirector can compose different object drafts into distinct layouts, producing diverse visual interpretations while maintaining the intended scene semantics.    
      </p>
      <div class="example-content">
        <div class="example-caption-area">
          <div class="example-heading-row">
            <span class="example-label">Example {{ differentIndex + 1 }}</span>
            <span class="example-progress">{{ String(differentIndex + 1).padStart(2, '0') }} / {{ String(differentExamples.length).padStart(2, '0') }}</span>
          </div>
          <p class="example-caption">“{{ currentDifferentExample.caption }}”</p>
        </div>
        <div class="image-navigation">
          <button type="button" class="nav-arrow nav-arrow-left" aria-label="Previous different objects example" @click="previousDifferent">
            <el-icon><ArrowLeft /></el-icon>
          </button>
          <div class="image-row-viewport">
            <div class="image-row">
              <figure v-for="(imageUrl, imageIndex) in currentDifferentExample.images" :key="`different-${currentDifferentExample.id}-${imageIndex}`" class="layout-figure">
                <el-image
                  :src="imageUrl"
                  :preview-src-list="currentDifferentExample.images"
                  :initial-index="imageIndex"
                  :alt="`different Objects example ${currentDifferentExample.id}, layout ${imageIndex + 1}`"
                  class="layout-image"
                  fit="contain"
                  preview-teleported
                >
                  <template #error><div class="image-error">Unable to load</div></template>
                </el-image>
                <figcaption class="image-label">Layout {{ imageIndex + 1 }}</figcaption>
              </figure>
            </div>
          </div>
          <button type="button" class="nav-arrow nav-arrow-right" aria-label="Next different objects example" @click="nextDifferent">
            <el-icon><ArrowRight /></el-icon>
          </button>
        </div>
        <div class="example-dots">
          <button
            v-for="(example, index) in differentExamples"
            :key="`different-dot-${example.id}`"
            type="button"
            class="example-dot"
            :class="{ 'example-dot-active': index === differentIndex }"
            :aria-label="`Show different objects example ${index + 1}`"
            :aria-current="index === differentIndex ? 'true' : undefined"
            @click="differentIndex = index"
          />
        </div>
      </div>
    </section>
      </div>
    </div>
  </div>
</template>

<style scoped>
.comparison-helper {
  margin: 0 auto 26px;
  color: #62666a;
  font-size: clamp(25px, 1.5vw, 19px);
  line-height: 1.75;
  text-align: left;
}
.layouts-section {
  width: 100%;
  color: #191d20;
  background: #faf9f7;
  font-family: Inter, 'Helvetica Neue', Arial, sans-serif;
  box-sizing: border-box;
}
.layouts-section :deep(.el-divider) {
  width: min(calc(100% - 40px), 1600px);
  margin: 0 auto 48px;
  border-color: #e7e5e2;
}
.layout-container {
  width: 100%;
  max-width: 1600px;
  margin: 0 auto;
  padding: 0 20px;
  box-sizing: border-box;
}
/* Same heading hierarchy as Methodology / Comparison. */
.layout-highlight {
  width: 100%;
  margin: 0 auto 54px;
  padding: 0 0 30px;
  border-bottom: 1px solid #e7e5e2;
  text-align: center;
}
.layout-kicker { display: none; }
.layout-main-title {
  margin: 0 auto 18px;
  color: #191d20;
  font-size: clamp(30px, 3vw, 42px);
  font-weight: 700;
  line-height: 1.25;
  letter-spacing: -0.035em;
  text-align: center;
}
.layout-main-description {
  width: 100%;
  max-width: 1600px;
  margin: 0 auto 22px;
  color: #62666a;
  font-size: clamp(25px, 1.5vw, 19px);
  font-weight: 400;
  line-height: 1.75;
  text-align: center;
}
.layout-feature-chips {
  display: flex;
  flex-wrap: wrap;
  align-items: center;
  justify-content: center;
  gap: 9px;
}
.layout-chip {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  min-height: 32px;
  padding: 6px 14px;
  color: #555b60;
  font-size: 12px;
  font-weight: 600;
  line-height: 1.25;
  background: #fff;
  border: 1px solid #dededb;
  border-radius: 999px;
  box-sizing: border-box;
  white-space: nowrap;
}
.stacked-layout-gallery {
  display: flex;
  flex-direction: column;
  gap: 68px;
  width: 100%;
  margin: 0 auto 76px;
}
.layout-group { width: 100%; min-width: 0; outline: none; }
.layout-group:focus-visible { outline: 2px solid #7769cb; outline-offset: 5px; border-radius: 8px; }
.section-heading {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100%;
  margin: 0 0 28px;
}
.section-heading-label {
  margin: 0;
  padding: 0;
  color: #7769cb;
  font-size: clamp(22px, 2.1vw, 30px);
  font-weight: 700;
  letter-spacing: -0.035em;
  line-height: 1.35;
  text-align: center;
}
.section-heading-line { display: none; }
.example-content, .example-caption-area { position: relative; width: 100%; min-width: 0; }
.example-heading-row {
  display: flex;
  justify-content: space-between;
  align-items: center;
  gap: 16px;
  margin-bottom: 8px;
}
.example-label {
  color: #292d30;
  font-size: 30px;
  font-weight: 650;
  line-height: 1.4;
}
.example-progress {
  flex-shrink: 0;
  padding: 5px 10px;
  background: #f2f1f0;
  color: #777;
  border-radius: 999px;
  font-size: 25px;
  font-variant-numeric: tabular-nums;
}
.example-caption {
  margin: 0 0 17px;
  color: #555b60;
  font-size: 25px;
  font-style: italic;
  line-height: 1.65;
  text-align: left;
}
.image-navigation { position: relative; width: 100%; }
.nav-arrow {
  position: absolute;
  top: 50%;
  z-index: 5;
  display: flex;
  align-items: center;
  justify-content: center;
  width: 46px;
  height: 46px;
  padding: 0;
  color: #292d30;
  background: #fff;
  border: 1px solid #d8d8d8;
  border-radius: 50%;
  box-shadow: 0 3px 12px rgba(0, 0, 0, .055);
  transform: translateY(-50%);
  cursor: pointer;
  transition: background-color .18s ease, color .18s ease;
}
.nav-arrow-left { left: -62px; }
.nav-arrow-right { right: -62px; }
.nav-arrow:hover { color: #fff; background: #191d20; }
.nav-arrow:focus-visible, .example-dot:focus-visible { outline: 2px solid #7769cb; outline-offset: 3px; }
.image-row-viewport {
  width: 100%;
  overflow-x: auto;
  overflow-y: hidden;
  scrollbar-width: thin;
  scrollbar-color: #ccc transparent;
}
.image-row { display: grid; grid-template-columns: repeat(4, minmax(0, 1fr)); gap: 12px; width: 100%; }
.layout-figure {
  min-width: 0;
  margin: 0;
  overflow: hidden;
  background: #fff;
  border: 1px solid #e6e6e6;
  border-radius: 8px;
  box-sizing: border-box;
}
.layout-image { display: block; width: 100%; aspect-ratio: 1 / 1; background: #fff; cursor: zoom-in; }
.layout-image :deep(.el-image__inner) {
  display: block;
  width: 100%;
  height: 100%;
  padding: 5px;
  object-fit: contain;
  box-sizing: border-box;
  transition: transform .2s ease;
}
.layout-figure:hover .layout-image :deep(.el-image__inner) { transform: scale(1.02); }
.image-label {
  padding: 9px 4px;
  color: #62666a;
  font-size: 20px;
  font-weight: 600;
  line-height: 1.35;
  text-align: center;
  background: #fafafa;
  border-top: 1px solid #ececec;
}
.image-error {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 100%;
  height: 100%;
  color: #999;
  font-size: 13px;
  background: #f7f7f7;
}
.example-dots {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 9px;
  margin-top: 18px;
}
.example-dot {
  width: 9px;
  height: 9px;
  padding: 0;
  border: 0;
  border-radius: 50%;
  background: #d0d0d0;
  cursor: pointer;
  transition: background-color .18s ease, transform .18s ease;
}
.example-dot:hover { background: #777; }
.example-dot-active, .example-dot-active:hover { background: #7769cb; transform: scale(1.2); }
@media (max-width: 1240px) {
  .image-navigation { padding: 0 52px; box-sizing: border-box; }
  .nav-arrow-left { left: 0; }
  .nav-arrow-right { right: 0; }
}
@media (max-width: 767px) {
  .layout-container { padding: 0 16px; }
  .layouts-section :deep(.el-divider) { margin-bottom: 32px; }
  .layout-highlight { margin-bottom: 42px; padding-bottom: 24px; }
  .layout-main-title { font-size: 30px; }
  .layout-main-description { font-size: 16px; line-height: 1.7; }
  .stacked-layout-gallery { gap: 48px; margin-bottom: 55px; }
  .comparison-desc, .comparison-helper, .userstudy-desc { font-size: 16px; line-height: 1.7; }
  .section-heading { margin-bottom: 22px; }
  .section-heading-label { font-size: 21px; }
  .example-label { font-size: 15px; }
  .example-caption { font-size: 15px; }
  .image-navigation { padding: 0 36px; }
  .nav-arrow { width: 28px; height: 28px; }
  .image-row { min-width: 660px; }
}
</style>
