<template>
  <div class="home">

    <!-- ── Animated drop background ── -->
    <div class="drop-bg" aria-hidden="true">
      <div class="drop drop-1"></div>
      <div class="drop drop-2"></div>
      <div class="drop drop-3"></div>
    </div>

    <!-- ── Navbar ── -->
    <nav class="navbar glass">
      <div class="nav-brand" @click="scrollToTop" role="button" tabindex="0">
        <svg class="logo-svg" viewBox="0 0 44 44" fill="none">
          <defs>
            <linearGradient id="navGrad" x1="0" y1="0" x2="44" y2="44" gradientUnits="userSpaceOnUse">
              <stop offset="0%" stop-color="#8B5CF6"/>
              <stop offset="100%" stop-color="#22D3EE"/>
            </linearGradient>
          </defs>
          <path d="M22 2 L38 11.5 L38 32.5 L22 42 L6 32.5 L6 11.5 Z" stroke="url(#navGrad)" stroke-width="1.5" fill="none" stroke-linejoin="round"/>
          <path d="M22 10 L30 22 L22 34 L14 22 Z" stroke="url(#navGrad)" stroke-width="1.5" fill="rgba(139,92,246,0.1)" stroke-linejoin="round"/>
          <circle cx="22" cy="22" r="3.5" fill="url(#navGrad)"/>
          <circle cx="22" cy="22" r="6.5" stroke="url(#navGrad)" stroke-width="0.7" stroke-dasharray="2 3" fill="none"/>
        </svg>
        <span class="brand-name">CHANAKYA</span>
      </div>
      <div class="nav-actions">
        <span class="theme-hint">{{ themeLabel }}</span>
        <button class="btn-ghost" @click="showAuth = true; authMode = 'login'">Sign in</button>
        <button class="btn-primary" @click="showAuth = true; authMode = 'signup'">Get started free →</button>
      </div>
    </nav>

    <!-- ── Hero ── -->
    <section class="hero">
      <div class="hero-badge">
        <span class="badge-pulse"></span>
        Strategic Intelligence Engine &nbsp;·&nbsp; v0.1 Preview
      </div>

      <h1 class="hero-title">
        See Tomorrow,<br>
        <span class="grad-text">Today.</span>
      </h1>

      <p class="hero-desc">
        Describe any scenario in plain language. Chanakya builds a living world of AI personas,
        runs the simulation, and hands you back a prediction report — complete with turning points,
        risk clusters, and every narrative that could unfold.
      </p>

      <div class="hero-pills">
        <div class="pill"><span class="pill-icon">⚡</span> Text-first — no file required to start</div>
        <div class="pill"><span class="pill-icon">🧠</span> Thousands of AI personas per simulation</div>
        <div class="pill"><span class="pill-icon">📊</span> Interactive reports you can keep questioning</div>
      </div>

      <div class="hero-cta">
        <button class="btn-hero" @click="scrollToConsole">
          Run your first simulation <span class="cta-arrow">↓</span>
        </button>
        <p class="hero-cta-note">No credit card needed &nbsp;·&nbsp; Free to explore</p>
      </div>
    </section>

    <!-- ── Quick-start regional templates ── -->
    <section class="templates-section">
      <div class="section-eyebrow">◈ QUICK START</div>
      <h2 class="section-title">Pick a scenario, hit run.</h2>
      <p class="section-sub">
        Each template pre-fills a real-world scenario shaped for its market. Edit any part of it — it's just a starting point.
      </p>

      <div class="templates-grid">
        <button
          v-for="t in templates"
          :key="t.region"
          class="template-card"
          :class="{ active: activeTemplate === t.region }"
          @click="applyTemplate(t)"
        >
          <div class="t-header">
            <span class="t-flag">{{ t.flag }}</span>
            <span class="t-region">{{ t.region }}</span>
          </div>
          <div class="t-title">{{ t.title }}</div>
          <div class="t-preview">{{ t.preview }}</div>
          <div class="t-use">{{ t.useLabel }} →</div>
        </button>
      </div>
    </section>

    <!-- ── Use cases ── -->
    <section class="usecases-section">
      <div class="section-eyebrow">◈ WHAT CHANAKYA DOES FOR YOU</div>
      <h2 class="section-title">Stop guessing. Start knowing.</h2>
      <p class="section-sub">
        Every big decision deserves a rehearsal. Chanakya gives you the stage.
      </p>

      <div class="usecases-grid">
        <div class="uc-card" v-for="uc in useCases" :key="uc.title">
          <div class="uc-icon-wrap">{{ uc.icon }}</div>
          <h3 class="uc-title">{{ uc.title }}</h3>
          <p class="uc-body">{{ uc.body }}</p>
          <div class="uc-tag">{{ uc.tag }}</div>
        </div>
      </div>
    </section>

    <!-- ── Simulation console ── -->
    <section class="console-section" ref="consoleRef">
      <div class="console-layout">

        <!-- Left: workflow -->
        <div class="workflow-panel">
          <div class="section-eyebrow">◈ HOW IT WORKS</div>
          <div class="workflow-steps">
            <div class="w-step" v-for="(s, i) in workflow" :key="i">
              <div class="w-connector">
                <div class="w-num">0{{ i + 1 }}</div>
                <div class="w-line" v-if="i < workflow.length - 1"></div>
              </div>
              <div class="w-body">
                <div class="w-title">{{ s.title }}</div>
                <div class="w-desc">{{ s.desc }}</div>
              </div>
            </div>
          </div>
        </div>

        <!-- Right: console -->
        <div class="console-box glass">
          <div class="console-top-bar">
            <span class="console-dot red"></span>
            <span class="console-dot yellow"></span>
            <span class="console-dot green"></span>
            <span class="console-title-label">chanakya://new-simulation</span>
          </div>

          <!-- File upload -->
          <div class="c-section">
            <div class="c-label-row">
              <span class="c-label">REALITY SEED</span>
              <span class="c-meta">PDF · MD · TXT — optional</span>
            </div>
            <div
              class="upload-drop"
              :class="{ over: isDragOver, filled: files.length > 0 }"
              @dragover.prevent="isDragOver = true"
              @dragleave.prevent="isDragOver = false"
              @drop.prevent="onDrop"
              @click="fileInput?.click()"
            >
              <input ref="fileInput" type="file" multiple accept=".pdf,.md,.txt" @change="onFileSelect" style="display:none" :disabled="loading"/>
              <div v-if="!files.length" class="upload-empty">
                <div class="upload-drop-icon">
                  <svg width="20" height="20" viewBox="0 0 20 20" fill="none">
                    <path d="M10 3v10M6 7l4-4 4 4" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
                    <path d="M3 14v1a2 2 0 002 2h10a2 2 0 002-2v-1" stroke="currentColor" stroke-width="1.5" stroke-linecap="round"/>
                  </svg>
                </div>
                <div class="upload-text">Drop a file here, or <u>browse</u></div>
                <div class="upload-hint">Supports PDF, Markdown, plain text</div>
              </div>
              <div v-else class="file-chips">
                <div class="file-chip" v-for="(f, i) in files" :key="i">
                  <span>📄</span>
                  <span class="chip-name">{{ f.name }}</span>
                  <button class="chip-remove" @click.stop="files.splice(i,1)">×</button>
                </div>
              </div>
            </div>
          </div>

          <div class="c-divider"><span>SCENARIO PROMPT</span></div>

          <!-- Prompt -->
          <div class="c-section">
            <div class="c-label-row">
              <span class="c-label">DESCRIBE YOUR SCENARIO</span>
            </div>
            <div class="prompt-wrap" :class="{ focused: promptFocused }">
              <textarea
                v-model="prompt"
                class="prompt-input"
                :placeholder="promptPlaceholder"
                rows="6"
                :disabled="loading"
                @focus="promptFocused = true"
                @blur="promptFocused = false"
              ></textarea>
              <div class="engine-badge">CHANAKYA ENGINE</div>
            </div>
            <div class="prompt-tips">
              <span class="tip">💡 Include: the decision · the audience · the trigger · the time horizon</span>
            </div>
          </div>

          <div class="c-section c-footer">
            <button class="run-btn" @click="startSim" :disabled="!canRun || loading">
              <span v-if="!loading">Run Simulation →</span>
              <span v-else class="running-text">Initializing simulation...</span>
            </button>
          </div>
        </div>
      </div>
    </section>

    <!-- ── Footer ── -->
    <footer class="site-footer">
      <div class="footer-inner">
        <div class="footer-brand">
          <svg viewBox="0 0 44 44" fill="none" width="22" height="22">
            <defs>
              <linearGradient id="footGrad" x1="0" y1="0" x2="44" y2="44" gradientUnits="userSpaceOnUse">
                <stop offset="0%" stop-color="#8B5CF6"/>
                <stop offset="100%" stop-color="#22D3EE"/>
              </linearGradient>
            </defs>
            <path d="M22 2 L38 11.5 L38 32.5 L22 42 L6 32.5 L6 11.5 Z" stroke="url(#footGrad)" stroke-width="1.5" fill="none" stroke-linejoin="round"/>
            <circle cx="22" cy="22" r="3" fill="url(#footGrad)"/>
          </svg>
          <span>CHANAKYA</span>
        </div>
        <p class="footer-copy">
          © {{ currentYear }} Chanakya. All rights reserved. Built for strategic clarity.
        </p>
        <div class="footer-links">
          <a href="#" @click.prevent>Privacy</a>
          <a href="#" @click.prevent>Terms</a>
          <a href="#" @click.prevent>Contact</a>
        </div>
      </div>
    </footer>

    <!-- ── History ── -->
    <HistoryDatabase />

    <!-- ── Auth modal ── -->
    <AuthModal :show="showAuth" @close="showAuth = false" @auth="onAuth" />
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'
import { useRouter } from 'vue-router'
import HistoryDatabase from '../components/HistoryDatabase.vue'
import AuthModal from '../components/AuthModal.vue'

const router = useRouter()

/* ── Auth ── */
const showAuth = ref(false)
const authMode = ref('signup')
const onAuth = ({ email }) => console.log('Authenticated:', email)

/* ── Theme label ── */
const themeLabel = computed(() => {
  const h = new Date().getHours()
  return h >= 6 && h < 18 ? '☀️ Day mode' : '🌙 Night mode'
})

/* ── Simulation form ── */
const prompt = ref('')
const files = ref([])
const loading = ref(false)
const isDragOver = ref(false)
const promptFocused = ref(false)
const fileInput = ref(null)
const consoleRef = ref(null)
const activeTemplate = ref(null)

const canRun = computed(() => prompt.value.trim().length > 10)

const promptPlaceholder = `Describe the scenario you want to simulate.

Example: A major airline announces a 25% fare hike during peak summer travel. How do frequent flyers, corporate travel managers, and leisure travellers react over 30 days?`

const onFileSelect = (e) => addFiles(Array.from(e.target.files))
const onDrop = (e) => {
  isDragOver.value = false
  addFiles(Array.from(e.dataTransfer.files))
}
const addFiles = (list) => {
  files.value.push(...list.filter(f => ['pdf','md','txt'].includes(f.name.split('.').pop().toLowerCase())))
}

const scrollToConsole = () => consoleRef.value?.scrollIntoView({ behavior: 'smooth', block: 'start' })
const scrollToTop = () => window.scrollTo({ top: 0, behavior: 'smooth' })

const applyTemplate = (t) => {
  prompt.value = t.prompt
  activeTemplate.value = t.region
  scrollToConsole()
}

const startSim = () => {
  if (!canRun.value || loading.value) return
  import('../store/pendingUpload.js').then(({ setPendingUpload }) => {
    setPendingUpload(files.value, prompt.value)
    router.push({ name: 'Process', params: { projectId: 'new' } })
  })
}

/* ── Data ── */
const currentYear = new Date().getFullYear()

const templates = [
  {
    flag: '🇮🇳',
    region: 'India',
    title: 'Election Campaign Simulation',
    preview: 'Farm loan waivers, 45 days before polling',
    useLabel: 'Use this scenario',
    prompt: `A major political party announces farm loan waivers across 5 key swing states, 45 days before general elections. Simulate how this narrative spreads across WhatsApp communities, regional news channels, and urban social media. Identify the decisive swing voter clusters and counter-narratives that emerge within 3 weeks.`
  },
  {
    flag: '🇯🇵',
    region: 'Japan',
    title: 'Enterprise Market Entry',
    preview: 'US tech company enters at 40% below incumbents',
    useLabel: 'Use this scenario',
    prompt: `A US software company plans to enter Japan's enterprise ERP market with pricing 40% below local incumbents and an AI-first feature set. Model how Japanese procurement committees, existing vendors (Fujitsu, NEC), and industry associations respond over a 60-day period. Surface the cultural and process barriers the company needs to address.`
  },
  {
    flag: '🇺🇸',
    region: 'United States',
    title: 'SaaS Pricing Disruption',
    preview: 'AI-native CRM at half the Salesforce price',
    useLabel: 'Use this scenario',
    prompt: `A well-funded startup announces an AI-native CRM alternative to Salesforce at 50% lower cost and a migration tool that moves data in 48 hours. Map how enterprise buyers, existing Salesforce customers, sales partners, and venture investors react across LinkedIn, analyst reports, and sales forums over 90 days.`
  },
  {
    flag: '🇪🇺',
    region: 'Europe',
    title: 'Regulatory Impact Analysis',
    preview: 'New EU AI liability rules — 6-month horizon',
    useLabel: 'Use this scenario',
    prompt: `The European Commission proposes new AI liability rules requiring that all AI-assisted decisions affecting consumers be fully explainable within 48 hours of request. Simulate how German tech firms, French fintech startups, UK-based insurers, and pan-European consumer advocacy groups position, react, and lobby over a 6-month period.`
  }
]

const useCases = [
  {
    icon: '🎯',
    title: 'Test before you commit',
    body: 'Launch campaigns, pricing changes, or product announcements into a simulated world first. Find out what lands — and what backfires — before you spend a single dollar.',
    tag: 'For product & marketing teams'
  },
  {
    icon: '🔍',
    title: 'Find your blind spots',
    body: "Every strategy has an assumption it's hiding. Chanakya surfaces the reactions, coalitions, and counter-narratives your plan didn't account for — while there's still time to adjust.",
    tag: 'For strategy & consulting'
  },
  {
    icon: '🌊',
    title: 'See the ripple, not just the splash',
    body: 'One decision sets off a chain. Chanakya maps how a single announcement cascades across media, regulators, competitors, and customers over days, weeks, or months.',
    tag: 'For policy & government'
  },
  {
    icon: '💬',
    title: 'A report you can argue with',
    body: 'When the simulation ends, the conversation starts. Ask your report why a cluster formed, what an actor would say next, or how a different trigger changes the outcome.',
    tag: 'For research & analysis'
  },
  {
    icon: '📈',
    title: 'Price your conviction before the market does',
    body: 'Think you know how a ballot, rate decision, or macro event plays out? Run the scenario in Chanakya first — see where probability clusters form and which outcome the crowd will converge on before it shows up on any prediction market.',
    tag: '🇺🇸 For prediction market traders · Polymarket-style'
  },
  {
    icon: '🏦',
    title: 'Trade the narrative, not just the number',
    body: 'Before an RBI policy meet, Union Budget, or sector announcement — simulate how Nifty 50 stocks, FII flows, and retail sentiment move. Know which sectors crowd in and which rotate out before the opening bell.',
    tag: '🇮🇳 For equity traders & fund managers'
  }
]

const workflow = [
  {
    title: 'Drop in your seed',
    desc: 'A plain-language question, a PDF brief, or a memo. Any format works. Chanakya reads it all.'
  },
  {
    title: 'Knowledge graph builds',
    desc: 'Chanakya extracts every actor, relationship, and context thread. No manual tagging needed.'
  },
  {
    title: 'Agents go live',
    desc: 'Thousands of AI personas interact, post, react, and influence each other — just like real people.'
  },
  {
    title: 'Report lands in your hands',
    desc: 'Every turning point, risk cluster, and emergent narrative is mapped in a structured, interactive report.'
  },
  {
    title: 'Keep questioning it',
    desc: "Drill into any actor or outcome. The simulation stays alive — it answers like it was there."
  }
]
</script>

<style scoped>
/* ── Layout ── */
.home {
  min-height: 100vh;
  position: relative;
  color: var(--text);
  font-family: 'Noto Sans', 'Noto Sans SC', system-ui, sans-serif;
}

/* ── Drops ── */
.drop-1 {
  width: 800px; height: 800px;
  background: radial-gradient(ellipse 60% 50% at 50% 50%, var(--blob-1) 0%, transparent 75%);
  top: -250px; right: -200px;
  animation-duration: 22s;
}
.drop-2 {
  width: 640px; height: 640px;
  background: radial-gradient(ellipse 55% 60% at 50% 50%, var(--blob-2) 0%, transparent 75%);
  bottom: 5%; left: -160px;
  animation-duration: 18s;
  animation-delay: -9s;
}
.drop-3 {
  width: 500px; height: 500px;
  background: radial-gradient(ellipse 50% 55% at 50% 50%, var(--blob-3) 0%, transparent 70%);
  top: 35%; left: 38%;
  animation-duration: 26s;
  animation-delay: -4s;
}

/* ── Navbar ── */
.navbar {
  position: sticky;
  top: 0;
  z-index: 200;
  height: 64px;
  border-top: none;
  border-left: none;
  border-right: none;
  border-bottom: 1px solid var(--border);
  border-radius: 0;
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0 48px;
  backdrop-filter: blur(24px);
  -webkit-backdrop-filter: blur(24px);
  background: var(--nav-bg);
}
.nav-brand {
  display: flex;
  align-items: center;
  gap: 11px;
  cursor: pointer;
  user-select: none;
}
.logo-svg { width: 34px; height: 34px; flex-shrink: 0; }
.brand-name {
  font-size: 1rem;
  font-weight: 800;
  letter-spacing: 4px;
  background: linear-gradient(135deg, var(--primary), var(--accent));
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
}
.nav-actions {
  display: flex;
  align-items: center;
  gap: 12px;
}
.theme-hint {
  font-size: 0.75rem;
  color: var(--text-muted);
  padding: 4px 10px;
  background: var(--badge-bg);
  border-radius: 20px;
  border: 1px solid var(--border);
}
.btn-ghost {
  background: transparent;
  border: 1px solid var(--border);
  color: var(--text);
  padding: 8px 18px;
  border-radius: 8px;
  font-size: 0.85rem;
  font-weight: 600;
  transition: all 0.2s;
}
.btn-ghost:hover {
  background: var(--badge-bg);
  border-color: var(--primary);
  color: var(--primary);
}
.btn-primary {
  background: linear-gradient(135deg, var(--btn-grad-from), var(--btn-grad-to));
  color: #fff;
  border: none;
  padding: 9px 20px;
  border-radius: 8px;
  font-size: 0.85rem;
  font-weight: 700;
  transition: opacity 0.2s, transform 0.2s;
  box-shadow: 0 4px 20px var(--drop-shadow);
}
.btn-primary:hover { opacity: 0.88; transform: translateY(-1px); }

/* ── Hero ── */
.hero {
  position: relative;
  z-index: 1;
  max-width: 820px;
  margin: 0 auto;
  padding: 100px 48px 80px;
  text-align: center;
}
.hero-badge {
  display: inline-flex;
  align-items: center;
  gap: 8px;
  background: var(--badge-bg);
  border: 1px solid var(--badge-border);
  color: var(--badge-text);
  padding: 6px 18px;
  border-radius: 100px;
  font-size: 0.76rem;
  font-weight: 600;
  letter-spacing: 0.3px;
  margin-bottom: 36px;
}
.badge-pulse {
  width: 7px; height: 7px;
  background: var(--primary);
  border-radius: 50%;
  flex-shrink: 0;
  animation: pulse-badge 2.2s ease-in-out infinite;
}
@keyframes pulse-badge {
  0%, 100% { opacity: 1; transform: scale(1); box-shadow: 0 0 0 0 var(--primary); }
  50%       { opacity: 0.5; transform: scale(0.75); box-shadow: 0 0 0 4px transparent; }
}
.hero-title {
  font-size: 5.5rem;
  font-weight: 900;
  line-height: 1.05;
  letter-spacing: -3px;
  color: var(--text);
  margin-bottom: 28px;
}
.grad-text {
  background: linear-gradient(135deg, var(--primary) 0%, var(--accent) 100%);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
}
.hero-desc {
  font-size: 1.12rem;
  line-height: 1.85;
  color: var(--text-subtle);
  max-width: 660px;
  margin: 0 auto 36px;
}
.hero-pills {
  display: flex;
  flex-wrap: wrap;
  gap: 10px;
  justify-content: center;
  margin-bottom: 40px;
}
.pill {
  display: inline-flex;
  align-items: center;
  gap: 6px;
  background: var(--card-bg);
  border: 1px solid var(--border-subtle);
  border-radius: 100px;
  padding: 7px 16px;
  font-size: 0.82rem;
  color: var(--text-muted);
  backdrop-filter: blur(8px);
  transition: border-color 0.2s, color 0.2s;
}
.pill:hover { border-color: var(--border); color: var(--text); }
.pill-icon { font-size: 0.95rem; }
.hero-cta { display: flex; flex-direction: column; align-items: center; gap: 10px; }
.btn-hero {
  display: inline-flex;
  align-items: center;
  gap: 10px;
  background: linear-gradient(135deg, var(--btn-grad-from), var(--btn-grad-to));
  color: #fff;
  border: none;
  border-radius: 14px;
  padding: 16px 36px;
  font-size: 1rem;
  font-weight: 700;
  transition: opacity 0.25s, transform 0.25s, box-shadow 0.25s;
  box-shadow: 0 8px 32px var(--drop-shadow);
}
.btn-hero:hover {
  opacity: 0.9;
  transform: translateY(-2px);
  box-shadow: 0 12px 40px var(--drop-shadow);
}
.cta-arrow {
  animation: bounce-y 1.6s ease-in-out infinite;
  display: inline-block;
}
@keyframes bounce-y {
  0%, 100% { transform: translateY(0); }
  50%       { transform: translateY(5px); }
}
.hero-cta-note { font-size: 0.75rem; color: var(--text-muted); opacity: 0.7; }

/* ── Section shared ── */
.section-eyebrow {
  font-size: 0.7rem;
  font-weight: 800;
  letter-spacing: 3px;
  color: var(--text-muted);
  margin-bottom: 14px;
}
.section-title {
  font-size: 2.2rem;
  font-weight: 800;
  letter-spacing: -1px;
  color: var(--text);
  margin-bottom: 12px;
}
.section-sub {
  font-size: 0.95rem;
  color: var(--text-subtle);
  line-height: 1.7;
  max-width: 560px;
  margin-bottom: 36px;
}

/* ── Templates ── */
.templates-section {
  position: relative;
  z-index: 1;
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 48px 80px;
}
.templates-grid {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 16px;
}
.template-card {
  background: var(--card-bg);
  border: 1px solid var(--border-subtle);
  border-radius: 16px;
  padding: 22px 20px;
  text-align: left;
  cursor: pointer;
  transition: all 0.28s ease;
  backdrop-filter: blur(12px);
  display: flex;
  flex-direction: column;
  gap: 10px;
}
.template-card:hover {
  border-color: var(--border);
  background: var(--badge-bg);
  transform: translateY(-3px);
  box-shadow: 0 8px 32px var(--drop-shadow);
}
.template-card.active {
  border-color: var(--primary);
  background: var(--badge-bg);
  box-shadow: 0 0 0 2px var(--primary), 0 8px 32px var(--drop-shadow);
}
.t-header {
  display: flex;
  align-items: center;
  gap: 8px;
}
.t-flag { font-size: 1.5rem; line-height: 1; }
.t-region { font-size: 0.7rem; font-weight: 800; letter-spacing: 1.5px; color: var(--text-muted); text-transform: uppercase; }
.t-title { font-size: 0.9rem; font-weight: 700; color: var(--text); line-height: 1.3; }
.t-preview { font-size: 0.78rem; color: var(--text-muted); line-height: 1.5; flex: 1; }
.t-use { font-size: 0.76rem; font-weight: 700; color: var(--primary); margin-top: 4px; }

/* ── Use cases ── */
.usecases-section {
  position: relative;
  z-index: 1;
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 48px 80px;
}
.usecases-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 20px;
}
.uc-card {
  background: var(--card-bg);
  border: 1px solid var(--border-subtle);
  border-radius: 18px;
  padding: 28px 26px;
  backdrop-filter: blur(12px);
  transition: all 0.28s ease;
  position: relative;
  overflow: hidden;
}
.uc-card::before {
  content: '';
  position: absolute;
  top: 0; left: 0; right: 0;
  height: 2px;
  background: linear-gradient(90deg, var(--primary), var(--accent));
  opacity: 0;
  transition: opacity 0.3s;
}
.uc-card:hover {
  border-color: var(--border);
  transform: translateY(-3px);
  box-shadow: 0 12px 40px var(--drop-shadow);
}
.uc-card:hover::before { opacity: 1; }
.uc-icon-wrap { font-size: 1.8rem; margin-bottom: 14px; display: block; }
.uc-title { font-size: 1.05rem; font-weight: 800; color: var(--text); margin-bottom: 10px; letter-spacing: -0.3px; }
.uc-body { font-size: 0.86rem; color: var(--text-subtle); line-height: 1.75; margin-bottom: 16px; }
.uc-tag {
  display: inline-block;
  font-size: 0.68rem;
  font-weight: 700;
  letter-spacing: 1px;
  text-transform: uppercase;
  color: var(--primary);
  background: var(--badge-bg);
  border: 1px solid var(--badge-border);
  border-radius: 100px;
  padding: 3px 10px;
}

/* ── Console section ── */
.console-section {
  position: relative;
  z-index: 1;
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 48px 100px;
}
.console-layout {
  display: flex;
  gap: 56px;
  align-items: flex-start;
}

/* Workflow panel */
.workflow-panel { flex: 0.85; padding-top: 4px; }
.workflow-steps { display: flex; flex-direction: column; }
.w-step { display: flex; gap: 18px; }
.w-connector { display: flex; flex-direction: column; align-items: center; flex-shrink: 0; width: 40px; }
.w-num {
  font-size: 0.68rem;
  font-weight: 800;
  color: var(--primary);
  background: var(--badge-bg);
  border: 1px solid var(--badge-border);
  border-radius: 6px;
  padding: 4px 6px;
  letter-spacing: 1px;
  white-space: nowrap;
}
.w-line {
  width: 1px;
  flex: 1;
  min-height: 24px;
  background: linear-gradient(to bottom, var(--border), transparent);
  margin: 5px 0;
}
.w-body { flex: 1; padding-bottom: 28px; }
.w-title { font-size: 0.92rem; font-weight: 700; color: var(--text); margin-bottom: 4px; }
.w-desc { font-size: 0.8rem; color: var(--text-muted); line-height: 1.65; }

/* Console box */
.console-box {
  flex: 1.15;
  border-radius: 20px;
  overflow: hidden;
  box-shadow:
    0 0 0 1px rgba(139,92,246,0.12),
    0 0 80px var(--drop-shadow),
    0 24px 64px rgba(0,0,0,0.35);
  background: var(--surface) !important;
  backdrop-filter: blur(28px) !important;
  -webkit-backdrop-filter: blur(28px) !important;
}
.console-top-bar {
  display: flex;
  align-items: center;
  gap: 6px;
  padding: 12px 18px;
  border-bottom: 1px solid var(--border-subtle);
  background: var(--badge-bg);
}
.console-dot {
  width: 10px; height: 10px;
  border-radius: 50%;
  flex-shrink: 0;
}
.console-dot.red    { background: #FF5F57; }
.console-dot.yellow { background: #FEBC2E; }
.console-dot.green  { background: #28C840; }
.console-title-label {
  font-size: 0.7rem;
  color: var(--text-muted);
  margin-left: 6px;
  font-family: monospace;
  letter-spacing: 0.5px;
}

.c-section { padding: 18px 22px; }
.c-label-row { display: flex; justify-content: space-between; align-items: center; margin-bottom: 10px; }
.c-label { font-size: 0.65rem; font-weight: 800; color: var(--primary); letter-spacing: 2.5px; }
.c-meta  { font-size: 0.65rem; color: var(--text-muted); letter-spacing: 1px; }

.upload-drop {
  border: 1px dashed var(--border);
  border-radius: 12px;
  min-height: 120px;
  max-height: 160px;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  transition: all 0.28s;
  background: var(--badge-bg);
  overflow-y: auto;
}
.upload-drop:hover, .upload-drop.over {
  border-color: var(--primary);
  background: rgba(139,92,246,0.07);
}
.upload-drop.filled { align-items: flex-start; }
.upload-empty { text-align: center; padding: 16px; }
.upload-drop-icon {
  width: 38px; height: 38px;
  border: 1px solid var(--border);
  border-radius: 10px;
  display: flex; align-items: center; justify-content: center;
  margin: 0 auto 10px;
  color: var(--primary);
}
.upload-text { font-size: 0.82rem; color: var(--text-subtle); margin-bottom: 4px; }
.upload-hint { font-size: 0.72rem; color: var(--text-muted); }
.file-chips { width: 100%; padding: 10px; display: flex; flex-direction: column; gap: 6px; }
.file-chip {
  display: flex; align-items: center; gap: 7px;
  background: var(--badge-bg);
  border: 1px solid var(--border);
  border-radius: 8px;
  padding: 7px 10px;
  font-size: 0.8rem;
}
.chip-name { flex: 1; color: var(--text); min-width: 0; overflow: hidden; text-overflow: ellipsis; white-space: nowrap; }
.chip-remove { background: none; border: none; color: var(--text-muted); font-size: 1rem; padding: 0; transition: color 0.2s; }
.chip-remove:hover { color: #F87171; }

.c-divider {
  display: flex; align-items: center;
  margin: 0 22px;
  border-top: 1px solid var(--border-subtle);
}
.c-divider span { font-size: 0.62rem; color: var(--text-muted); letter-spacing: 2px; font-weight: 700; padding: 8px 0; opacity: 0.5; }

.prompt-wrap {
  position: relative;
  border: 1px solid var(--border);
  border-radius: 12px;
  background: var(--input-bg);
  transition: border-color 0.25s, box-shadow 0.25s;
}
.prompt-wrap.focused {
  border-color: var(--primary);
  box-shadow: 0 0 0 3px rgba(139,92,246,0.1);
}
.prompt-input {
  width: 100%;
  border: none;
  background: transparent;
  padding: 14px 16px;
  font-family: 'Noto Sans', system-ui, sans-serif;
  font-size: 0.85rem;
  line-height: 1.75;
  resize: vertical;
  outline: none;
  min-height: 130px;
  color: var(--input-text);
}
.prompt-input::placeholder { color: var(--input-placeholder); }
.engine-badge {
  position: absolute; bottom: 9px; right: 12px;
  font-size: 0.6rem; color: var(--text-muted); letter-spacing: 2px; font-weight: 700; opacity: 0.5;
}
.prompt-tips { margin-top: 8px; }
.tip { font-size: 0.75rem; color: var(--text-muted); }

.c-footer { padding-top: 0; }
.run-btn {
  width: 100%;
  background: linear-gradient(135deg, var(--btn-grad-from) 0%, var(--btn-grad-to) 100%);
  color: #fff;
  border: none;
  border-radius: 12px;
  padding: 17px 24px;
  font-family: 'Noto Sans', system-ui, sans-serif;
  font-size: 0.98rem;
  font-weight: 700;
  display: flex;
  justify-content: space-between;
  align-items: center;
  transition: all 0.28s;
  position: relative;
  overflow: hidden;
  box-shadow: 0 6px 24px var(--drop-shadow);
}
.run-btn::before {
  content: '';
  position: absolute; inset: 0;
  background: linear-gradient(135deg, #8B5CF6, #22D3EE);
  opacity: 0; transition: opacity 0.28s;
}
.run-btn:hover:not(:disabled)::before { opacity: 1; }
.run-btn > * { position: relative; z-index: 1; }
.run-btn:hover:not(:disabled) { transform: translateY(-1px); box-shadow: 0 10px 32px var(--drop-shadow); }
.run-btn:disabled { background: var(--border); color: var(--text-muted); cursor: not-allowed; box-shadow: none; }
.running-text::after { content: '…'; animation: dots 1s steps(3) infinite; }
@keyframes dots { 0% {content:'.'} 33% {content:'..'} 66% {content:'...'} }

/* ── Footer ── */
.site-footer {
  position: relative;
  z-index: 1;
  border-top: 1px solid var(--border-subtle);
  padding: 28px 48px;
}
.footer-inner {
  max-width: 1200px;
  margin: 0 auto;
  display: flex;
  align-items: center;
  justify-content: space-between;
  flex-wrap: wrap;
  gap: 12px;
}
.footer-brand {
  display: flex; align-items: center; gap: 8px;
  font-size: 0.78rem; font-weight: 800; letter-spacing: 2px;
  color: var(--text-muted);
}
.footer-copy { font-size: 0.75rem; color: var(--text-muted); }
.footer-links { display: flex; gap: 20px; }
.footer-links a { font-size: 0.75rem; color: var(--text-muted); text-decoration: none; transition: color 0.2s; }
.footer-links a:hover { color: var(--primary); }

/* ── Responsive ── */
@media (max-width: 1024px) {
  .templates-grid { grid-template-columns: repeat(2, 1fr); }
  .usecases-grid  { grid-template-columns: 1fr; }
  .console-layout { flex-direction: column; }
  .hero-title { font-size: 4rem; }
}
@media (max-width: 640px) {
  .navbar { padding: 0 20px; }
  .hero, .templates-section, .usecases-section, .console-section { padding-left: 20px; padding-right: 20px; }
  .hero-title { font-size: 2.8rem; letter-spacing: -2px; }
  .templates-grid { grid-template-columns: 1fr; }
  .nav-actions .theme-hint { display: none; }
  .footer-inner { flex-direction: column; text-align: center; }
  .footer-links { justify-content: center; }
}
</style>
