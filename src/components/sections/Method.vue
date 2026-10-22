<script setup lang="ts">

import Page from '../mds/how_work.mdx'

import Reasoning from './Reasoning.vue'

import Concept from './Concept.vue'

import Refine from './Refine.vue'

</script>

<template>

  <div class="how-work-section">

    <el-divider />

    

    

    

    <el-row justify="center">

      <el-col

        :xs="24"

        :sm="24"

        :md="24"

        :lg="24"

        :xl="24"

      >

        <section class="method-highlight">

          <div

            class="method-kicker"

            aria-hidden="true"

          >

            <span class="method-kicker-colors">

              <i class="method-kicker-color method-kicker-orange" />

              <i class="method-kicker-color method-kicker-yellow" />

              <i class="method-kicker-color method-kicker-green" />

              <i class="method-kicker-color method-kicker-purple" />

            </span>

          </div>

          <h1 class="method-main-title">

            The Framework

          </h1>

          <p class="method-main-description">

            The goal of SketchDirector is to generate editable multiconcept scene sketches from textual descriptions.

          </p>

        </section>

      </el-col>

    </el-row>

    

    

    

    <el-row justify="center">

      <el-col

        :xs="24"

        :sm="24"

        :md="24"

        :lg="24"

        :xl="24"

      >

        <section class="method-section overview-section">

          <div class="how-work-content">

            <Page />

          </div>

        </section>

      </el-col>

    </el-row>

    

    

    

    <el-row justify="center">

      <el-col

        :xs="24"

        :sm="24"

        :md="24"

        :lg="24"

        :xl="24"

      >

        <div class="section-heading">

          <h2 class="section-heading-label">

            Asset Generation

          </h2>

          <span class="section-heading-line" />

        </div>

      </el-col>

    </el-row>

    <div class="stage-content">

      <Concept

        embedded

        :show-title="false"

      />

    </div>

    

    

    

    <el-row justify="center">

      <el-col

        :xs="24"

        :sm="24"

        :md="24"

        :lg="24"

        :xl="24"

      >

        <div class="section-heading">

          <h2 class="section-heading-label">

            Agentic Layout Reasoning

          </h2>

          <span class="section-heading-line" />

        </div>

      </el-col>

    </el-row>

    <div class="stage-content">

      <Reasoning

        embedded

        :show-title="false"

      />

    </div>

    

    

    

    <el-row justify="center">

      <el-col

        :xs="24"

        :sm="24"

        :md="24"

        :lg="24"

        :xl="24"

      >

        <div class="section-heading">

          <h2 class="section-heading-label">

            Detail Refinement

          </h2>

          <span class="section-heading-line" />

        </div>

      </el-col>

    </el-row>

    <div class="stage-content">

      <Refine

        embedded

        :show-title="false"

      />

    </div>

  </div>

</template>

<style scoped>
/* SketchDirector: clean academic project-page styling */
.method-column {
  width: 100%;
  max-width: 1600px;
  margin: 0 auto;
  padding: 0 20px;
  box-sizing: border-box;
}

.how-work-section {
  width: 100%;
  padding: 72px 0 90px;
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

.method-highlight {
  width: 100%;
  max-width: 1600px;
  margin: 0 auto 48px;
  padding: 0;
  background: transparent;
  border: none;
  box-shadow: none;
  text-align: center;
}

/* Hide the previous rainbow decoration, retain its markup untouched. */
.method-kicker { display: none; }
.method-highlight::after { content: none; }
.method-main-title {
  margin: 0 auto 20px;
  color: #191d20;
  font-size: clamp(30px, 3vw, 42px);
  font-weight: 700;
  line-height: 1.25;
  letter-spacing: -0.035em;
  text-align: center;
}
.method-main-description {
  width: 100%;
  max-width: 1600px;
  margin: 0 auto;
  color: #62666a;
  font-size: clamp(25px, 1.5vw, 19px);
  font-weight: 400;
  line-height: 1.75;
  text-align: center;
}
.method-stage-chips {
  display: flex;
  align-items: center;
  justify-content: flex-start;
  flex-wrap: wrap;
  gap: 10px;
  width: 100%;
}
.method-chip,
.method-chip-orange,
.method-chip-green,
.method-chip-purple {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  padding: 9px 17px;
  min-height: 38px;
  background: #fff;
  color: #33383c;
  border: 1px solid #d9d9d7;
  border-radius: 999px;
  box-shadow: none;
  font-size: 13px;
  font-weight: 600;
  line-height: 1.35;
  white-space: nowrap;
  box-sizing: border-box;
}
.method-section, .how-work-content, 
.stage-content {
  width: 100%;
  max-width: 1600px;
  margin: 0 auto;
}
.overview-section {
  width: 100%;
  max-width: 1600px;
  margin: 0 auto 60px;
}
.section-heading {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 100%;
  margin: 72px auto 30px;
}

.section-heading-label {
  margin: 0;
  padding: 0;
  color: #191d20;
  font-size: clamp(24px, 2.2vw, 32px);
  font-weight: 700;
  line-height: 1.35;
  letter-spacing: -0.035em;
  text-align: center;
  background: none;
}

.section-heading-line {
  display: none;
}

.how-work-content {
  width: 100%;
}

.how-work-content :deep(img) {
  display: block;
  width: 100%;
  max-width: 1600px;
  height: auto;
  margin: 24px auto 0;
  object-fit: contain;
}
/* Preserve any inherited interactive guidance, but remove colored backgrounds. */
.gallery-interaction-tip {
  display: flex;
  align-items: center;
  gap: 10px;
  margin-top: 16px;
  padding: 12px 16px;
  color: #4b5053;
  background: #fff;
  border: 1px solid #e2e1de;
  border-radius: 10px;
  box-shadow: none;
  font-size: 16px;
  font-weight: 500;
  line-height: 1.5;
}
.gallery-interaction-icon {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  flex-shrink: 0;
  width: 28px;
  height: 28px;
  color: #7769cb;
  background: transparent;
  border: 0;
  border-radius: 50%;
}
@media (max-width: 767px) {
  .method-column {
    padding: 0 16px;
  }

  .method-main-title {
    font-size: 30px;
  }

  .section-heading {
    margin: 48px auto 24px;
  }
}
</style>
