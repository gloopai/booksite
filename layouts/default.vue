<template>
  <div class="reading-shell">
    <header class="site-header">
      <div class="site-header__inner">
        <NuxtLink class="brand" to="/">Booksite</NuxtLink>
        <nav class="site-nav" aria-label="主导航">
          <NuxtLink to="/">目录</NuxtLink>
        </nav>
      </div>
    </header>
    <main class="site-main">
      <slot />
    </main>
    <footer class="site-footer">
      <p>本站内容为示例与合法来源文稿，请替换为你拥有权利或已获授权的内容。</p>
    </footer>
  </div>
</template>
