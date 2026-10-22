<script lang="ts" setup>
import {
  Document,
  Files,
  Link,
} from '@element-plus/icons-vue'

// 项目名称
const projectName = 'SketchDirector'

// 论文标题
const titleLead = 'Complex Sketching via'
const titleHighlight = 'Agentic Layout Reasoning'
const titleEnd = 'and Detail Refinement'

// 标题颜色
const titleColor = '#191d20'

// 按钮颜色
const buttonColor = '#1d2327'

// 作者清单
const authors = [
  {
    name: 'Yiqi Liang',
    homepage: '',
    icon: '',
    addressFlag: '',
  },
  { name: 'Xiaodong Cun*', homepage: '', icon: '', addressFlag: '' },
]

// 地址清单
const addresses: Array<{
  name: string
  homepage?: string
  icon?: string
  addressFlag?: string
}> = [
  {
    name: 'GVC Lab, Great Bay University',
    homepage: 'https://gvclab.github.io/',
    icon: '',
    addressFlag: '',
  },
]

// 共一和通讯作者提示
const correspondingAuthorText = '*Corresponding author.'


// 强调内容
const emphases: string[] = []

// 引导资料链接
const buttons = [
  {
    disabled: false,
    name: 'Paper',
    link: './_AAAI_27__SketchDirector.pdf',
    component: Document,
  },
  {
    disabled: true,
    name: 'Code',
    link: '',
    component: Link,
  },
  {
    disabled: false,
    name: 'Dataset',
    link: 'https://huggingface.co/datasets/yiki77/MCSketch8K',
    component: Link,
  },
]
</script>

<template>
  <section class="title-section">
    <div class="title-content">
      <!-- 单一连续标题：项目名紫色，论文标题黑色，按宽度自然换行 -->
      <h1 class="paper-heading">
        <span class="heading-line"><span class="project-name">{{ projectName }}:</span> {{ titleLead }}</span>
        <span class="heading-line">{{ titleHighlight }} {{ titleEnd }}</span>
      </h1>


      <!-- 作者名单 -->
      <div class="author-list">
        <a
          v-for="author in authors"
          :key="author.name"
          class="author-link"
          :class="{
            'author-link-disabled': !author.homepage,
          }"
          :href="author.homepage || undefined"
          :target="author.homepage ? '_blank' : undefined"
          :rel="
            author.homepage
              ? 'noopener noreferrer'
              : undefined
          "
        >
          <el-avatar
            v-if="author.icon"
            :size="34"
            :src="author.icon"
            class="author-avatar"
          />

          <span class="author-name">
            {{ author.name }}

            <sup
              v-if="author.addressFlag"
              class="name-sup"
            >
              {{ author.addressFlag }}
            </sup>
          </span>
        </a>
      </div>

      <!-- 地址名单 -->
      <div
        v-if="addresses.length"
        class="address-list"
      >
        <a
          v-for="address in addresses"
          :key="address.name"
          class="address-link"
          :href="address.homepage || undefined"
          :target="
            address.homepage
              ? '_blank'
              : undefined
          "
          :rel="
            address.homepage
              ? 'noopener noreferrer'
              : undefined
          "
        >
          <el-avatar
            v-if="address.icon"
            :size="30"
            :src="address.icon"
            class="address-avatar"
          />

          <span class="address-name">
            <sup
              v-if="address.addressFlag"
              class="address-sup"
            >
              {{ address.addressFlag }}
            </sup>

            {{ address.name }}
          </span>
        </a>
      </div>

      <!-- 共一和通讯作者提示 -->
      <div
        v-if="correspondingAuthorText"
        class="corresponding-author"
      >
        {{ correspondingAuthorText }}
      </div>

      <!-- 强调内容 -->
      <div
        v-for="emphasis in emphases"
        :key="emphasis"
        class="emphasis"
      >
        {{ emphasis }}
      </div>

      <!-- 引导按钮 -->
      <div class="guidance-row">
        <template
          v-for="button in buttons"
          :key="button.name"
        >
          <a
            v-if="!button.disabled"
            :href="button.link"
            target="_blank"
            rel="noopener noreferrer"
            class="guidance-link"
          >
            <el-button
              class="guidance-button"
              size="large"
              :color="buttonColor"
              round
            >
              <el-icon :size="18">
                <component :is="button.component" />
              </el-icon>

              <span class="button-text">
                {{ button.name }}
              </span>
            </el-button>
          </a>

          <span
            v-else
            class="guidance-link guidance-link-disabled"
          >
            <el-button
              class="guidance-button"
              size="large"
              :color="buttonColor"
              disabled
              round
            >
              <el-icon :size="18">
                <component :is="button.component" />
              </el-icon>

              <span class="button-text">
                {{ button.name }}
              </span>
            </el-button>
          </span>
        </template>
      </div>

      <!-- TL;DR -->
     <section class="overview-card">

        <h2 class="overview-title">
          Given a scene description, SketchDirector generates editable multi-concept scene sketches by composing individual vector assets through agentic layout reasoning and progressively refining their details while preserving object structures and spatial relationships.
          
        </h2>

      </section>
    </div>
  </section>
</template>

<style scoped>
.overview-card { max-width: 1250px; margin: 65px auto 0px; padding: 0 0 0px; text-align: center; background: transparent; box-shadow: none;
}
.overview-title { margin:0; color:#202427; font-size:clamp(20px,2.1vw,30px); line-height:1.45; font-weight:600; letter-spacing:-.035em; }
.title-section {
  width: 100%;
  background: #faf9f7;
  padding: 28px 22px 48px;
  box-sizing: border-box;
}
.title-content { max-width: 1200px; margin: 0 auto; text-align: center; }
.paper-heading {
  max-width: 1160px;
  margin: 0 auto;
  font-family: Inter, 'Helvetica Neue', Arial, sans-serif;
  font-size: clamp(28px, 3.4vw, 58px);
  font-weight: 600;
  line-height: 1.22;
  letter-spacing: -0.045em;
  color: #191d20;
  text-align: center;
  overflow-wrap: normal;
  text-wrap: initial;
}
.heading-line { display: block; white-space: nowrap; }
@media (max-width: 850px) {
  .paper-heading { font-size: clamp(24px, 4.3vw, 36px); }
  .heading-line { white-space: normal; }
}
.project-name {
  color: #7769cb;
  font-weight: 800;
  white-space: nowrap;
}
.heading-rest { color: #191d20; }
.hero-description {
  max-width: 920px;
  margin: 26px auto 30px;
  font: 400 clamp(16px, 1.7vw, 20px)/1.85 Inter, Arial, sans-serif;
  color: #757a7d;
  text-wrap: balance;
}
.hero-description strong { color: #33383b; font-weight: 700; }
.author-list, .address-list {
  display: flex;
  align-items: center;
  justify-content: center;
  flex-wrap: wrap;
  gap: 12px 24px;
}
.author-list { margin: 28px auto 35px;}
.author-link, .address-link { color: #202428; text-decoration: none; }
.author-link:not(:last-child)::after { content: '·'; position: relative; left: 13px; color: #c1c1c1; }
.author-link:hover, .address-link:hover { color: #7769cb; }
.author-link-disabled { cursor: default; }
.author-link-disabled:hover { color: #202428; }
.author-name { font-size: 30px; font-weight: 600; border-bottom: 1px solid #bdbdbd; padding-bottom: 3px; }
.address-name { font-size: 25px; color: #555; }
.name-sup, .address-sup { margin-left: 2px; font-size: .7em; color: #777; }
.author-avatar, .address-avatar { margin-right: 7px; }
.corresponding-author { margin: 12px 0; color: #777; font-size: 14px; }
.emphasis { margin: 9px 0; font-weight: 700; }
.guidance-row { display: flex; justify-content: center; align-items: center; flex-wrap: wrap; gap: 13px; }
.guidance-link { display: inline-flex; text-decoration: none; }
.guidance-button {
  height: 56px;
  min-width: 136px;
  padding: 0 23px;
  border-radius: 12px !important;
  box-shadow: none;
  transition: transform .2s ease, box-shadow .2s ease;
}
.guidance-link:not(.guidance-link-disabled) .guidance-button:hover {
  transform: translateY(-2px);
  box-shadow: 0 6px 18px rgba(0,0,0,.13);
}
.guidance-link-disabled { cursor: not-allowed; }
.guidance-button:disabled { background: #fff !important; border: 1px solid #dedede !important; color: #252a2d !important; opacity: .8; }
.button-text { margin-left: 7px; font-size: 20px; font-weight: 650; }
@media (max-width: 800px) {
  .title-section { padding: 40px 22px; }
  .paper-heading { font-size: clamp(28px, 5vw, 42px); }
  .hero-description { margin: 22px auto 27px; line-height: 1.65; }
}
@media (max-width: 480px) {
  .title-section { padding: 36px 14px; }
  .paper-heading { font-size: clamp(25px, 7vw, 33px); line-height: 1.3; }
  .guidance-row { gap: 9px; }
  .guidance-button { min-width: 105px; height: 47px; padding: 0 13px; }
  .button-text { font-size: 14px; }
}
</style>
