<script setup lang="ts">
const route = useRoute();
const raw = route.params.slug as string[] | string;
const segments = Array.isArray(raw) ? raw : [raw];
if (segments[0] !== "articles") {
  throw createError({ statusCode: 404, statusMessage: "页面不存在" });
}

const path = `/${segments.join("/")}`;

const { data: doc } = await useAsyncData(`content-${path}`, () =>
  queryContent(path).findOne()
);

if (!doc.value) {
  throw createError({ statusCode: 404, statusMessage: "文章不存在" });
}

const title = doc.value.title || "未命名文章";
const description =
  doc.value.description ||
  (typeof doc.value.excerpt === "string" ? doc.value.excerpt : "") ||
  `${title} — Booksite`;

const config = useRuntimeConfig();
const base = String(config.public.siteUrl || "http://localhost:3000").replace(
  /\/$/,
  ""
);
const canonical = `${base}${route.path}`;

useSeoMeta({
  title,
  description,
  ogTitle: title,
  ogDescription: description,
  ogType: "article",
  twitterCard: "summary",
});

if (doc.value.date) {
  useSeoMeta({ articlePublishedTime: String(doc.value.date) });
}

useHead({
  link: [{ rel: "canonical", href: canonical }],
  script: [
    {
      type: "application/ld+json",
      innerHTML: JSON.stringify({
        "@context": "https://schema.org",
        "@type": "Article",
        headline: title,
        description,
        datePublished: doc.value.date || undefined,
        mainEntityOfPage: canonical,
      }),
    },
  ],
});
</script>

<template>
  <article class="article-article" itemscope itemtype="https://schema.org/Article">
    <NuxtLink class="back-link" to="/">← 返回目录</NuxtLink>

    <header class="article-hero">
      <h1 itemprop="headline">{{ doc.title }}</h1>
      <div class="article-meta">
        <time v-if="doc.date" itemprop="datePublished" :datetime="String(doc.date)">{{
          doc.date
        }}</time>
        <span v-if="doc.readingMinutes">约 {{ doc.readingMinutes }} 分钟阅读</span>
      </div>
    </header>

    <div class="prose" itemprop="articleBody">
      <ContentRenderer v-if="doc" :value="doc" />
    </div>
  </article>
</template>
