<template>
  <section class="hero" aria-label="Hero">
    <!-- 背景球体 -->
    <BannerSphere />

    <!-- 背景环境光晕 -->
    <div class="ambient-glow" aria-hidden="true"></div>

    <!-- 右上角提示卡片 -->
    <div class="tip-card" aria-hidden="true">
      <div class="tip-eyebrow">
        <span class="tip-dot" aria-hidden="true"></span>
        <span>AI CLASSES · LIMITED OFFER</span>
      </div>
      <span class="tip-title">Free website included</span>
      <span class="tip-link">See the offer →</span>
    </div>

    <!-- 主内容：标题 + 副标题 -->
    <div class="hero-content">
      <h1 class="hero-title">
        <span class="line">AI works for you.</span>
        <span class="line accent">Not the opposite.</span>
      </h1>

      <p class="hero-subtitle">
        All the AI your business needs, without the hassle.<br />
        No subscription. Pay only for what you use.<br />
        Made in Montréal.
      </p>
    </div>

    <!-- 左下角 Product Hunt 徽章 -->
    <a class="ph-badge" href="https://www.producthunt.com" target="_blank" rel="noopener">
      <span class="ph-logo" aria-hidden="true">
        <svg viewBox="0 0 40 40" fill="none" xmlns="http://www.w3.org/2000/svg">
          <circle cx="20" cy="20" r="20" fill="#FF6154"/>
          <path d="M13 11h8.5a4.5 4.5 0 0 1 0 9H19v9h-6V11Z" fill="white"/>
          <path d="M19 20h5l-7 10v-6h-2l4-4Z" fill="white"/>
        </svg>
      </span>
      <span class="ph-content">
        <span class="ph-eyebrow">FEATURED ON</span>
        <span class="ph-title">Product Hunt <strong>304</strong></span>
      </span>
    </a>

    <!-- 右下角 CTA -->
    <a class="cta-btn" href="https://auth.crewdle.ai/signup" target="_blank" rel="noopener">
      <span>Start for free</span>
      <span class="cta-arrow">→</span>
    </a>
  </section>
</template>

<script setup lang="ts">
/**
 * 复刻 Crewdle 首屏 Banner：
 * - 深色背景 + 中央 3D 光泽球体
 * - 左侧大标题，第二行使用品牌橙红色
 * - 左下角 Product Hunt 官方风格徽章
 * - 右下角 Start for free CTA
 * - 右上角促销提示卡片
 */
</script>

<style scoped>
.hero {
  position: relative;
  min-height: 100vh;
  display: flex;
  align-items: center;
  justify-content: flex-start;
  padding: 0 64px;
  overflow: hidden;
}

.ambient-glow {
  position: absolute;
  top: 50%;
  left: 50%;
  width: 160vw;
  height: 160vw;
  transform: translate(-50%, -50%);
  border-radius: 50%;
  background: radial-gradient(
    circle,
    rgba(255, 105, 38, 0.20) 0%,
    rgba(255, 105, 38, 0.07) 28%,
    transparent 60%
  );
  pointer-events: none;
  z-index: 0;
}

.hero-content {
  position: relative;
  z-index: 10;
  max-width: 760px;
  padding-top: 80px;
}

.hero-title {
  font-family: 'TroisMille', Inter, system-ui, sans-serif;
  font-size: clamp(48px, 8.6vw, 124px);
  font-weight: 400;
  line-height: 0.96;
  letter-spacing: -0.015em;
  color: rgb(241, 235, 226);
  margin-bottom: 32px;
}

.hero-title .line {
  display: block;
  white-space: nowrap;
}

.hero-title .accent {
  color: rgb(255, 83, 34);
}

.hero-subtitle {
  font-size: 17px;
  line-height: 1.6;
  color: rgb(165, 152, 142);
  max-width: 420px;
}

.cta-btn {
  position: absolute;
  right: 64px;
  bottom: 64px;
  z-index: 20;
  display: inline-flex;
  align-items: center;
  gap: 10px;
  font-size: 15px;
  font-weight: 500;
  color: rgb(255, 255, 255);
  background-color: rgb(255, 83, 34);
  text-decoration: none;
  padding: 16px 28px;
  border-radius: 999px;
  transition: transform 0.2s ease, background-color 0.2s ease;
}

.cta-btn:hover {
  background-color: rgb(255, 100, 50);
  transform: translateY(-1px);
}

.cta-arrow {
  font-size: 16px;
}

.ph-badge {
  position: absolute;
  left: 64px;
  bottom: 64px;
  z-index: 20;
  display: inline-flex;
  align-items: center;
  gap: 10px;
  text-decoration: none;
  padding: 10px 14px;
  border-radius: 8px;
  background-color: rgb(255, 255, 255);
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.2);
  transition: transform 0.2s ease;
}

.ph-badge:hover {
  transform: translateY(-1px);
}

.ph-logo {
  width: 28px;
  height: 28px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.ph-logo svg {
  width: 100%;
  height: 100%;
}

.ph-content {
  display: flex;
  flex-direction: column;
  line-height: 1.2;
}

.ph-eyebrow {
  font-size: 9px;
  font-weight: 600;
  letter-spacing: 0.08em;
  color: rgb(120, 110, 105);
  text-transform: uppercase;
}

.ph-title {
  font-size: 13px;
  font-weight: 500;
  color: rgb(34, 29, 26);
}

.ph-title strong {
  font-weight: 700;
  color: rgb(255, 83, 34);
}

.tip-card {
  position: absolute;
  top: 120px;
  right: 64px;
  z-index: 20;
  display: flex;
  flex-direction: column;
  gap: 4px;
  padding: 16px 20px;
  border-radius: 16px;
  background-color: rgb(255, 255, 255);
  color: rgb(34, 29, 26);
  box-shadow: 0 12px 40px rgba(0, 0, 0, 0.25);
  font-family: 'TroisMille', Inter, system-ui, sans-serif;
  min-width: 240px;
}

.tip-eyebrow {
  display: inline-flex;
  align-items: center;
  gap: 6px;
  font-size: 10px;
  font-weight: 600;
  letter-spacing: 0.08em;
  color: rgb(100, 70, 180);
  text-transform: uppercase;
}

.tip-dot {
  width: 6px;
  height: 6px;
  border-radius: 50%;
  background-color: rgb(255, 83, 34);
}

.tip-title {
  font-size: 17px;
  font-weight: 500;
  line-height: 1.15;
  letter-spacing: -0.01em;
  color: rgb(34, 29, 26);
  white-space: nowrap;
}

.tip-link {
  font-size: 12px;
  font-weight: 500;
  color: rgb(255, 83, 34);
  margin-top: 4px;
}

@media (max-width: 1024px) {
  .hero {
    padding: 0 32px;
  }

  .tip-card {
    right: 24px;
    top: 100px;
  }

  .cta-btn {
    right: 32px;
    bottom: 48px;
  }

  .ph-badge {
    left: 32px;
    bottom: 48px;
  }
}

@media (max-width: 768px) {
  .hero {
    padding: 0 20px;
    align-items: flex-end;
    padding-bottom: 160px;
  }

  .hero-content {
    padding-top: 0;
  }

  .hero-subtitle {
    font-size: 15px;
  }

  .tip-card {
    display: none;
  }

  .cta-btn {
    right: 20px;
    bottom: 90px;
  }

  .ph-badge {
    left: 20px;
    bottom: 90px;
  }
}
</style>
