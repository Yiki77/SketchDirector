<script setup lang="ts">
import { computed, nextTick, ref } from 'vue'

interface ReasoningProps {
  embedded?: boolean
  showTitle?: boolean
}

withDefaults(defineProps<ReasoningProps>(), {
  embedded: false,
  showTitle: true,
})

// Vite serves public/ from the site root. BASE_URL supports subpath deployment.
const base = (import.meta.env.BASE_URL || '/').replace(/\/?$/, '/')
const videos = Array.from({ length: 5 }, (_, index) => ({
  id: index + 1,
  label: `Example ${index + 1}`,
  src: `${base}layout_process/layout${index + 1}/layout_operations.mp4`,
}))

const verificationImageSrc = `${base}verify.jpg`
const correctionImageSrc = `${base}correction.jpg`

const activeIndex = ref(0)
const activeVideo = computed(() => videos[activeIndex.value])
const videoRef = ref<HTMLVideoElement | null>(null)
const isPlaying = ref(false)
const videoError = ref(false)

async function toggleVideo() {
  const video = videoRef.value
  if (!video) return

  if (video.paused) {
    try {
      await video.play()
    } catch {
      // Autoplay can be blocked by the browser.
    }
  } else {
    video.pause()
  }
}

async function selectVideo(index: number) {
  const nextIndex = (index + videos.length) % videos.length
  if (nextIndex === activeIndex.value) return

  videoRef.value?.pause()
  isPlaying.value = false
  videoError.value = false
  activeIndex.value = nextIndex

  // :key replaces the <video> element, so the new source starts at 0.
  await nextTick()
  const video = videoRef.value
  if (!video) return
  try {
    await video.play()
  } catch {
    // The video's autoplay attribute still handles compatible browsers.
  }
}

function onVideoKeydown(event: KeyboardEvent) {
  if (event.key === 'Enter' || event.key === ' ') {
    event.preventDefault()
    void toggleVideo()
  }
}
</script>

<template>
  <section :class="['reasoning-section', { 'reasoning-embedded': embedded }]">
    <el-divider v-if="!embedded" />

    <h2 v-if="embedded && showTitle" class="section-title method-subtitle reasoning-title">
      Layout Reasoning Process
    </h2>

    <div class="reasoning-content">
      <p class="reasoning-desc">
        SketchDirector organizes independently generated sketch assets through a closed-loop Plan–Execute–Verify–Correct process. Rather than predicting the entire scene layout at once, the agent generates executable object-level operations, evaluates the resulting composition through geometric and visual-semantic verification, and iteratively corrects layout errors with guidance from self-evolving memory.  
      </p>

      <h3 class="reasoning-subheading">Plan &amp; Execution</h3>

      <p class="reasoning-desc">
        SketchDirector translates spatial intentions into executable programs using six primitive operations: move, scale, rotate, layer, delete, and modify. By sequentially executing these operations, SketchDirector updates object attributes and scene states, producing an interpretable and traceable layout construction process.  
      </p>

      <div class="video-carousel">
        <!-- Arrows are placed at the left/right edges of the video, not below it. -->
        <div class="video-stage">
          <button
            class="carousel-arrow carousel-arrow-left"
            type="button"
            aria-label="Previous example"
            @click="selectVideo(activeIndex - 1)"
          >
            <span aria-hidden="true">‹</span>
          </button>

          <div class="video-frame">
            <video
              :key="activeVideo.id"
              ref="videoRef"
              class="reasoning-video"
              :src="activeVideo.src"
              autoplay
              muted
              loop
              playsinline
              preload="metadata"
              tabindex="0"
              :aria-label="`${activeVideo.label}: SketchDirector agentic layout reasoning video. Click or press Enter to pause or play.`"
              @click="toggleVideo"
              @keydown="onVideoKeydown"
              @play="isPlaying = true"
              @pause="isPlaying = false"
              @error="videoError = true"
              @loadeddata="videoError = false"
            >
              Your browser does not support HTML5 video.
            </video>

            <p v-if="videoError" class="video-error">
              Video could not be loaded. Check public/layout_process/layout{{ activeVideo.id }}/layout_operations.mp4.
            </p>
          </div>

          <button
            class="carousel-arrow carousel-arrow-right"
            type="button"
            aria-label="Next example"
            @click="selectVideo(activeIndex + 1)"
          >
            <span aria-hidden="true">›</span>
          </button>
        </div>

        <div class="video-controls">
          <span class="video-hint">Click to {{ isPlaying ? 'pause' : 'play' }}</span>

          <div class="carousel-dots" aria-label="Select an example">
            <button
              v-for="(item, index) in videos"
              :key="item.id"
              type="button"
              class="carousel-dot"
              :class="{ 'is-active': index === activeIndex }"
              :aria-label="`Show ${item.label}`"
              :aria-current="index === activeIndex ? 'true' : undefined"
              @click="selectVideo(index)"
            />
          </div>

          <span class="video-counter">{{ activeIndex + 1 }} / {{ videos.length }}</span>
        </div>
      </div>

      <section class="verification-section" aria-labelledby="verification-title">
        <h3 id="verification-title" class="reasoning-subheading">Multimodal Verification</h3>
        <p class="reasoning-desc">
          SketchDirector combines geometric verification with visual-semantic evaluation to assess each generated layout. Geometric checks enforce canvas boundaries and reasonable object scales, while a vision-language model evaluates semantic alignment, spatial composition, object completeness, visual aesthetics, and size consistency. Layouts that fail verification are returned to the agent for correction.
        </p>
        <img
          class="verification-image"
          :src="verificationImageSrc"
          alt="Multimodal verification process in SketchDirector"
          loading="lazy"
        />
      </section>

      <section class="correction-section" aria-labelledby="correction-title">
        <h3 id="correction-title" class="reasoning-subheading">Self-Evolving Correction</h3>
        <p class="reasoning-desc">
          SketchDirector introduces a hierarchical self-evolving memory mechanism to guide iterative layout correction. Short-Term Failure Memory records unsuccessful reasoning trajectories and their verification feedback to support immediate revisions, while Long-Term Experience Memory retrieves reusable composition strategies from previously successful scenes. Together, they enable the agent to learn from failures and accumulated experiences during layout reasoning.  
        </p>
        <img
          class="correction-image"
          :src="correctionImageSrc"
          alt="Self-evolving correction mechanism in SketchDirector"
          loading="lazy"
        />
      </section>
    </div>
  </section>
</template>

<style scoped>
.reasoning-section {
  width: 100%;
  max-width: 1600px;
  margin: 0 auto;
  background: transparent;
}

.reasoning-title {
  margin: 0 auto 26px;
  color: #191d20;
  font-size: clamp(23px, 2.4vw, 32px);
  font-weight: 650;
  line-height: 1.3;
  text-align: center;
}

.reasoning-content {
  width: 100%;
  max-width: 1600px;
  margin: 0 auto;
  padding: 0 20px;
  box-sizing: border-box;
}

.reasoning-desc {
  width: 100%;
  margin: 0 auto 36px;
  color: #62666a;
  font-size: clamp(25px, 1.5vw, 19px);
  font-weight: 400;
  line-height: 1.75;
  text-align: left;
}

.reasoning-subheading {
  margin: 0 0 24px;
  color: #7769cb;
  font-size: clamp(20px, 2vw, 26px);
  font-weight: 650;
  line-height: 1.4;
  text-align: center;
  letter-spacing: -0.025em;
}

.verification-section,
.correction-section {
  width: 100%;
  margin-top: 64px;
}

.verification-image,
.correction-image {
  display: block;
  width: 100%;
  max-width: 100%;
  height: auto;
  margin: 0 auto;
  object-fit: contain;
}

.video-carousel { width: 100%; }

.video-stage {
  position: relative;
  width: 100%;
}

.video-frame {
  position: relative;
  width: 100%;
  margin: 0 auto;
  overflow: hidden;
  background: #fff;
  border: 1px solid #e6e6e6;
  border-radius: 8px;
  box-sizing: border-box;
}

.reasoning-video {
  display: block;
  width: 100%;
  height: auto;
  aspect-ratio: 1100 / 421;
  object-fit: contain;
  background: #fff;
  cursor: pointer;
}
.reasoning-video:focus-visible,
.carousel-arrow:focus-visible,
.carousel-dot:focus-visible {
  outline: 2px solid #7769cb;
  outline-offset: 2px;
}

.video-error {
  margin: 0;
  padding: 12px 16px;
  color: #9a3030;
  font-size: 13px;
}

/* Hint and dots sit below the video, with arrows outside its left/right edges. */
.video-controls {
  display: grid;
  grid-template-columns: 1fr auto 1fr;
  align-items: center;
  gap: 12px;
  width: 100%;
  margin: 12px auto 0;
}

.video-hint {
  justify-self: start;
  display: inline-flex;
  align-items: center;
  padding: 7px 13px;
  color: #fff;
  font-size: 13px;
  font-weight: 500;
  line-height: 1.2;
  background: rgba(41, 41, 41, 0.65);
  border-radius: 999px;
  user-select: none;
  white-space: nowrap;
}

.carousel-arrow {
  position: absolute;
  top: 50%;
  z-index: 3;
  transform: translateY(-50%);
  display: inline-flex;
  align-items: center;
  justify-content: center;
  width: 48px;
  height: 48px;
  padding: 0;
  color: #292d30;
  background: #fff;
  border: 1px solid #d8d8d8;
  border-radius: 50%;
  cursor: pointer;
  transition: background-color 0.18s ease, color 0.18s ease;
}

.carousel-arrow-left { left: -66px; }
.carousel-arrow-right { right: -66px; }
.carousel-arrow span { font-size: 30px; line-height: 1; transform: translateY(-1px); }
.carousel-arrow:hover { background: #191d20; color: #fff; }

.carousel-dots { display: flex; align-items: center; justify-self: center; gap: 9px; }
.carousel-dot {
  width: 9px;
  height: 9px;
  padding: 0;
  background: #d0d0d0;
  border: 0;
  border-radius: 50%;
  cursor: pointer;
  transition: background-color 0.18s ease, transform 0.18s ease;
}
.carousel-dot.is-active { background: #7769cb; transform: scale(1.2); }
.carousel-dot:hover { background: #777; }
.carousel-dot.is-active:hover { background: #7769cb; }

.video-counter {
  justify-self: end;
  color: #777;
  font-size: 13px;
  font-variant-numeric: tabular-nums;
}

@media (max-width: 1240px) {
  /* Reserve gutters so both arrows remain outside the video image. */
  .video-stage { padding: 0 56px; box-sizing: border-box; }
  .carousel-arrow-left { left: 0; }
  .carousel-arrow-right { right: 0; }
}

@media (max-width: 767px) {
  .reasoning-content { padding: 0 16px; }
  .reasoning-desc { font-size: 25px; }
  .reasoning-subheading { font-size: 20px; margin-bottom: 20px; }
  .verification-section, .correction-section { margin-top: 44px; }
  .video-frame { border-radius: 7px; }
  .video-stage { padding: 0 34px; }
  .carousel-arrow { width: 28px; height: 28px; }
  .carousel-arrow span { font-size: 22px; }
  .video-controls { grid-template-columns: 1fr auto; row-gap: 12px; }
  .video-hint { grid-column: 1; grid-row: 1; font-size: 11px; padding: 6px 10px; }
  .video-counter { grid-column: 2; grid-row: 1; }
  .carousel-dots { grid-column: 1 / -1; grid-row: 2; }
}
</style>
