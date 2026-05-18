<template>
  <router-view />
</template>

<script setup>
import { onMounted, onUnmounted } from 'vue'

function applyTheme() {
  const h = new Date().getHours()
  document.documentElement.setAttribute('data-theme', (h >= 6 && h < 18) ? 'light' : 'dark')
}

let themeInterval
onMounted(() => {
  applyTheme()
  themeInterval = setInterval(applyTheme, 60000)
})
onUnmounted(() => clearInterval(themeInterval))
</script>

<style>
/* ── Theme tokens ── */
:root {
  --bg:               #07090F;
  --bg-alt:           #0D1117;
  --surface:          rgba(13, 17, 23, 0.72);
  --surface-solid:    #0D1117;
  --text:             #F1F5F9;
  --text-muted:       #64748B;
  --text-subtle:      #94A3B8;
  --border:           rgba(139, 92, 246, 0.28);
  --border-subtle:    rgba(255, 255, 255, 0.08);
  --primary:          #8B5CF6;
  --primary-hover:    #7C3AED;
  --accent:           #22D3EE;
  --card-bg:          rgba(13, 17, 23, 0.55);
  --blob-1:           rgba(120, 60, 255, 0.55);
  --blob-2:           rgba(0, 210, 220, 0.35);
  --blob-3:           rgba(220, 80, 180, 0.28);
  --nav-bg:           rgba(7, 9, 15, 0.82);
  --input-bg:         rgba(7, 9, 15, 0.55);
  --input-text:       #CBD5E1;
  --input-placeholder:#2D3748;
  --scroll-track:     #0D1117;
  --scroll-thumb:     #1E293B;
  --badge-bg:         rgba(139, 92, 246, 0.10);
  --badge-border:     rgba(139, 92, 246, 0.45);
  --badge-text:       #A78BFA;
  --drop-shadow:      rgba(139, 92, 246, 0.30);
  --btn-grad-from:    #7C3AED;
  --btn-grad-to:      #0891B2;
}

[data-theme="light"] {
  --bg:               #F0EBFF;
  --bg-alt:           #E8E0FF;
  --surface:          rgba(255, 255, 255, 0.72);
  --surface-solid:    #FFFFFF;
  --text:             #0F172A;
  --text-muted:       #475569;
  --text-subtle:      #64748B;
  --border:           rgba(124, 58, 237, 0.22);
  --border-subtle:    rgba(0, 0, 0, 0.07);
  --primary:          #7C3AED;
  --primary-hover:    #6D28D9;
  --accent:           #0891B2;
  --card-bg:          rgba(255, 255, 255, 0.60);
  --blob-1:           rgba(139, 92, 246, 0.30);
  --blob-2:           rgba(8, 145, 178, 0.22);
  --blob-3:           rgba(236, 72, 153, 0.18);
  --nav-bg:           rgba(240, 235, 255, 0.88);
  --input-bg:         rgba(255, 255, 255, 0.80);
  --input-text:       #1E293B;
  --input-placeholder:#94A3B8;
  --scroll-track:     #EDE9FE;
  --scroll-thumb:     #C4B5FD;
  --badge-bg:         rgba(124, 58, 237, 0.09);
  --badge-border:     rgba(124, 58, 237, 0.35);
  --badge-text:       #7C3AED;
  --drop-shadow:      rgba(124, 58, 237, 0.20);
  --btn-grad-from:    #7C3AED;
  --btn-grad-to:      #0891B2;
}

/* ── Global reset ── */
*, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

html {
  scroll-behavior: smooth;
  transition: background-color 0.6s ease, color 0.6s ease;
}

body {
  background-color: var(--bg);
  color: var(--text);
  transition: background-color 0.6s ease, color 0.6s ease;
}

#app {
  font-family: 'Noto Sans', 'Noto Sans SC', system-ui, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: var(--text);
  background-color: transparent;
  min-height: 100vh;
  transition: color 0.6s ease;
}

::-webkit-scrollbar { width: 6px; height: 6px; }
::-webkit-scrollbar-track { background: var(--scroll-track); }
::-webkit-scrollbar-thumb { background: var(--scroll-thumb); border-radius: 4px; }
::-webkit-scrollbar-thumb:hover { background: var(--primary); }

button { font-family: inherit; cursor: pointer; }
a { color: inherit; }

/* ── Shared drop/blob effect (used globally) ── */
.drop-bg {
  position: fixed;
  inset: 0;
  pointer-events: none;
  z-index: 0;
}
.drop {
  position: absolute;
  border-radius: 50%;
  filter: blur(48px);
  animation: drop-float 18s ease-in-out infinite alternate;
  will-change: transform;
}
@keyframes drop-float {
  0%   { transform: translate(0, 0) scale(1); }
  33%  { transform: translate(30px, -40px) scale(1.08); }
  66%  { transform: translate(-25px, 25px) scale(0.92); }
  100% { transform: translate(15px, -15px) scale(1.04); }
}

/* ── Shared glass panel ── */
.glass {
  background: var(--surface);
  border: 1px solid var(--border);
  backdrop-filter: blur(24px);
  -webkit-backdrop-filter: blur(24px);
}
</style>
