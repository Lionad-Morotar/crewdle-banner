<template>
  <header class="app-header">
    <!-- 品牌 Logo -->
    <a href="/" class="logo" aria-label="Crewdle AI home">
      CREWDLE
    </a>

    <!-- 主导航 -->
    <nav class="main-nav" aria-label="Primary">
      <button class="nav-item has-chevron" type="button">
        Products
        <svg class="chevron" aria-hidden="true" width="10" height="6" viewBox="0 0 10 6" fill="none">
          <path d="M1 1L5 5L9 1" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
        </svg>
      </button>
      <a class="nav-item" href="#services">Services</a>
      <a class="nav-item" href="#pricing">Pricing</a>
      <a class="nav-item" href="#docs">Docs</a>
      <a class="nav-item" href="#blog">Blog</a>
    </nav>

    <!-- 右侧操作 -->
    <div class="actions">
      <button class="lang-switch" type="button" aria-label="Switch language">FR</button>
      <a class="signin-btn" href="#signin">Sign in</a>
    </div>
  </header>
</template>

<script setup lang="ts">
// 顶部导航：仅复刻 Banner 区域的视觉与结构，链接为占位符
</script>

<style scoped>
.app-header {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  z-index: 50;
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 24px 32px;
  color: rgb(241, 235, 226);
}

.logo {
  font-size: 13px;
  font-weight: 600;
  letter-spacing: 0.12em;
  color: inherit;
  text-decoration: none;
  font-family: Inter, system-ui, sans-serif;
}

.main-nav {
  display: flex;
  align-items: center;
  gap: 22px;
  position: absolute;
  left: 50%;
  transform: translateX(-50%);
}

.actions {
  display: flex;
  align-items: center;
  gap: 16px;
}

.nav-item {
  font-size: 14px;
  font-weight: 500;
  color: rgb(241, 235, 226);
  text-decoration: none;
  background: none;
  border: none;
  cursor: pointer;
  padding: 0;
  font-family: inherit;
  opacity: 0.95;
  transition: opacity 0.2s ease;
}

.nav-item:hover {
  opacity: 1;
}

.nav-item.has-chevron {
  display: inline-flex;
  align-items: center;
  gap: 5px;
}

.chevron {
  opacity: 0.7;
  margin-top: 1px;
}

.lang-switch {
  font-size: 11px;
  font-weight: 600;
  color: rgb(241, 235, 226);
  background: transparent;
  border: 1px solid rgba(255, 255, 255, 0.25);
  border-radius: 999px;
  padding: 4px 9px;
  cursor: pointer;
  font-family: inherit;
  transition: border-color 0.2s ease;
}

.lang-switch:hover {
  border-color: rgba(255, 255, 255, 0.45);
}

.signin-btn {
  font-size: 14px;
  font-weight: 500;
  color: rgb(241, 235, 226);
  background-color: transparent;
  text-decoration: none;
  padding: 7px 16px;
  border-radius: 999px;
  border: 1px solid rgba(255, 255, 255, 0.45);
  transition: border-color 0.2s ease, background-color 0.2s ease;
}

.signin-btn:hover {
  border-color: rgba(255, 255, 255, 0.65);
  background-color: rgba(255, 255, 255, 0.05);
}

.signup-btn {
  display: none;
}

@media (max-width: 900px) {
  .main-nav {
    display: none;
  }

  .app-header {
    padding: 20px 20px;
  }
}
</style>
