<script>
import Title from './sections/Title.vue'
import Method from './sections/Method.vue'
import BibTeX from './sections/BibTeX.vue'
import Comparison from './sections/Comparison.vue'
import Layouts from './sections/Layouts.vue'
import Ablation from './sections/Ablation.vue'
import Limitations from './sections/Limitations.vue'
import Intro from './sections/Intro.vue'

export default {
  components: {
    Title,
    Intro,
    Method,
    BibTeX,
    Comparison,
    Layouts,
    Ablation,
    Limitations
  }
}
</script>

<template>
  <main id="top">

    <!-- Overview -->
    <div id="overview">
      <Title />
      <Intro />
    </div>

    <!-- Method -->
    <section id="method">
      <Method />
    </section>

    <!-- Results -->
    <section id="results">
      <Comparison />
      <Layouts />
      <Ablation />
      <Limitations />
    </section>

    <!-- Dataset -->
    <section id="dataset">
      <BibTeX />
    </section>

  </main>
</template>

<style>
html {
  scroll-behavior: smooth;
}

#overview,
#method,
#results,
#dataset {
  scroll-margin-top: 24px;
}
</style>
