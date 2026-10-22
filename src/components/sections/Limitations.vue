<script setup lang="ts">
const base = (import.meta.env.BASE_URL || '/').replace(/\/?$/, '/')
const relationExamples = [
  {
    image: `${base}limitation/1.jpg`,
    prompt: 'A woman is watering the plants with a kettle in the garden.',
  },
  {
    image: `${base}limitation/2.jpg`,
    prompt: 'A castle on top of a hill.',
  },
]
const instanceExamples = [
  {
    image: `${base}limitation/3.jpg`,
    label: 'Before',
  },
  {
    image: `${base}limitation/4.jpg`,
    label: 'After',
  },
]
const instancePrompt =
  'A sheep is eating grass in the mountains.'
</script>
<template>
  <div class="limitations-section">
    <el-divider />
    <el-row justify="center">
      <el-col
        :xs="24"
        :sm="24"
        :md="24"
        :lg="24"
        :xl="24"
        class="limitations-container"
      >
<!-- ================================================== -->
        <!-- Main highlight -->
        <!-- ================================================== -->
        <section class="limitations-highlight">
          <div class="limitations-kicker" aria-hidden="true">
            <span class="limitations-kicker-colors">
              <i class="limitations-kicker-color kicker-orange" />
              <i class="limitations-kicker-color kicker-yellow" />
              <i class="limitations-kicker-color kicker-green" />
              <i class="limitations-kicker-color kicker-purple" />
            </span>
          </div>
          <h1 class="limitations-main-title">
            Limitations
          </h1>
          <p class="limitations-main-description">
            Although SketchDirector produces editable and spatially coherent multi-concept scene sketches, several challenges remain in modeling complex interactions, maintaining instance consistency during refinement, and reducing computational overhead.
          </p>
        </section>
        <!-- ================================================== -->
        <!-- One-row limitations -->
        <!-- ================================================== -->
        <div class="limitations-grid">
          <!-- A. Complex Relations -->
          <article class="limitation-card card-orange">
            <div class="card-heading">
              <span class="card-index index-orange">
                A
              </span>
              <h2>
                Complex Interactions
              </h2>
            </div>
            <div class="paired-images">
              <figure
                v-for="(example, index) in relationExamples"
                :key="example.image"
                class="mini-figure"
              >
                <el-tooltip
                  :content="example.prompt"
                  placement="top"
                  effect="light"
                  :show-after="200"
                  popper-class="limitation-prompt-tooltip"
                >
                  <div
                    class="mini-image-stage"
                    tabindex="0"
                  >
                    <img
                      :src="example.image"
                      :alt="example.prompt"
                      class="mini-image"
                      loading="lazy"
                    />
                    <span class="case-badge">
                      A{{ index + 1 }}
                    </span>
                  </div>
                </el-tooltip>
              </figure>
            </div>
            <p class="card-note">
              SketchDirector may struggle to represent complex physical relationships, particularly when objects require precise interactions or spatial dependencies.  
            </p>
          </article>
          <!-- B. Instance Consistency -->
          <article class="limitation-card card-green">
            <div class="card-heading">
              <span class="card-index index-green">
                B
              </span>
              <h2>
                Object Hallucination
              </h2>
            </div>
            <el-tooltip
              :content="instancePrompt"
              placement="top"
              effect="light"
              :show-after="200"
              popper-class="limitation-prompt-tooltip"
            >
              <div
                class="paired-images"
                tabindex="0"
              >
                <figure
                  v-for="example in instanceExamples"
                  :key="example.image"
                  class="mini-figure"
                >
                  <div class="mini-image-stage">
                    <img
                      :src="example.image"
                      :alt="`${example.label}: ${instancePrompt}`"
                      class="mini-image"
                      loading="lazy"
                    />
                    <span class="case-badge">
                      {{ example.label }}
                    </span>
                  </div>
                </figure>
              </div>
            </el-tooltip>
            <p class="card-note">
              Detail refinement may occasionally introduce redundant object instances, leading to inconsistencies in object identity or count.
            </p>
          </article>
          <!-- C. Computational Cost -->
          <article class="limitation-card card-purple">
            <div class="card-heading">
              <span class="card-index index-purple">
                C
              </span>
              <h2>
                Computational Cost
              </h2>
            </div>
            <p class="card-note">
            Iterative layout verification and diffusion-guided refinement introduce additional computational overhead, particularly for complex scenes requiring multiple reasoning or optimization rounds.
            </p>
          </article>
        </div>
      </el-col>
    </el-row>
  </div>
</template>
<style scoped>
/* SketchDirector academic project-page palette */
.limitations-section {
  width: 100%;
  color: #191d20;
  font-family: Inter, 'Helvetica Neue', Arial, sans-serif;
}
.limitations-section :deep(.el-divider) {
  max-width: 1600px;
  margin: 0 auto 48px;
  border-color: #e7e5e2;
}
.limitations-container {
  width: 100%;
  max-width: 1600px;
  padding: 0 20px;
  margin: 0 auto;
  box-sizing: border-box;
}
.limitations-highlight {
  width: 100%;
  margin: 0 auto 46px;
  padding: 0;
  background: transparent;
  border: none;
  box-shadow: none;
  text-align: center;
}
.limitations-highlight::after,
.limitations-kicker { display: none; }
.limitations-main-title {
  margin: 0 auto 18px;
  color: #191d20;
  font-size: clamp(30px, 3vw, 42px);
  font-weight: 700;
  line-height: 1.25;
  letter-spacing: -0.035em;
  text-align: center;
}
.limitations-main-description {
  max-width: 1600px;
  margin: 0 auto 22px;
  color: #62666a;
  font-size: clamp(25px, 1.5vw, 19px);
  font-weight: 400;
  line-height: 1.75;
  text-align: center;
}
.limitations-feature-chips {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  align-items: center;
  gap: 10px;
}
.limitations-chip {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  min-height: 32px;
  padding: 7px 14px;
  color: #42464a;
  font-size: 25px;
  font-weight: 600;
  line-height: 1.25;
  background: #fff;
  border: 1px solid #dedede;
  border-radius: 999px;
  box-sizing: border-box;
  white-space: nowrap;
}
.limitations-grid {
  display: grid;
  grid-template-columns: repeat(3, minmax(0, 1fr));
  gap: 18px;
  width: 100%;
  margin: 0 auto 56px;
  align-items: stretch;
}
.limitation-card {
  display: flex;
  flex-direction: column;
  min-width: 0;
  padding: 20px;
  background: #fff;
  border: 1px solid #e6e6e6;
  border-radius: 10px;
  box-shadow: none;
  box-sizing: border-box;
}
.card-heading {
  display: flex;
  align-items: center;
  gap: 10px;
  min-height: 32px;
  margin-bottom: 18px;
}
.card-heading h2 {
  margin: 0;
  color: #191d20;
  font-size: clamp(25px, 1.5vw, 19px);
  font-weight: 650;
  line-height: 1.35;
}
.card-index {
  display: inline-flex;
  flex: 0 0 30px;
  align-items: center;
  justify-content: center;
  width: 30px;
  height: 30px;
  color: #7769cb;
  font-size: 25px;
  font-weight: 700;
  background: #f4f2fa;
  border: 1px solid #e7e2f4;
  border-radius: 8px;
  box-sizing: border-box;
}
.paired-images {
  display: grid;
  grid-template-columns: repeat(2, minmax(0, 1fr));
  gap: 10px;
  width: 100%;
}
.mini-figure { min-width: 0; margin: 0; }
.mini-image-stage {
  position: relative;
  width: 100%;
  aspect-ratio: 1 / 0.82;
  overflow: hidden;
  background: #fff;
  border: 1px solid #e6e6e6;
  border-radius: 7px;
  box-sizing: border-box;
}
.mini-image {
  display: block;
  width: 100%;
  height: 100%;
  object-fit: contain;
  background: #fff;
}
.mini-image-stage:focus-visible,
.paired-images:focus-visible {
  outline: 2px solid #7769cb;
  outline-offset: 3px;
}
.case-badge {
  position: absolute;
  top: 8px;
  left: 8px;
  padding: 4px 8px;
  color: #555;
  font-size: 15px;
  font-weight: 650;
  line-height: 1.2;
  background: rgba(255,255,255,0.96);
  border: 1px solid #e5e5e5;
  border-radius: 999px;
}
.card-note {
  margin: 18px 0 0;
  color: #62666a;
  font-size: 20px;
  font-weight: 400;
  line-height: 1.75;
  text-align: left;
}
:global(.limitation-prompt-tooltip) {
  max-width: 360px;
  font-size: 20px;
  font-style: italic;
  line-height: 1.55;
  white-space: normal;
}
@media (max-width: 991px) {
  .limitations-grid { grid-template-columns: 1fr; }
  .limitation-card { padding: 18px; }
  .mini-image-stage { aspect-ratio: 1.5 / 1; }
}
@media (max-width: 767px) {
  .limitations-container { padding: 0 16px; }
  .limitations-section :deep(.el-divider) { margin-bottom: 32px; }
  .limitations-highlight { margin-bottom: 32px; }
  .limitations-main-title { font-size: 30px; }
  .limitations-main-description { font-size: 16px; }
  .limitations-chip { font-size: 11px; }
  .card-note { font-size: 14px; }
}
</style>
