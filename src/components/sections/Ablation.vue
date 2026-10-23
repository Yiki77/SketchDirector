<script setup lang="ts">

import { computed, ref } from 'vue'

import { ArrowLeft, ArrowRight } from '@element-plus/icons-vue'

const base = (import.meta.env.BASE_URL || '/').replace(/\/?$/, '/')



interface ComparisonImage {

  label: string

  src: string

}



interface ComparisonGroup {

  id: number

  prompt: string

  images: ComparisonImage[]

}



/* ============================================================ */



const backboneRows = [

  {

    method: 'Gemini-3.1-pro',

    sem: '0.858',

    comp: '0.723',

    compl: '0.723',

    aes: '0.819',

    size: '0.707',

    iter: '5.332',

    ver: '0.535',

    best: false,

  },

  {

    method: 'Claude-Opus-4-7',

    sem: '0.879',

    comp: '0.681',

    compl: '0.791',

    aes: '0.739',

    size: '0.721',

    iter: '5.843',

    ver: '0.477',

    best: false,

  },

  {

    method: 'Qwen3.7-Plus',

    sem: '0.629',

    comp: '0.477',

    compl: '0.526',

    aes: '0.552',

    size: '0.507',

    iter: '10.743',

    ver: '0.436',

    best: false,

  },

  {

    method: 'OpenAI GPT-5.5',

    sem: '0.926',

    comp: '0.795',

    compl: '0.811',

    aes: '0.871',

    size: '0.785',

    iter: '1.656',

    ver: '0.803',

    best: true,

  },

]



const reasoningRows = [

  {

    method: 'w/o Plan',

    sem: '0.680',

    comp: '0.507',

    compl: '0.573',

    aes: '0.648',

    size: '0.472',

    iter: '12.375',

    ver: '0.357',

    best: false,

  },

  {

    method: 'w/o Geo. Ver.',

    sem: '0.700',

    comp: '0.553',

    compl: '0.570',

    aes: '0.658',

    size: '0.589',

    iter: '1.857',

    ver: '0.633',

    best: false,

  },

  {

    method: 'w/o Sem. Ver.',

    sem: '0.606',

    comp: '0.515',

    compl: '0.647',

    aes: '0.576',

    size: '0.550',

    iter: '1.438',

    ver: '0.468',

    best: false,

  },

  {

    method: 'w/o Short Mem.',

    sem: '0.740',

    comp: '0.661',

    compl: '0.607',

    aes: '0.677',

    size: '0.687',

    iter: '2.875',

    ver: '0.635',

    best: false,

  },

  {

    method: 'w/o Long Mem.',

    sem: '0.747',

    comp: '0.697',

    compl: '0.615',

    aes: '0.713',

    size: '0.677',

    iter: '1.600',

    ver: '0.650',

    best: false,

  },

  {

    method: 'SketchDirector',

    sem: '0.817',

    comp: '0.769',

    compl: '0.707',

    aes: '0.738',

    size: '0.733',

    iter: '1.375',

    ver: '0.873',

    best: true,

  },

]



/* ============================================================ */



const backboneMethods = [

  { key: 'gpt', label: 'OpenAI GPT-5.5' },

  { key: 'gemini', label: 'Gemini-3.1-pro' },

  { key: 'claude', label: 'Claude-Opus-4-7' },

  { key: 'qwen', label: 'Qwen3.7-Plus' },

]



const backbonePrompts = [

  'A fox walking through the forest.',

  'A painter stands before an easel. A paint box lies on the ground, with several brushes inside it.',

  'A farmer, pitchfork in hand, stands before a barn door, its door wide open, revealing stacks of sacks and farm tools.',

  'A billboard stands on a city rooftop, with skyscrapers rising behind it and traffic flowing on the street below.',

]



const backboneGroups: ComparisonGroup[] = backbonePrompts.map((prompt, index) => ({

  id: index + 1,

  prompt,

  images: backboneMethods.map((method) => ({

    label: method.label,

    src: `${base}backbones/${index + 1}-${method.key}.jpg`,

  })),

}))



/* ============================================================ */



const reasoningMethods = [

  { key: 'plan', label: 'w/o Plan' },

  { key: 'geo', label: 'w/o Geo. Ver.' },

  { key: 'sem', label: 'w/o Sem. Ver.' },

  { key: 'short', label: 'w/o Short Merm.' },

  { key: 'long', label: 'w/o Long Merm.' },

  { key: 'SketchDirector', label: 'SketchDirector' },

]



const reasoningPrompts = [

  'A bird sits on a tree branch. A small nest rests beside it, with leaves and flowers surrounding the tree.',

  'A drone flies through a futuristic city. Hovering vehicles move between skyscrapers.',

  'A monkey swings on a jungle vine. Large trees surround the monkey, with a waterfall flowing between the rocks.',

  'A diver explores an underwater cave. Coral reefs cover the rocks, with schools of fish swimming overhead.',

]



const reasoningGroups: ComparisonGroup[] = reasoningPrompts.map((prompt, index) => ({

  id: index + 1,

  prompt,

  images: reasoningMethods.map((method) => ({

    label: method.label,

    src: `${base}reasoning/${index + 1}-${method.key}.jpg`,

  })),

}))



/* ============================================================ */



const resamplingPrompts = [

  'The bus stopped at the stop.',

  'A lighthouse stands beside rocks, with a sailboat in the distance.',

  'A shop with potted flowers and a bicycle in the front yard.',

  'A clothesline hangs on a tree on the left and on the eaves on the right, with several pieces of clothing, including skirts and pants, hanging on it.',

]



const resamplingGroups: ComparisonGroup[] = resamplingPrompts.map((prompt, index) => ({

  id: index + 1,

  prompt,

  images: [

    {

      label: 'Point-based Resampling',

      src: `${base}resampling/${index + 1}-point.jpg`,

    },

    {

      label: 'Point-based Result',

      src: `${base}resampling/${index + 1}-point-result.jpg`,

    },

    {

      label: 'Contour Resampling',

      src: `${base}resampling/${index + 1}-curve.jpg`,

    },

    {

      label: 'Contour Result',

      src: `${base}resampling/${index + 1}-curve-result.jpg`,

    },

  ],

}))



/* ============================================================ */



const optimizationPrompts = [

  'A woman planting flowers in a backyard garden.',

  'A man walking his dog along a beach.',

  'An old tower standing among ancient ruins.',

  'A bridge in front a mountain connects two small villages across a valley.',

]



const optimizationGroups: ComparisonGroup[] = optimizationPrompts.map((prompt, index) => ({

  id: index + 1,

  prompt,

  images: [

    {

      label: 'Draft',

      src: `${base}optimization/${index + 1}-draft.jpg`,

    },

    {

      label: 'SketchDirector',

      src: `${base}optimization/${index + 1}-SketchDirector.jpg`,

    },

    {

      label: 'w/o Local',

      src: `${base}optimization/${index + 1}-local.jpg`,

    },

    {

      label: 'w/o Global',

      src: `${base}optimization/${index + 1}-global.jpg`,

    },

    {

      label: 'w/o Progressive',

      src: `${base}optimization/${index + 1}-progressive.jpg`,

    },

  ],

}))



/* ============================================================ */



const backboneIndex = ref(0)

const reasoningIndex = ref(0)

const resamplingIndex = ref(0)

const optimizationIndex = ref(0)



const currentBackbone = computed(() => backboneGroups[backboneIndex.value])

const currentReasoning = computed(() => reasoningGroups[reasoningIndex.value])

const currentResampling = computed(() => resamplingGroups[resamplingIndex.value])

const currentOptimization = computed(() => optimizationGroups[optimizationIndex.value])



const previousBackbone = () => {

  backboneIndex.value =

    (backboneIndex.value - 1 + backboneGroups.length) % backboneGroups.length

}



const nextBackbone = () => {

  backboneIndex.value =

    (backboneIndex.value + 1) % backboneGroups.length

}



const previousReasoning = () => {

  reasoningIndex.value =

    (reasoningIndex.value - 1 + reasoningGroups.length) % reasoningGroups.length

}



const nextReasoning = () => {

  reasoningIndex.value =

    (reasoningIndex.value + 1) % reasoningGroups.length

}



const previousResampling = () => {

  resamplingIndex.value =

    (resamplingIndex.value - 1 + resamplingGroups.length) % resamplingGroups.length

}



const nextResampling = () => {

  resamplingIndex.value =

    (resamplingIndex.value + 1) % resamplingGroups.length

}



const previousOptimization = () => {

  optimizationIndex.value =

    (optimizationIndex.value - 1 + optimizationGroups.length) % optimizationGroups.length

}



const nextOptimization = () => {

  optimizationIndex.value =

    (optimizationIndex.value + 1) % optimizationGroups.length

}



const handleKeydown = (

  event: KeyboardEvent,

  previous: () => void,

  next: () => void,

) => {

  if (event.key === 'ArrowLeft') {

    event.preventDefault()

    previous()

  }



  if (event.key === 'ArrowRight') {

    event.preventDefault()

    next()

  }

}

</script>



<template>

  <div class="ablation-layout-section">

    <el-divider />



    <!-- ================================================== -->

    <!-- Main highlight -->

    <!-- ================================================== -->

    <el-row justify="center">

      <el-col

        :xs="24"

        :sm="24"
        :md="24"
        :lg="24"
        :xl="24"
        class="ablation-column"

      >

        <section class="ablation-highlight">

          <div class="ablation-kicker" aria-hidden="true">

            <span class="ablation-kicker-colors">

              <i class="ablation-kicker-color kicker-orange" />

              <i class="ablation-kicker-color kicker-yellow" />

              <i class="ablation-kicker-color kicker-green" />

              <i class="ablation-kicker-color kicker-purple" />

            </span>

          </div>



          <h1 class="ablation-main-title">

            Ablation Study

          </h1>



          <p class="ablation-main-description">

            We conduct ablation studies to investigate the contributions of different VLM backbones, agentic reasoning components, and detail refinement strategies. Through quantitative and visual comparisons, we examine their effects on scene quality, spatial coherence, and reasoning efficiency.

          </p>



          
        </section>

      </el-col>

    </el-row>



    <!-- ================================================== -->

    <!-- Main content -->

    <!-- ================================================== -->

    <el-row justify="center">

      <el-col

        :xs="24"

        :sm="24"
        :md="24"
        :lg="24"
        :xl="24"
        class="ablation-column"

      >

        <div class="ablation-stack">



          <!-- ================================================== -->

          <!-- VLM Backbones -->

          <!-- ================================================== -->

          <section class="ablation-group backbone-panel">

            <div class="section-heading">

              <h2 class="section-heading-label">

                Impact of VLM Backbones

              </h2>

              <span class="section-heading-line" />

            </div>

            <p class="comparison-helper">
              We evaluate SketchDirector with four VLM backbones to examine their influence on scene sketch generation. OpenAI GPT-5.5 achieves the strongest overall verification performance and requires the fewest reasoning iterations. Gemini-3.1-Pro and Claude-Opus-4-7 also capture major scene concepts, but generally produce fewer visual details, while Qwen3.7-Plus requires more iterations and achieves lower layout verification scores.  
            </p>



            <div class="result-area">

              <div class="minor-title">

                VLM Backbone Ablation

              </div>



              <div class="table-scroll">

                <table class="ablation-table">

                  <thead>

                    <tr>

                      <th class="method-col">Method</th>

                      <th>Sem. ↑</th>

                      <th>Comp. ↑</th>

                      <th>Compl. ↑</th>

                      <th>Aes. ↑</th>

                      <th>Size ↑</th>

                      <th>Iter. ↓</th>

                      <th>Ver. ↑</th>

                    </tr>

                  </thead>



                  <tbody>

                    <tr

                      v-for="row in backboneRows"

                      :key="row.method"

                      :class="{ 'best-row': row.best }"

                    >

                      <td class="method-col">{{ row.method }}</td>

                      <td>{{ row.sem }}</td>

                      <td>{{ row.comp }}</td>

                      <td>{{ row.compl }}</td>

                      <td>{{ row.aes }}</td>

                      <td>{{ row.size }}</td>

                      <td>{{ row.iter }}</td>

                      <td>{{ row.ver }}</td>

                    </tr>

                  </tbody>

                </table>

              </div>


            </div>



            <div class="visual-area">

              <div class="minor-title">

                VLM Backbone Visual Comparison

              </div>



              <div

                class="example-content"

                tabindex="0"

                aria-label="VLM backbone visual comparison"

                @keydown="handleKeydown($event, previousBackbone, nextBackbone)"

              >

                <div class="example-caption-area">

                  <div class="example-heading-row">

                    <span class="example-label">

                      Example {{ currentBackbone.id }}

                    </span>



                    <span class="example-progress">

                      {{ String(backboneIndex + 1).padStart(2, '0') }}

                      /

                      {{ String(backboneGroups.length).padStart(2, '0') }}

                    </span>

                  </div>



                  <p class="example-caption">

                    “{{ currentBackbone.prompt }}”

                  </p>

                </div>



                <div class="image-navigation">

                  <button

                    type="button"

                    class="nav-arrow"

                    aria-label="Previous VLM backbone example"

                    @click="previousBackbone"

                  >

                    <el-icon><ArrowLeft /></el-icon>

                  </button>



                  <div class="image-row-viewport">

                    <div class="image-row image-row-4">

                      <figure

                        v-for="(image, imageIndex) in currentBackbone.images"

                        :key="`backbone-${currentBackbone.id}-${image.label}`"

                        class="comparison-figure"

                      >

                        <el-image

                          :src="image.src"

                          :preview-src-list="currentBackbone.images.map(item => item.src)"

                          :initial-index="imageIndex"

                          :alt="`${image.label} result for backbone example ${currentBackbone.id}`"

                          class="comparison-image"

                          fit="contain"

                          preview-teleported

                        >

                          <template #error>

                            <div class="image-error">

                              Unable to load

                            </div>

                          </template>

                        </el-image>



                        <figcaption class="image-label">

                          {{ image.label }}

                        </figcaption>

                      </figure>

                    </div>

                  </div>



                  <button

                    type="button"

                    class="nav-arrow"

                    aria-label="Next VLM backbone example"

                    @click="nextBackbone"

                  >

                    <el-icon><ArrowRight /></el-icon>

                  </button>

                </div>



                <div class="example-dots">

                  <button

                    v-for="(group, index) in backboneGroups"

                    :key="`backbone-dot-${group.id}`"

                    type="button"

                    class="example-dot"

                    :class="{ 'example-dot-active': index === backboneIndex }"

                    :aria-label="`Show VLM backbone example ${index + 1}`"

                    @click="backboneIndex = index"

                  />

                </div>

              </div>

            </div>

          </section>



          <!-- ================================================== -->

          <!-- Agentic Reasoning -->

          <!-- ================================================== -->

          <section class="ablation-group reasoning-panel">

            <div class="section-heading">

              <h2 class="section-heading-label">

                Impact of Agentic Reasoning

              </h2>

              <span class="section-heading-line" />

            </div>

            <p class="comparison-helper">
              We investigate the contributions of planning, geometric verification, visual-semantic verification, and self-evolving memory. Removing planning substantially degrades spatial organization, while disabling verification leads to geometric violations or semantic inconsistencies. Removing memory components further weakens layout quality and reasoning efficiency. These results demonstrate the complementary roles of the individual components in reliable scene composition.  
            </p>

            <div class="result-area">

              <div class="minor-title">

                Agentic Reasoning Component Ablation

              </div>



              <div class="table-scroll">

                <table class="ablation-table">

                  <thead>

                    <tr>

                      <th class="method-col">Method</th>

                      <th>Sem. ↑</th>

                      <th>Comp. ↑</th>

                      <th>Compl. ↑</th>

                      <th>Aes. ↑</th>

                      <th>Size ↑</th>

                      <th>Iter. ↓</th>

                      <th>Ver. ↑</th>

                    </tr>

                  </thead>



                  <tbody>

                    <tr

                      v-for="row in reasoningRows"

                      :key="row.method"

                      :class="{ 'best-row': row.best }"

                    >

                      <td class="method-col">{{ row.method }}</td>

                      <td>{{ row.sem }}</td>

                      <td>{{ row.comp }}</td>

                      <td>{{ row.compl }}</td>

                      <td>{{ row.aes }}</td>

                      <td>{{ row.size }}</td>

                      <td>{{ row.iter }}</td>

                      <td>{{ row.ver }}</td>

                    </tr>

                  </tbody>

                </table>

              </div>

            </div>



            <div class="visual-area">

              <div class="minor-title">

                Agentic Reasoning Ablation Examples

              </div>



              <div

                class="example-content"

                tabindex="0"

                aria-label="Agentic reasoning ablation examples"

                @keydown="handleKeydown($event, previousReasoning, nextReasoning)"

              >

                <div class="example-caption-area">

                  <div class="example-heading-row">

                    <span class="example-label">

                      Example {{ currentReasoning.id }}

                    </span>



                    <span class="example-progress">

                      {{ String(reasoningIndex + 1).padStart(2, '0') }}

                      /

                      {{ String(reasoningGroups.length).padStart(2, '0') }}

                    </span>

                  </div>



                  <p class="example-caption">

                    “{{ currentReasoning.prompt }}”

                  </p>

                </div>



                <div class="image-navigation">

                  <button

                    type="button"

                    class="nav-arrow"

                    aria-label="Previous reasoning example"

                    @click="previousReasoning"

                  >

                    <el-icon><ArrowLeft /></el-icon>

                  </button>



                  <div class="image-row-viewport">

                    <div class="image-row image-row-6">

                      <figure

                        v-for="(image, imageIndex) in currentReasoning.images"

                        :key="`reasoning-${currentReasoning.id}-${image.label}`"

                        class="comparison-figure"

                      >

                        <el-image

                          :src="image.src"

                          :preview-src-list="currentReasoning.images.map(item => item.src)"

                          :initial-index="imageIndex"

                          :alt="`${image.label} result for reasoning example ${currentReasoning.id}`"

                          class="comparison-image"

                          fit="contain"

                          preview-teleported

                        >

                          <template #error>

                            <div class="image-error">

                              Unable to load

                            </div>

                          </template>

                        </el-image>



                        <figcaption class="image-label">

                          {{ image.label }}

                        </figcaption>

                      </figure>

                    </div>

                  </div>



                  <button

                    type="button"

                    class="nav-arrow"

                    aria-label="Next reasoning example"

                    @click="nextReasoning"

                  >

                    <el-icon><ArrowRight /></el-icon>

                  </button>

                </div>



                <div class="example-dots">

                  <button

                    v-for="(group, index) in reasoningGroups"

                    :key="`reasoning-dot-${group.id}`"

                    type="button"

                    class="example-dot"

                    :class="{ 'example-dot-active': index === reasoningIndex }"

                    :aria-label="`Show reasoning example ${index + 1}`"

                    @click="reasoningIndex = index"

                  />

                </div>

              </div>

            </div>

          </section>



          <!-- ================================================== -->

          <!-- Structure-aware Refinement -->

          <!-- ================================================== -->

          <section class="ablation-group refinement-panel">

            <div class="section-heading">

              <h2 class="section-heading-label">

                Impact of Detail Refinement

              </h2>

              <span class="section-heading-line" />

            </div>

            <p class="comparison-helper">
              We investigate the effectiveness of curve contour resampling and progressive joint optimization in structure-aware refinement. Compared with point-based resampling, our contour-preserving approach maintains object geometry and provides a stable initialization for refinement. Furthermore, combining object-level and scene-level supervision through progressive optimization enhances sketch details while preserving object structures and spatial coherence.  
            </p>



            <!-- Contour Resampling -->

            <div class="visual-area refinement-visual-area resampling-panel">

              <div class="minor-title">

                Contour Resampling Examples

              </div>



              <div

                class="example-content"

                tabindex="0"

                aria-label="Contour resampling examples"

                @keydown="handleKeydown($event, previousResampling, nextResampling)"

              >

                <div class="example-caption-area">

                  <div class="example-heading-row">

                    <span class="example-label">

                      Example {{ currentResampling.id }}

                    </span>



                    <span class="example-progress">

                      {{ String(resamplingIndex + 1).padStart(2, '0') }}

                      /

                      {{ String(resamplingGroups.length).padStart(2, '0') }}

                    </span>

                  </div>



                  <p class="example-caption">

                    “{{ currentResampling.prompt }}”

                  </p>

                </div>



                <div class="image-navigation">

                  <button

                    type="button"

                    class="nav-arrow"

                    aria-label="Previous resampling example"

                    @click="previousResampling"

                  >

                    <el-icon><ArrowLeft /></el-icon>

                  </button>



                  <div class="image-row-viewport">

                    <div class="image-row image-row-4">

                      <figure

                        v-for="(image, imageIndex) in currentResampling.images"

                        :key="`resampling-${currentResampling.id}-${image.label}`"

                        class="comparison-figure"

                      >

                        <el-image

                          :src="image.src"

                          :preview-src-list="currentResampling.images.map(item => item.src)"

                          :initial-index="imageIndex"

                          :alt="`${image.label} for resampling example ${currentResampling.id}`"

                          class="comparison-image"

                          fit="contain"

                          preview-teleported

                        >

                          <template #error>

                            <div class="image-error">

                              Unable to load

                            </div>

                          </template>

                        </el-image>



                        <figcaption class="image-label">

                          {{ image.label }}

                        </figcaption>

                      </figure>

                    </div>

                  </div>



                  <button

                    type="button"

                    class="nav-arrow"

                    aria-label="Next resampling example"

                    @click="nextResampling"

                  >

                    <el-icon><ArrowRight /></el-icon>

                  </button>

                </div>



                <div class="example-dots">

                  <button

                    v-for="(group, index) in resamplingGroups"

                    :key="`resampling-dot-${group.id}`"

                    type="button"

                    class="example-dot"

                    :class="{ 'example-dot-active': index === resamplingIndex }"

                    :aria-label="`Show resampling example ${index + 1}`"

                    @click="resamplingIndex = index"

                  />

                </div>

              </div>

            </div>



            <!-- Joint Optimization -->

            <div class="visual-area refinement-visual-area optimization-panel">

              <div class="minor-title">

                Joint Optimization Examples

              </div>



              <div

                class="example-content"

                tabindex="0"

                aria-label="Joint optimization examples"

                @keydown="handleKeydown($event, previousOptimization, nextOptimization)"

              >

                <div class="example-caption-area">

                  <div class="example-heading-row">

                    <span class="example-label">

                      Example {{ currentOptimization.id }}

                    </span>



                    <span class="example-progress">

                      {{ String(optimizationIndex + 1).padStart(2, '0') }}

                      /

                      {{ String(optimizationGroups.length).padStart(2, '0') }}

                    </span>

                  </div>



                  <p class="example-caption">

                    “{{ currentOptimization.prompt }}”

                  </p>

                </div>



                <div class="image-navigation">

                  <button

                    type="button"

                    class="nav-arrow"

                    aria-label="Previous optimization example"

                    @click="previousOptimization"

                  >

                    <el-icon><ArrowLeft /></el-icon>

                  </button>



                  <div class="image-row-viewport">

                    <div class="image-row image-row-5">

                      <figure

                        v-for="(image, imageIndex) in currentOptimization.images"

                        :key="`optimization-${currentOptimization.id}-${image.label}`"

                        class="comparison-figure"

                      >

                        <el-image

                          :src="image.src"

                          :preview-src-list="currentOptimization.images.map(item => item.src)"

                          :initial-index="imageIndex"

                          :alt="`${image.label} result for optimization example ${currentOptimization.id}`"

                          class="comparison-image"

                          fit="contain"

                          preview-teleported

                        >

                          <template #error>

                            <div class="image-error">

                              Unable to load

                            </div>

                          </template>

                        </el-image>



                        <figcaption class="image-label">

                          {{ image.label }}

                        </figcaption>

                      </figure>

                    </div>

                  </div>



                  <button

                    type="button"

                    class="nav-arrow"

                    aria-label="Next optimization example"

                    @click="nextOptimization"

                  >

                    <el-icon><ArrowRight /></el-icon>

                  </button>

                </div>



                <div class="example-dots">

                  <button

                    v-for="(group, index) in optimizationGroups"

                    :key="`optimization-dot-${group.id}`"

                    type="button"

                    class="example-dot"

                    :class="{ 'example-dot-active': index === optimizationIndex }"

                    :aria-label="`Show optimization example ${index + 1}`"

                    @click="optimizationIndex = index"

                  />

                </div>

              </div>

            </div>

          </section>

        </div>

      </el-col>

    </el-row>

  </div>

</template>

<style scoped>
/* Ablation Study: shared SketchDirector academic-page styling */
.comparison-helper {
  margin: 0 auto 26px;
  color: #62666a;
  font-size: clamp(25px, 1.5vw, 19px);
  line-height: 1.75;
  text-align: left;
}
.ablation-layout-section {
  width: 100%;
  padding: 64px 0 80px;
  color: #191d20;
  background: #faf9f7;
  font-family: Inter, 'Helvetica Neue', Arial, sans-serif;
  box-sizing: border-box;
}
.ablation-layout-section :deep(.el-divider) {
  max-width: 1600px;
  margin: 0 auto 48px;
  border-color: #e7e5e2;
}
.ablation-column {
  width: 100%;
  max-width: 1600px;
  padding: 0 20px;
  margin: 0 auto;
  box-sizing: border-box;
  min-width: 0;
}
.ablation-highlight {
  width: 100%;
  max-width: 1600px;
  margin: 0 auto 58px;
  padding: 0;
  background: transparent;
  border: none;
  border-radius: 0;
  box-shadow: none;
  text-align: center;
}
.ablation-highlight::after, .ablation-kicker { display: none; }
.ablation-main-title {
  margin: 0 0 18px;
  color: #191d20;
  font-size: clamp(30px, 3vw, 42px);
  font-weight: 750;
  line-height: 1.2;
  letter-spacing: -.04em;
  text-align: center;
}
.ablation-main-description {
  max-width: 1600px;
  margin: 0 auto 22px;
  color: #62666a;
  font-size: clamp(25px, 1.5vw, 19px);
  font-weight: 400;
  line-height: 1.75;
  text-align: center;
}
.ablation-feature-chips {
  display: flex;
  align-items: center;
  justify-content: center;
  flex-wrap: wrap;
  gap: 9px;
  width: 100%;
}
.ablation-chip {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  padding: 8px 14px;
  min-height: 34px;
  background: #fff;
  border: 1px solid #dededc;
  border-radius: 999px;
  color: #51545a;
  font-size: 12px;
  font-weight: 600;
  line-height: 1.2;
  box-sizing: border-box;
}
.ablation-stack {
  display: flex;
  flex-direction: column;
  gap: 70px;
  width: 100%;
  margin: 0 auto;
}
.ablation-group {
  --panel-accent: #7769cb;
  --panel-accent-soft: rgba(119,105,203,.095);
  --panel-accent-text: #6557b2;
  width: 100%;
  min-width: 0;
  box-sizing: border-box;
}
.section-heading {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100%;
  margin: 0 0 30px;
}
.section-heading-label {
  margin: 0;
  padding: 0;
  background: none;
  border: 0;
  border-radius: 0;
  color: #7769cb;
  font-size: clamp(22px, 2.2vw, 30px);
  font-weight: 700;
  line-height: 1.35;
  letter-spacing: -.03em;
  text-align: center;
}
.section-heading-line { display: none; }
.minor-title {
  display: block;
  margin: 0 0 20px;
  color: #77797f;
  font-size: clamp(12px, 1.7vw, 24px);
  font-weight: 650;
  line-height: 1.4;
  text-align: center;
}
.minor-title::before { display: none; }
.result-area { width: 100%; margin-bottom: 45px; }
.visual-area { width: 100%; }
.refinement-visual-area + .refinement-visual-area {
  margin-top: 58px;
  padding-top: 0;
  border: 0;
}
.table-scroll {
  width: 100%;
  overflow-x: auto;
  -webkit-overflow-scrolling: touch;
  background: #fff;
  border: 1px solid #e4e4e4;
  border-radius: 9px;
  box-sizing: border-box;
}
.ablation-table {
  width: 100%;
  min-width: 790px;
  border-collapse: collapse;
  color: #30343a;
  font-size: 20px;
  font-variant-numeric: tabular-nums;
  text-align: center;
}
.ablation-table th, .ablation-table td {
  padding: 14px 12px;
  border-bottom: 1px solid #ebebeb;
  white-space: nowrap;
}
.ablation-table thead th {
  background: #f5f5f4;
  color: #33373b;
  font-size: 20px;
  font-weight: 700;
  border-bottom: 1px solid #e0e0de;
}
.ablation-table tbody tr:last-child td { border-bottom: 0; }
.ablation-table tbody tr:hover { background: #f8f8fc; }
.ablation-table .method-col {
  min-width: 165px;
  padding-left: 18px;
  text-align: left;
}
.ablation-table .best-row {
  color: #50458f;
  background: #f1effa;
  font-weight: 700;
}
.result-caption {
  margin: 14px 2px 0;
  color: #62666a;
  font-size: 25px;
  line-height: 1.65;
  text-align: left;
}
.result-caption strong { color: #30343a; font-weight: 700; }
.example-content {
  position: relative;
  width: 100%;
  min-width: 0;
  padding: 0;
  box-sizing: border-box;
  outline: none;
}
.example-content:focus-visible { outline: 2px solid #7769cb; outline-offset: 5px; }
.example-caption-area { margin-bottom: 12px; }
.example-heading-row {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 16px;
  margin-bottom: 7px;
}
.example-label {
  color: #2d3135;
  font-size: 25px;
  font-weight: 650;
  line-height: 1.4;
}
.example-label::before { display: none; }
.example-progress {
  flex-shrink: 0;
  padding: 5px 10px;
  border: 1px solid #e1e1e1;
  border-radius: 999px;
  background: #fff;
  color: #777;
  font-size: 25px;
  font-weight: 600;
  letter-spacing: .03em;
}
.example-caption {
  margin: 0 0 10px;
  color: #555a60;
  font-size: 25px;
  font-style: italic;
  font-weight: 400;
  line-height: 1.65;
  text-align: left;
}
.image-navigation { position: relative; width: 100%; }
.nav-arrow {
  position: absolute;
  top: 50%;
  z-index: 5;
  transform: translateY(-50%);
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
  box-shadow: 0 2px 12px rgba(0,0,0,.055);
  cursor: pointer;
  transition: background-color .18s, color .18s;
}
.nav-arrow:hover { color: #fff; background: #191d20; }
.nav-arrow:focus-visible { outline: 2px solid #7769cb; outline-offset: 2px; }
.nav-arrow:first-child { left: -64px; }
.nav-arrow:last-child { right: -64px; }
.image-row-viewport {
  width: 100%;
  min-width: 0;
  overflow-x: auto;
  overflow-y: hidden;
  scrollbar-width: thin;
}
.image-row-viewport::-webkit-scrollbar { height: 6px; }
.image-row-viewport::-webkit-scrollbar-thumb { background: #d1d1d1; border-radius: 999px; }
.image-row { display: grid; gap: 12px; width: 100%; min-width: 0; }
.image-row-4 { grid-template-columns: repeat(4, minmax(0, 1fr)); }
.image-row-5 { grid-template-columns: repeat(5, minmax(0, 1fr)); }
.image-row-6 { grid-template-columns: repeat(6, minmax(0, 1fr)); }
.comparison-figure {
  min-width: 0;
  margin: 0;
  overflow: hidden;
  background: #fff;
  border: 1px solid #e5e5e5;
  border-radius: 8px;
  box-sizing: border-box;
}
.comparison-image {
  display: block;
  width: 100%;
  aspect-ratio: 1 / 1;
  background: #fff;
  cursor: zoom-in;
}
.comparison-image :deep(.el-image__inner) {
  display: block;
  width: 100%;
  height: 100%;
  padding: 5px;
  box-sizing: border-box;
  transition: transform .2s ease;
}
.comparison-figure:hover .comparison-image :deep(.el-image__inner) { transform: scale(1.025); }
.image-label {
  display: flex;
  align-items: center;
  justify-content: center;
  min-height: 38px;
  padding: 7px 5px;
  overflow: hidden;
  background: #f7f7f7;
  border-top: 1px solid #ebebeb;
  color: #50545a;
  font-size: 20px;
  font-weight: 600;
  line-height: 1.3;
  text-align: center;
  box-sizing: border-box;
}
.image-error {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 100%;
  height: 100%;
  padding: 10px;
  background: #f6f6f6;
  color: #777;
  font-size: 13px;
  text-align: center;
  box-sizing: border-box;
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
  background: #cecece;
  border: 0;
  border-radius: 50%;
  cursor: pointer;
  transition: background-color .2s, transform .2s;
}
.example-dot:hover { background: #777; }
.example-dot-active { background: #7769cb; transform: scale(1.2); }
.example-dot:focus-visible { outline: 2px solid #7769cb; outline-offset: 3px; }
@media (max-width: 1240px) {
  .image-navigation { padding: 0 54px; box-sizing: border-box; }
  .nav-arrow:first-child { left: 0; }
  .nav-arrow:last-child { right: 0; }
}
@media (max-width: 767px) {
  .ablation-layout-section { padding: 40px 0 55px; }
  .ablation-layout-section :deep(.el-divider) { margin-bottom: 32px; }
  .ablation-column { padding: 0 16px; }
  .ablation-highlight { margin-bottom: 44px; }
  .ablation-main-title { font-size: 30px; }
  .ablation-main-description { font-size: 16px; }
  .ablation-feature-chips { gap: 7px; }
  .ablation-chip { font-size: 11px; padding: 7px 11px; }
  .ablation-stack { gap: 50px; }
  .section-heading { margin-bottom: 24px; }
  .section-heading-label { font-size: 22px; }
  .minor-title { font-size: 19px; margin-bottom: 16px; }
  .ablation-table { font-size: 12px; }
  .example-label { font-size: 15px; }
  .example-caption { font-size: 14px; }
  .image-navigation { padding: 0 32px; }
  .nav-arrow { width: 28px; height: 28px; }
  .image-row-4 { min-width: 650px; }
  .image-row-5 { min-width: 800px; }
  .image-row-6 { min-width: 950px; }
  .comparison-desc, .comparison-helper, .userstudy-desc { font-size: 16px; line-height: 1.7; }
  
}
</style>
