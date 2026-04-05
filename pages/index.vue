<script setup lang="ts">
const { data: articles } = await useAsyncData("articles-index", () =>
  queryContent("/articles")
    .only(["_path", "title", "description", "date"])
    .sort({ date: -1 })
    .find()
);

useSeoMeta({
  title: "文章目录",
  description:
    "以阅读体验为先的文章站点：清晰排版、深浅色适配、面向移动与桌面端。",
  ogTitle: "文章目录",
  ogDescription:
    "以阅读体验为先的文章站点：清晰排版、深浅色适配、面向移动与桌面端。",
});

const config = useRuntimeConfig();
const base = String(config.public.siteUrl || "https://ik97.com").replace(
  /\/$/,
  ""
);

useHead({
  link: [
    {
      rel: "canonical",
      href: `${base}/`,
    },
  ],
});
</script>

<template>
  <div>
    <!-- <h1 class="page-title">文章目录</h1>
    <p class="page-sub">
      文章以 Markdown 存放在
      <code>content/articles</code>，构建时生成静态页面，便于收录与加速首屏。
    </p> -->

    <div v-if="!articles?.length" class="page-sub">暂无文章，请在 content 目录添加 .md 文件。</div>

    <div v-else class="card-grid">
      <NuxtLink
        v-for="item in articles"
        :key="item._path"
        :to="item._path"
        class="article-card"
      >
        <h2 class="article-card__title">{{ item.title }}</h2>
        <div class="article-card__meta">
          <time v-if="item.date" :datetime="item.date">{{ item.date }}</time>
          <span v-if="item.description"> · {{ item.description }}</span>
        </div>
      </NuxtLink>
    </div>
  </div>
</template>
