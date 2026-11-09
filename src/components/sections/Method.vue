<script setup lang="ts">
import { computed, onBeforeUnmount, onMounted, ref } from 'vue'

/**
 * SketchDirector – interactive, chapter-based framework walkthrough.
 * Place these files in the Vue project's public/videos/ directory:
 *   framework.mp4
 *   framework-start.jpg
 * The video is used as an animation source; no native controls are displayed.
 */
const videoUrl = `${import.meta.env.BASE_URL}videos/framework.mp4`
const posterUrl = `${import.meta.env.BASE_URL}videos/framework-start.jpg`

interface Step {
  id: string
  title: string
  description: string
  category: string
  start: number
  end: number
}

// Keep the seven chapter boundaries from the most recently provided Method.vue.
// IMPORTANT: They describe a 50.97-second video. Update the timestamps if the
// actual MP4 is shorter (the earlier uploaded framework.mp4 was 34.88 seconds).
const steps: Step[] = [
  {
    id: 'asset',
    title: 'Generate Independent Vector Sketch Assets',
    description:
      'The agent decomposes the scene description into editable SVG objects, represented by cubic Bézier curves for subsequent editing and composition.',
    category: 'Asset Generation',
    start: 0,
    end: 5.5,
  },
  {
    id: 'actions',
    title: 'Manipulate Objects with Executable Actions',
    description:
      'Six primitive operations—move, scale, rotate, layer, delete, and modify—enable flexible object-level control.',
    category: 'Agentic Layout Reasoning',
    start: 5.5,
    end: 7.5,
  },
  {
    id: 'plan-execute-verify',
    title: 'Agentic Layout Reasoning',
    description:
      'After generating draft assets, SketchDirector formulates scene sketching as an agentic layout reasoning process that employs a closed-loop Plan–Execute–Verify–Correct process to iteratively improve scene composition.',
    category: 'Agentic Layout Reasoning',
    start: 7.5,
    end: 17.5,
  },
  {
    id: 'correct-memory',
    title: 'Replan Layout with Self-Evolving Correction',
    description:
      'When verification fails, the agent analyzes errors and revises the layout program. Short-term memory records failed attempts, while long-term memory retrieves successful experiences from previous scenes to guide replanning until the layout passes verification.',
    category: 'Agentic Layout Reasoning',
    start: 17.5,
    end: 26.0,
  },
  {
    id: 'resample',
    title: 'Resample Object Curve Contours',
    description:
      'After layout verification, object contours are subdivided into a fixed number of cubic Bézier segments using the de Casteljau algorithm, preserving the original contour geometry, curve ordering, and established spatial layout.',
    category: 'Detail Refinement',
    start: 26.0,
    end: 31.0,
  },
  {
    id: 'refine',
    title: 'Refine Objects through Progressive Joint Optimization',
    description:
      'Object-level and scene-level diffusion losses jointly guide the optimization of the current object’s Bézier control points, enhancing sketch details while preserving the verified spatial layout.',
    category: 'Detail Refinement',
    start: 31.0,
    end: 35.0,
  },
  {
    id: 'final-refine',
    title: 'Progressively Construct the Final Refined Sketch',
    description:
      'The joint optimization process is repeated sequentially for each object, while previously refined objects remain fixed. As optimized objects are progressively integrated into the scene, SketchDirector produces the final refined sketch with enhanced details and consistent spatial composition.',
    category: 'Detail Refinement',
    start: 35.0,
    end: 50.97,
  },
]

const currentIndex = ref(0)
const currentTime = ref(0)
const isPlaying = ref(false)
const hasFinished = ref(false)
const videoReady = ref(false)
const videoRef = ref<HTMLVideoElement | null>(null)
const visualRef = ref<HTMLElement | null>(null)

const currentStep = computed(() => steps[currentIndex.value])
const stepCount = steps.length
const currentProgress = computed(() => {
  const step = currentStep.value
  const duration = Math.max(step.end - step.start, 0.01)
  return Math.max(0, Math.min(100, ((currentTime.value - step.start) / duration) * 100))
})

function progressFor(index: number): number {
  if (index < currentIndex.value) return 100
  if (index > currentIndex.value) return 0
  return currentProgress.value
}

let observer: IntersectionObserver | null = null
let animationFrame = 0
let hasUserInteracted = false
let hasAutoplayed = false
let isChangingStep = false
let queuedSeek: { time: number; play: boolean } | null = null

function play(): void {
  const video = videoRef.value
  if (!video) return
  if (!videoReady.value) {
    queuedSeek = { time: currentTime.value, play: true }
    video.load()
    return
  }
  void video.play().catch(() => {
    // Browser autoplay can be blocked; the play button still works manually.
    isPlaying.value = false
  })
}

function pause(): void {
  videoRef.value?.pause()
  isPlaying.value = false
}

function selectStep(index: number, autoplay = true): void {
  const target = Math.max(0, Math.min(stepCount - 1, index))
  const start = steps[target].start
  currentIndex.value = target
  currentTime.value = start
  hasFinished.value = false

  const video = videoRef.value
  if (!video) return
  if (!videoReady.value) {
    queuedSeek = { time: start, play: autoplay }
    video.load()
    return
  }

  // Avoid attempting to seek beyond a shorter media file.
  if (Number.isFinite(video.duration) && start >= video.duration) {
    pause()
    hasFinished.value = true
    return
  }

  isChangingStep = true
  video.currentTime = start
  if (autoplay) play()
  else pause()
}

function nextStep(): void {
  hasUserInteracted = true
  selectStep(currentIndex.value === stepCount - 1 ? 0 : currentIndex.value + 1)
}

function previousStep(): void {
  hasUserInteracted = true
  selectStep(currentIndex.value === 0 ? stepCount - 1 : currentIndex.value - 1)
}

function togglePlay(): void {
  hasUserInteracted = true
  if (isPlaying.value) {
    pause()
  } else if (hasFinished.value || currentTime.value >= currentStep.value.end - 0.05) {
    selectStep(hasFinished.value ? 0 : currentIndex.value)
  } else {
    play()
  }
}

function onLoadedMetadata(): void {
  const video = videoRef.value
  if (!video) return
  videoReady.value = true
  if (Number.isFinite(video.duration) && video.duration + 0.1 < steps[stepCount - 1].end) {
    console.warn(
      `[SketchDirector Method] MP4 duration (${video.duration.toFixed(2)}s) is shorter than ` +
        `the configured seven-step timeline (${steps[stepCount - 1].end}s). Update the MP4 or steps[].start/end.`,
    )
  }
  if (queuedSeek) {
    const target = queuedSeek
    queuedSeek = null
    if (target.time < video.duration) {
      isChangingStep = true
      video.currentTime = target.time
      if (target.play) play()
    }
  }
}

function onSeeked(): void {
  const video = videoRef.value
  if (!video) return
  currentTime.value = video.currentTime
  isChangingStep = false
}

function onEnded(): void {
  const video = videoRef.value
  if (!video) return
  isPlaying.value = false

  // If the MP4 is shorter than the configured timeline, stop instead of
  // repeatedly trying to seek into a nonexistent final chapter.
  if (currentIndex.value === stepCount - 1 || video.duration < steps[currentIndex.value + 1].start) {
    hasFinished.value = true
    currentTime.value = Math.min(video.duration, currentStep.value.end)
    return
  }
  selectStep(currentIndex.value + 1)
}

function advanceIfNeeded(): void {
  const video = videoRef.value
  if (!video || !videoReady.value || !isPlaying.value || video.seeking || isChangingStep) return

  currentTime.value = video.currentTime
  if (video.currentTime < currentStep.value.end - 0.04) return

  if (currentIndex.value < stepCount - 1) {
    selectStep(currentIndex.value + 1)
  } else {
    currentTime.value = currentStep.value.end
    hasFinished.value = true
    pause()
  }
}

function animateProgress(): void {
  if (!isPlaying.value) {
    animationFrame = 0
    return
  }
  advanceIfNeeded()
  animationFrame = window.requestAnimationFrame(animateProgress)
}

function onPlay(): void {
  isPlaying.value = true
  if (!animationFrame) animationFrame = window.requestAnimationFrame(animateProgress)
}

function onPause(): void {
  isPlaying.value = false
  if (animationFrame) window.cancelAnimationFrame(animationFrame)
  animationFrame = 0
}

/** Click on any individual progress segment to seek within that chapter. */
function scrubToStep(event: MouseEvent, index: number): void {
  hasUserInteracted = true
  const button = event.currentTarget as HTMLButtonElement
  const rect = button.getBoundingClientRect()
  const fraction = Math.max(0, Math.min(0.99, (event.clientX - rect.left) / rect.width))
  const step = steps[index]
  const time = step.start + fraction * (step.end - step.start)

  currentIndex.value = index
  currentTime.value = time
  hasFinished.value = false
  const video = videoRef.value
  if (!video) return
  if (!videoReady.value) {
    queuedSeek = { time, play: true }
    video.load()
    return
  }
  if (Number.isFinite(video.duration) && time >= video.duration) {
    pause()
    hasFinished.value = true
    return
  }
  isChangingStep = true
  video.currentTime = time
  play()
}

function onKeydown(event: KeyboardEvent): void {
  const el = event.target as HTMLElement
  if (['INPUT', 'TEXTAREA', 'SELECT'].includes(el.tagName) || el.isContentEditable) return
  if (event.key === 'ArrowLeft') {
    event.preventDefault()
    previousStep()
  } else if (event.key === 'ArrowRight') {
    event.preventDefault()
    nextStep()
  } else if (event.key === ' ' && el.tagName !== 'BUTTON') {
    event.preventDefault()
    togglePlay()
  }
}

onMounted(() => {
  const reducedMotion = window.matchMedia('(prefers-reduced-motion: reduce)').matches
  if (reducedMotion || typeof IntersectionObserver === 'undefined') return

  observer = new IntersectionObserver(
    (entries) => {
      if (hasUserInteracted || hasAutoplayed) return
      if (!entries.some((entry) => entry.isIntersecting)) return
      hasAutoplayed = true
      play()
    },
    { threshold: 0.35 },
  )
  if (visualRef.value) observer.observe(visualRef.value)
})

onBeforeUnmount(() => {
  observer?.disconnect()
  if (animationFrame) window.cancelAnimationFrame(animationFrame)
  videoRef.value?.pause()
})
</script>

<template>
  <section class="how-work-section" aria-labelledby="method-title">
    <el-divider />

    <div class="method-container" @keydown="onKeydown">
      <h1 id="method-title" class="method-main-title">The Framework</h1>
 
      <div class="method-interactive" aria-label="Interactive SketchDirector framework walkthrough">
        <!-- Step explanation and previous/next navigation -->
        <div class="walkthrough-top">
          <button class="nav-arrow" type="button" aria-label="Previous step" @click="previousStep">
            <svg viewBox="0 0 24 24" aria-hidden="true"><path d="m14 5-7 7 7 7" /></svg>
          </button>

          <Transition name="step-fade" mode="out-in">
            <div :key="currentStep.id" class="walkthrough-copy" aria-live="polite">
              <div class="walkthrough-heading">
                <span class="step-badge">{{ currentIndex + 1 }}</span>
                <h2>{{ currentStep.title }}</h2>
              </div>
              <p>{{ currentStep.description }}</p>
            </div>
          </Transition>

          <button class="nav-arrow" type="button" aria-label="Next step" @click="nextStep">
            <svg viewBox="0 0 24 24" aria-hidden="true"><path d="m10 5 7 7-7 7" /></svg>
          </button>
        </div>

        <!-- Playback controls ABOVE the diagram, matching the reference style -->
        <div class="walkthrough-controls">
          <button
            class="play-control"
            type="button"
            :aria-label="isPlaying ? 'Pause animation' : hasFinished ? 'Replay animation' : 'Play animation'"
            @click="togglePlay"
          >
            <svg v-if="isPlaying" viewBox="0 0 24 24" aria-hidden="true">
              <path d="M8 5v14M16 5v14" />
            </svg>
            <svg v-else-if="hasFinished" viewBox="0 0 24 24" aria-hidden="true">
              <path d="M19 8a8 8 0 1 0 1 6M19 3v5h-5" />
            </svg>
            <svg v-else viewBox="0 0 24 24" aria-hidden="true" class="play-icon">
              <path d="m9 5 10 7-10 7z" />
            </svg>
          </button>

          <nav class="segmented-progress" aria-label="Framework stages">
            <button
              v-for="(step, index) in steps"
              :key="step.id"
              class="segment-button"
              :class="{ 'is-current': currentIndex === index }"
              type="button"
              :aria-label="`Step ${index + 1}: ${step.title}`"
              :aria-current="currentIndex === index ? 'step' : undefined"
              :title="`${index + 1}. ${step.title}`"
              @click="scrubToStep($event, index)"
            >
              <span class="segment-fill" :style="{ width: `${progressFor(index)}%` }" />
            </button>
          </nav>

          <div class="step-position" aria-label="Current step">
            {{ currentIndex + 1 }} <span>/ {{ stepCount }}</span>
          </div>
        </div>

        <!-- No CSS zoom, translations, or view-switch controls -->
        <div ref="visualRef" class="walkthrough-visual">
          <video
            ref="videoRef"
            class="framework-animation"
            :src="videoUrl"
            :poster="posterUrl"
            muted
            playsinline
            preload="metadata"
            disablepictureinpicture
            :controls="false"
            aria-label="Animated diagram of the current SketchDirector framework step"
            @loadedmetadata="onLoadedMetadata"
            @seeked="onSeeked"
            @play="onPlay"
            @pause="onPause"
            @timeupdate="advanceIfNeeded"
            @ended="onEnded"
          >
            Your browser does not support HTML5 video.
          </video>
        </div>
      </div>

      <p class="method-hint">
        Use the arrows to navigate steps, click a progress segment to jump, or pause the animation at any point.
      </p>
    </div>
  </section>
</template>

<style scoped>
/* Match the SketchDirector title section: warm off-white, charcoal, violet. */
.how-work-section {
  width: 100%;
  padding: 72px 22px 85px;
  background: #faf9f7;
  color: #191d20;
  font-family: Inter, 'Helvetica Neue', Arial, sans-serif;
  box-sizing: border-box;
}

.how-work-section :deep(.el-divider) {
  max-width: 1120px;
  margin: 0 auto 48px;
  border-color: #e7e5e2;
}

.method-container {
  width: 100%;
  max-width: 1450px;
  margin: 0 auto;
}

.method-main-title {
  margin: 0 auto 36px;
  color: #191d20;
  font-size: clamp(30px, 3vw, 42px);
  font-weight: 650;
  line-height: 1.25;
  letter-spacing: -0.045em;
  text-align: center;
}

.method-interactive { width: 100%; }

.walkthrough-top {
  display: grid;
  grid-template-columns: 52px minmax(0, 1fr) 52px;
  gap: 18px;
  align-items: start;
  min-height: 123px;
}

.nav-arrow,
.play-control {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  flex: none;
  padding: 0;
  border: 1px solid #e0dce9;
  border-radius: 50%;
  background: #fff;
  color: #7769cb;
  cursor: pointer;
  transition: background-color .2s ease, border-color .2s ease, transform .2s ease;
}

.nav-arrow { width: 48px; height: 48px; margin-top: 6px; }
.nav-arrow:hover,
.play-control:hover { background: #f1effb; border-color: #b9b1e5; }
.nav-arrow:active,
.play-control:active { transform: scale(.95); }
button:focus-visible { outline: 3px solid #a99de6; outline-offset: 3px; }

.nav-arrow svg { width: 23px; height: 23px; }
.nav-arrow svg path {
  fill: none;
  stroke: currentColor;
  stroke-width: 2.4;
  stroke-linecap: round;
  stroke-linejoin: round;
}

.walkthrough-copy { min-width: 0; }
.walkthrough-heading { display: flex; align-items: center; gap: 13px; }
.step-badge {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  flex: 0 0 36px;
  width: 36px;
  height: 36px;
  border-radius: 50%;
  background: #7769cb;
  color: #fff;
  font-size: 18px;
  font-weight: 700;
}

.walkthrough-heading h2 {
  margin: 0;
  color: #7769cb;
  font-size: clamp(21px, 2.1vw, 30px);
  font-weight: 650;
  line-height: 1.35;
  letter-spacing: -0.025em;
}

.walkthrough-copy p {
  max-width: 1080px;
  margin: 12px 0 0;
  color: #555b60;
  font-size: clamp(16px, 1.5vw, 20px);
  font-weight: 400;
  line-height: 1.7;
}

.step-fade-enter-active,
.step-fade-leave-active { transition: opacity .16s ease, transform .16s ease; }
.step-fade-enter-from { opacity: 0; transform: translateY(6px); }
.step-fade-leave-to { opacity: 0; transform: translateY(-6px); }

/* Moved ABOVE the video: play button + seven segment bars + step counter. */
.walkthrough-controls {
  display: flex;
  align-items: center;
  gap: 17px;
  margin: 23px 0 20px;
  padding: 0 2px;
}

.play-control { width: 48px; height: 48px; }
.play-control svg { width: 25px; height: 25px; }
.play-control svg path {
  fill: none;
  stroke: currentColor;
  stroke-width: 2.7;
  stroke-linecap: round;
  stroke-linejoin: round;
}
.play-control svg.play-icon path { fill: currentColor; stroke: none; }

.segmented-progress {
  display: flex;
  flex: 1;
  align-items: center;
  gap: 7px;
  min-width: 0;
}
.segment-button {
  position: relative;
  flex: 1 1 0;
  min-width: 0;
  height: 8px;
  padding: 0;
  overflow: hidden;
  border: none;
  border-radius: 20px;
  background: #dedbe7;
  cursor: pointer;
  transition: background-color .2s ease;
}
.segment-button:hover { background: #c8c1e5; }
.segment-fill {
  display: block;
  width: 0;
  height: 100%;
  border-radius: inherit;
  background: #7769cb;
}

.step-position {
  flex: 0 0 52px;
  color: #575b60;
  font-size: 16px;
  font-weight: 500;
  font-variant-numeric: tabular-nums;
  text-align: right;
  white-space: nowrap;
}
.step-position span { color: #96949b; }

/* Entire video remains in its original frame. No scale/translate/XY crop. */
.walkthrough-visual {
  position: relative;
  width: 100%;
  aspect-ratio: 1400 / 720;
  overflow: hidden;
  border: 1px solid #e9e7e4;
  border-radius: 12px;
  background: #fff;
  box-sizing: border-box;
}
.framework-animation {
  display: block;
  width: 100%;
  height: 100%;
  object-fit: contain;
  pointer-events: none;
}

.method-hint {
  margin: 16px auto 0;
  color: #88878a;
  font-size: 13px;
  line-height: 1.6;
  text-align: center;
}

@media (max-width: 767px) {
  .how-work-section { padding: 52px 15px 66px; }
  .how-work-section :deep(.el-divider) { margin-bottom: 35px; }
  .method-main-title { margin-bottom: 28px; font-size: 30px; }
  .walkthrough-top {
    grid-template-columns: 36px minmax(0, 1fr) 36px;
    gap: 9px;
    min-height: 156px;
  }
  .nav-arrow { width: 36px; height: 36px; margin-top: 4px; }
  .nav-arrow svg { width: 19px; height: 19px; }
  .walkthrough-heading { gap: 8px; }
  .step-badge { width: 29px; height: 29px; flex-basis: 29px; font-size: 15px; }
  .walkthrough-heading h2 { font-size: 19px; }
  .walkthrough-copy p { margin-top: 9px; font-size: 14px; line-height: 1.55; }
  .walkthrough-controls { gap: 10px; margin: 22px 0 15px; }
  .play-control { width: 40px; height: 40px; }
  .play-control svg { width: 22px; height: 22px; }
  .segmented-progress { gap: 4px; }
  .segment-button { height: 7px; }
  .step-position { flex-basis: 40px; font-size: 13px; }
  .walkthrough-visual { border-radius: 8px; }
  .method-hint { padding: 0 5px; font-size: 11px; }
}

@media (prefers-reduced-motion: reduce) {
  .step-fade-enter-active,
  .step-fade-leave-active,
  .nav-arrow,
  .play-control { transition: none; }
}
</style>
