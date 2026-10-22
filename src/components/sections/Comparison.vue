<script lang="ts" setup>

import {

  computed,

  ref

} from 'vue'
// ============================================================// Types// ============================================================
interface ComparisonRow {

  model: string

  semanticAlignment: string

  spatialComposition: string

  objectCompleteness: string

  visualAesthetics: string

  sizeConsistency: string

}

type EvaluatorKey =

  | 'qwen'

  | 'claude'

  | 'gemini'

  | 'gpt'

type MetricKey =

  | 'semanticAlignment'

  | 'spatialComposition'

  | 'objectCompleteness'

  | 'visualAesthetics'

  | 'sizeConsistency'
// ============================================================// Evaluator// ============================================================
const activeTab = ref<EvaluatorKey>('qwen')
// ============================================================// Qwen3.7-Plus evaluation// ============================================================
const qwenData: ComparisonRow[] = [

  {

    model: 'VectorFusion',

    semanticAlignment: '0.327',

    spatialComposition: '0.153',

    objectCompleteness: '0.237',

    visualAesthetics: '0.283',

    sizeConsistency: '0.233'

  },

  {

    model: 'SVGDreamer',

    semanticAlignment: '0.717',

    spatialComposition: '0.590',

    objectCompleteness: '0.667',

    visualAesthetics: '0.690',

    sizeConsistency: '0.667'

  },

  {

    model: 'DiffSketcher',

    semanticAlignment: '0.543',

    spatialComposition: '0.367',

    objectCompleteness: '0.400',

    visualAesthetics: '0.550',

    sizeConsistency: '0.443'

  },

  {

    model: 'SketchAgent',

    semanticAlignment: '0.735',

    spatialComposition: '0.633',

    objectCompleteness: '0.630',

    visualAesthetics: '0.720',

    sizeConsistency: '0.647'

  },

  {

    model: 'OpenAI GPT-5.5',

    semanticAlignment: '0.918',

    spatialComposition: '0.727',

    objectCompleteness: '0.815',

    visualAesthetics: '0.843',

    sizeConsistency: '0.783'

  },

  {

    model: 'SketchDirector',

    semanticAlignment: '0.928',

    spatialComposition: '0.853',

    objectCompleteness: '0.858',

    visualAesthetics: '0.878',

    sizeConsistency: '0.818'

  },

  {

    model: 'SketchDirector (refine)',

    semanticAlignment: '0.936',

    spatialComposition: '0.875',

    objectCompleteness: '0.893',

    visualAesthetics: '0.875',

    sizeConsistency: '0.850'

  }

]
// ============================================================// Claude-Opus-4-7 evaluation// ============================================================
const claudeData: ComparisonRow[] = [

  {

    model: 'VectorFusion',

    semanticAlignment: '0.310',

    spatialComposition: '0.145',

    objectCompleteness: '0.220',

    visualAesthetics: '0.270',

    sizeConsistency: '0.240'

  },

  {

    model: 'SVGDreamer',

    semanticAlignment: '0.700',

    spatialComposition: '0.580',

    objectCompleteness: '0.650',

    visualAesthetics: '0.680',

    sizeConsistency: '0.660'

  },

  {

    model: 'DiffSketcher',

    semanticAlignment: '0.520',

    spatialComposition: '0.350',

    objectCompleteness: '0.390',

    visualAesthetics: '0.540',

    sizeConsistency: '0.430'

  },

  {

    model: 'SketchAgent',

    semanticAlignment: '0.720',

    spatialComposition: '0.620',

    objectCompleteness: '0.620',

    visualAesthetics: '0.710',

    sizeConsistency: '0.640'

  },

  {

    model: 'GPT-5.5',

    semanticAlignment: '0.900',

    spatialComposition: '0.780',

    objectCompleteness: '0.800',

    visualAesthetics: '0.830',

    sizeConsistency: '0.770'

  },

  {

    model: 'SketchDirector',

    semanticAlignment: '0.930',

    spatialComposition: '0.850',

    objectCompleteness: '0.860',

    visualAesthetics: '0.870',

    sizeConsistency: '0.820'

  },

  {

    model: 'SketchDirector(refine)',

    semanticAlignment: '0.930',

    spatialComposition: '0.850',

    objectCompleteness: '0.860',

    visualAesthetics: '0.870',

    sizeConsistency: '0.820'

  }

]
// ============================================================// Gemini3.1-Pro evaluation// ============================================================
const geminiData: ComparisonRow[] = [

  {

    model: 'VectorFusion',

    semanticAlignment: '0.4033',

    spatialComposition: '0.0967',

    objectCompleteness: '0.220',

    visualAesthetics: '0.350',

    sizeConsistency: '0.380'

  },

  {

    model: 'SVGDreamer',

    semanticAlignment: '0.6300',

    spatialComposition: '0.3833',

    objectCompleteness: '0.4900',

    visualAesthetics: '0.6233',

    sizeConsistency: '0.7667'

  },

  {

    model: 'DiffSketcher',

    semanticAlignment: '0.4717',

    spatialComposition: '0.1900',

    objectCompleteness: '0.1900',

    visualAesthetics: '0.5567',

    sizeConsistency: '0.5400'

  },

  {

    model: 'SketchAgent',

    semanticAlignment: '0.7867',

    spatialComposition: '0.480',

    objectCompleteness: '0.500',

    visualAesthetics: '0.7733',

    sizeConsistency: '0.8033'

  },

  {

    model: 'GPT-5.5',

    semanticAlignment: '0.9537',

    spatialComposition: '0.780',

    objectCompleteness: '0.8333',

    visualAesthetics: '0.730',

    sizeConsistency: '0.9333'

  },

  {

    model: 'SketchDirector',

    semanticAlignment: '0.9593',

    spatialComposition: '0.7733',

    objectCompleteness: '0.7967',

    visualAesthetics: '0.840',

    sizeConsistency: '0.8967'

  },

  {

    model: 'SketchDirector(refine)',

    semanticAlignment: '0.9667',

    spatialComposition: '0.7367',

    objectCompleteness: '0.8633',

    visualAesthetics: '0.9067',

    sizeConsistency: '0.950'

  }

]
// ============================================================// OpenAI GPT-5.5 evaluation// ============================================================
const gptData: ComparisonRow[] = [

  {

    model: 'VectorFusion',

    semanticAlignment: '0.450',

    spatialComposition: '0.250',

    objectCompleteness: '0.350',

    visualAesthetics: '0.400',

    sizeConsistency: '0.400'

  },

  {

    model: 'SVGDreamer',

    semanticAlignment: '0.7057',

    spatialComposition: '0.5753',

    objectCompleteness: '0.6393',

    visualAesthetics: '0.6287',

    sizeConsistency: '0.656'

  },

  {

    model: 'DiffSketcher',

    semanticAlignment: '0.578',

    spatialComposition: '0.4193',

    objectCompleteness: '0.416',

    visualAesthetics: '0.5293',

    sizeConsistency: '0.5313'

  },

  {

    model: 'SketchAgent',

    semanticAlignment: '0.7097',

    spatialComposition: '0.6523',

    objectCompleteness: '0.5877',

    visualAesthetics: '0.687',

    sizeConsistency: '0.6577'

  },

  {

    model: 'GPT-5.5',

    semanticAlignment: '0.8573',

    spatialComposition: '0.6497',

    objectCompleteness: '0.7733',

    visualAesthetics: '0.8303',

    sizeConsistency: '0.785'

  },

  {

    model: 'SketchDirector',

    semanticAlignment: '0.8957',

    spatialComposition: '0.7617',

    objectCompleteness: '0.757',

    visualAesthetics: '0.8147',

    sizeConsistency: '0.7363'

  },

  {

    model: 'SketchDirector(refine)',

    semanticAlignment: '0.9017',

    spatialComposition: '0.7807',

    objectCompleteness: '0.8023',

    visualAesthetics: '0.8563',

    sizeConsistency: '0.7843'

  }

]
// ============================================================// Current table data// ============================================================
const dataMap: Record<

  EvaluatorKey,

  ComparisonRow[]

> = {

  qwen: qwenData,

  claude: claudeData,

  gemini: geminiData,

  gpt: gptData

}

const currentData = computed<ComparisonRow[]>(() => {

  return dataMap[

    activeTab.value

  ]

})
// ============================================================// Bold best values// ============================================================
const isBest = (

  row: ComparisonRow,

  prop: MetricKey

) => {

  if (

    activeTab.value === 'qwen'

    && row.model === 'SketchDirector (refine)'

    && [

      'semanticAlignment',

      'spatialComposition',

      'objectCompleteness',

      'sizeConsistency'

    ].includes(prop)

  ) {

    return true

  }

  if (

    row.model === 'SketchDirector'

    && prop === 'visualAesthetics'

  ) {

    return true

  }

  return false

}
// ============================================================// Row styling// ============================================================
const getRowClassName = (

  {

    row

  }: {

    row: ComparisonRow

  }

) => {

  if (

    row.model.toLowerCase().includes(

      'SketchDirector'

    )

  ) {

    return row.model

      .toLowerCase()

      .includes('refine')

      ? 'SketchDirector-refine-row'

      : 'SketchDirector-row'

  }

  return ''

}

</script>

<template>

  <div class="comparison-section">

    <el-divider />

    <el-row justify="center">

      <el-col

        :xs="24"
        :sm="24"
        :md="24"
        :lg="24"
        :xl="24"
        class="comparison-column"

      >
<!-- ================================================== --><!-- Comparison overview --><!-- ================================================== -->
        <section class="comparison-intro-card">

          <div

            class="comparison-kicker"

            aria-hidden="true"

          >

            <span class="comparison-kicker-bar kicker-orange" />

            <span class="comparison-kicker-bar kicker-yellow" />

            <span class="comparison-kicker-bar kicker-green" />

            <span class="comparison-kicker-bar kicker-purple" />

          </div>

          <h1 class="comparison-card-title">

            Comparison

          </h1>

          <p class="comparison-desc">

            We evaluate SketchDirector against representative LLM-based and optimization-based sketch generation methods through quantitative assessments, visual comparisons, and a human preference study. The results demonstrate improvements in scene composition, object completeness, and visual quality.

          </p>


        </section>
<!-- ================================================== --><!-- Quantitative comparison --><!-- ================================================== -->
        <section class="comparison-panel">

          <div class="comparison-section-heading">

            <h2 class="comparison-section-heading-label">

              Quantitative Comparison

            </h2>

            <span class="comparison-section-heading-line" />

          </div>

          <p class="comparison-helper">

            We evaluate scene sketch quality across five dimensions using four independent vision-language models. SketchDirector achieves the highest average scores across all five metrics.

          </p>

          <div class="comparison-card">
<!-- Evaluator tabs -->
            <div class="evaluator-header">

              <span class="evaluator-label">

                Evaluator

              </span>

            </div>

            <el-tabs

              v-model="activeTab"

              class="comparison-tabs"

            >

              <el-tab-pane

                label="Qwen3.7-Plus"

                name="qwen"

              />

              <el-tab-pane

                label="Claude-Opus-4-7"

                name="claude"

              />

              <el-tab-pane

                label="Gemini3.1-Pro"

                name="gemini"

              />

              <el-tab-pane

                label="OpenAI GPT-5.5"

                name="gpt"

              />

            </el-tabs>
<!-- Table -->
            <div class="table-scroll">

              <el-table

                :data="currentData"

                table-layout="fixed"

                class="comparison-table"

                :row-class-name="getRowClassName"

              >

                <el-table-column

                  prop="model"

                  label="Method"

                  width="160"

                />

                <el-table-column

                  prop="semanticAlignment"

                  label="Semantic Alignment ↑"

                  min-width="145"

                  align="center"

                >

                  <template #default="scope">

                    <b

                      v-if="

                        isBest(

                          scope.row,

                          'semanticAlignment'

                        )

                      "

                      class="best-value"

                    >

                      {{ scope.row.semanticAlignment }}

                    </b>

                    <span v-else>

                      {{ scope.row.semanticAlignment }}

                    </span>

                  </template>

                </el-table-column>

                <el-table-column

                  prop="spatialComposition"

                  label="Spatial Composition ↑"

                  min-width="150"

                  align="center"

                >

                  <template #default="scope">

                    <b

                      v-if="

                        isBest(

                          scope.row,

                          'spatialComposition'

                        )

                      "

                      class="best-value"

                    >

                      {{ scope.row.spatialComposition }}

                    </b>

                    <span v-else>

                      {{ scope.row.spatialComposition }}

                    </span>

                  </template>

                </el-table-column>

                <el-table-column

                  prop="objectCompleteness"

                  label="Object Completeness ↑"

                  min-width="150"

                  align="center"

                >

                  <template #default="scope">

                    <b

                      v-if="

                        isBest(

                          scope.row,

                          'objectCompleteness'

                        )

                      "

                      class="best-value"

                    >

                      {{ scope.row.objectCompleteness }}

                    </b>

                    <span v-else>

                      {{ scope.row.objectCompleteness }}

                    </span>

                  </template>

                </el-table-column>

                <el-table-column

                  prop="visualAesthetics"

                  label="Visual Aesthetics ↑"

                  min-width="135"

                  align="center"

                >

                  <template #default="scope">

                    <b

                      v-if="

                        isBest(

                          scope.row,

                          'visualAesthetics'

                        )

                      "

                      class="best-value"

                    >

                      {{ scope.row.visualAesthetics }}

                    </b>

                    <span v-else>

                      {{ scope.row.visualAesthetics }}

                    </span>

                  </template>

                </el-table-column>

                <el-table-column

                  prop="sizeConsistency"

                  label="Size Consistency ↑"

                  min-width="130"

                  align="center"

                >

                  <template #default="scope">

                    <b

                      v-if="

                        isBest(

                          scope.row,

                          'sizeConsistency'

                        )

                      "

                      class="best-value"

                    >

                      {{ scope.row.sizeConsistency }}

                    </b>

                    <span v-else>

                      {{ scope.row.sizeConsistency }}

                    </span>

                  </template>

                </el-table-column>

              </el-table>

            </div>

          </div>

        </section>
<!-- ================================================== --><!-- Qualitative comparison --><!-- ================================================== -->
        <section class="comparison-panel qualitative-panel">

          <div class="comparison-section-heading">

            <h2 class="comparison-section-heading-label">

              Qualitative Comparison

            </h2>

            <span class="comparison-section-heading-line" />

          </div>

          <p class="comparison-helper">

            Compared with LLM-based methods, SketchDirector produces clearer object organization and more coherent spatial arrangements. Compared with optimization-based methods, it better preserves object boundaries and scene layouts.
          </p>

          <div class="qualitative-card">

            <img

              src="/baseline_compare.jpg"

              alt="Qualitative comparison between SketchDirector and baseline methods"

            />

          </div>

        </section>
<!-- ================================================== --><!-- User study --><!-- ================================================== -->
        <section class="comparison-panel userstudy-panel">

          <div class="comparison-section-heading">

            <h2 class="comparison-section-heading-label">

              User Study

            </h2>

            <span class="comparison-section-heading-line" />

          </div>


          <p class="comparison-helper">

              We conduct a user study with 68 participants to assess object completeness, layout alignment, and visual aesthetics. Participants compare SketchDirector with LLM-based baselines and its refined results with optimization-based baselines. Across all three criteria, participants consistently prefer SketchDirector, supporting its effectiveness in producing coherent and visually appealing scene sketches.

          </p>


          <div class="userstudy-image-card">

            <img

              src="/user_study.jpg"

              alt="User study results"

            />

          </div>


        </section>

      </el-col>

    </el-row>

  </div>

</template>

<style scoped>
/* Consistent academic page layout: shared 1600px content width. */
.comparison-section {
  width: 100%;
  padding: 72px 0 90px;
  background: #faf9f7;
  color: #191d20;
  font-family: Inter, 'Helvetica Neue', Arial, sans-serif;
  box-sizing: border-box;
}
.comparison-section :deep(.el-divider) {
  max-width: 1120px;
  margin: 0 auto 48px;
  border-color: #e7e5e2;
}
.comparison-column {
  width: 100%;
  max-width: 1600px;
  padding: 0 20px;
  margin: 0 auto;
  min-width: 0;
  box-sizing: border-box;
}
/* Keep introduction title and summary in original order. */
.comparison-intro-card {
  width: 100%;
  margin: 0 auto 60px;
  padding: 0 0 30px;
  text-align: center;
  background: transparent;
  border: 0;
  border-bottom: 1px solid #e7e5e2;
  border-radius: 0;
  box-shadow: none;
  box-sizing: border-box;
}
.comparison-kicker, .comparison-intro-card::after { display: none; }
.comparison-card-title {
  margin: 0 0 18px;
  color: #191d20;
  font-size: clamp(30px, 3vw, 42px);
  font-weight: 700;
  line-height: 1.25;
  letter-spacing: -0.035em;
  text-align: center;
}
.comparison-desc {
  width: 100%;
  max-width: 1600px;
  margin: 0 auto;
  color: #62666a;
  font-size: clamp(25px, 1.5vw, 19px);
  font-weight: 400;
  line-height: 1.75;
  text-align: center;
}
.comparison-scope-tags {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  gap: 10px;
  margin-top: 22px;
}
.comparison-scope-tag {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  padding: 8px 14px;
  min-height: 34px;
  color: #42464a;
  font-size: 12px;
  font-weight: 600;
  line-height: 1.35;
  border: 1px solid #deddda;
  border-radius: 999px;
  background: #fff;
  box-sizing: border-box;
  white-space: nowrap;
}
.comparison-panel { width: 100%; margin: 0 auto 68px; }
.comparison-section-heading {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 100%;
  margin: 0 0 22px;
}
.comparison-section-heading-label {
  margin: 0;
  padding: 0;
  color: #7769cb;
  font-size: clamp(24px, 2.2vw, 32px);
  font-weight: 700;
  line-height: 1.35;
  letter-spacing: -0.035em;
  background: none;
  border: 0;
  border-radius: 0;
  text-align: center;
}
.comparison-section-heading-line { display: none; }
.comparison-helper {
  margin: 0 auto 26px;
  color: #62666a;
  font-size: clamp(25px, 1.5vw, 19px);
  line-height: 1.75;
  text-align: left;
}
/* Quantitative evaluation: restrained table and tab styling. */
.comparison-card {
  width: 100%;
  padding: 20px;
  background: #fff;
  border: 1px solid #e5e5e5;
  border-radius: 9px;
  box-shadow: none;
  box-sizing: border-box;
}
.evaluator-header { display: flex; align-items: center; margin: 0 0 10px; }
.evaluator-label {
  color: #555b60;
  font-size: 20px;
  font-weight: 700;
  letter-spacing: 0.05em;
  text-transform: uppercase;
}
.comparison-tabs { width: 100%; margin: 0 0 16px; }
.comparison-tabs :deep(.el-tabs__header) { margin: 0; }
.comparison-tabs :deep(.el-tabs__nav-wrap::after) { display: none; }
.comparison-tabs :deep(.el-tabs__nav-scroll) { overflow-x: auto; overflow-y: hidden; padding-bottom: 2px; }
.comparison-tabs :deep(.el-tabs__nav) {
  display: flex;
  gap: 6px;
  padding: 3px;
  white-space: nowrap;
  background: #f5f5f4;
  border: 1px solid #e5e5e3;
  border-radius: 8px;
}
.comparison-tabs :deep(.el-tabs__item) {
  height: 36px;
  padding: 0 12px;
  color: #62666a;
  font-size: 20px;
  font-weight: 500;
  line-height: 36px;
  border-radius: 6px;
}
.comparison-tabs :deep(.el-tabs__item:hover) { color: #191d20; }
.comparison-tabs :deep(.el-tabs__item.is-active) {
  color: #191d20;
  font-weight: 700;
  background: #fff;
  box-shadow: 0 1px 4px rgba(0,0,0,.07);
}
.comparison-tabs :deep(.el-tabs__active-bar) { display: none; }
.table-scroll {
  width: 100%;
  max-width: 100%;
  overflow-x: auto;
  border: 1px solid #e6e6e6;
  border-radius: 7px;
  scrollbar-width: thin;
  scrollbar-color: #c6c6c6 transparent;
}
.comparison-table { width: 100%; min-width: 890px; font-size: 18px; color: #292d30; }
.comparison-table :deep(.el-table__inner-wrapper::before) { display: none; }
.comparison-table :deep(th.el-table__cell) {
  padding: 11px 0;
  background: #f6f6f5;
  border-bottom: 1px solid #e3e3e1;
}
.comparison-table :deep(td.el-table__cell) {
  padding: 11px 0;
  background: #fff;
  border-bottom: 1px solid #eeeeec;
}
.comparison-table :deep(th.el-table__cell .cell) {
  color: #35393d;
  font-weight: 700;
  line-height: 1.35;
  text-align: center;
  white-space: normal;
}
.comparison-table :deep(td.el-table__cell .cell) { color: #383c40; font-variant-numeric: tabular-nums; }
.comparison-table :deep(td.el-table__cell:first-child .cell),
.comparison-table :deep(th.el-table__cell:first-child .cell) {
  padding-left: 12px;
  text-align: left;
}
.comparison-table :deep(.SketchDirector-row td.el-table__cell) { background: #f7f5fd; }
.comparison-table :deep(.SketchDirector-refine-row td.el-table__cell) { background: #eeeaf9; }
.comparison-table :deep(.SketchDirector-row td.el-table__cell:first-child),
.comparison-table :deep(.SketchDirector-refine-row td.el-table__cell:first-child) { font-weight: 700; }
.comparison-table :deep(.SketchDirector-row:hover td.el-table__cell),
.comparison-table :deep(.SketchDirector-refine-row:hover td.el-table__cell) { background: #e9e4f8; }
.best-value { color: #51439d; font-weight: 800; }
/* Qualitative image and user-study image remain in their original positions. */
.qualitative-card, .userstudy-image-card {
  width: 100%;
  padding: 0;
  overflow: hidden;
  background: #fff;
  border: 1px solid #e6e6e6;
  border-radius: 8px;
  box-shadow: none;
  box-sizing: border-box;
}
.qualitative-card img, .userstudy-image-card img {
  display: block;
  width: 100%;
  max-width: 100%;
  height: auto;
  margin: 0;
  object-fit: contain;
}
.userstudy-desc {
  margin: 24px 0 0;
  color: #62666a;
  font-size: clamp(25px, 1.5vw, 19px);
  font-weight: 400;
  line-height: 1.75;
  text-align: left;
}
@media (max-width: 767px) {
  .comparison-section { padding: 40px 0 58px; }
  .comparison-section :deep(.el-divider) { margin-bottom: 32px; }
  .comparison-column { padding: 0 16px; }
  .comparison-intro-card { margin-bottom: 44px; padding-bottom: 24px; }
  .comparison-card-title { font-size: 30px; }
  .comparison-desc, .comparison-helper, .userstudy-desc { font-size: 16px; line-height: 1.7; }
  .comparison-section-heading-label { font-size: 23px; }
  .comparison-panel { margin-bottom: 48px; }
  .comparison-card { padding: 12px; }
  .comparison-tabs :deep(.el-tabs__item) { padding: 0 10px; font-size: 12px; }
  .comparison-table { min-width: 850px; }
  .comparison-scope-tag { font-size: 11px; padding: 7px 11px; }
}
</style>
