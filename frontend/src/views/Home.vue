<template>
  <div class="home-container">
    <!-- Background effects -->
    <div class="bg-grid"></div>
    <div class="bg-orb bg-orb-1"></div>
    <div class="bg-orb bg-orb-2"></div>

    <!-- Navbar -->
    <nav class="navbar">
      <div class="nav-brand">
        <svg class="logo-mark" viewBox="0 0 44 44" fill="none" xmlns="http://www.w3.org/2000/svg">
          <defs>
            <linearGradient id="logoGrad" x1="0" y1="0" x2="44" y2="44" gradientUnits="userSpaceOnUse">
              <stop offset="0%" stop-color="#8B5CF6"/>
              <stop offset="100%" stop-color="#22D3EE"/>
            </linearGradient>
          </defs>
          <path d="M22 2 L38 11.5 L38 32.5 L22 42 L6 32.5 L6 11.5 Z" stroke="url(#logoGrad)" stroke-width="1.5" fill="none" stroke-linejoin="round"/>
          <path d="M22 10 L30 22 L22 34 L14 22 Z" stroke="url(#logoGrad)" stroke-width="1.5" fill="rgba(139,92,246,0.1)" stroke-linejoin="round"/>
          <circle cx="22" cy="22" r="3.5" fill="url(#logoGrad)"/>
          <circle cx="22" cy="22" r="6.5" stroke="url(#logoGrad)" stroke-width="0.75" stroke-dasharray="2 3" fill="none"/>
        </svg>
        <span class="brand-name">CHANAKYA</span>
      </div>
      <a href="https://github.com/abhipuja1995/MiroFish" target="_blank" class="github-link">
        GitHub <span>↗</span>
      </a>
    </nav>

    <!-- Hero -->
    <section class="hero-section">
      <div class="hero-badge">
        <span class="badge-dot"></span>
        Strategic Intelligence Engine &nbsp;·&nbsp; v0.1 Preview
      </div>

      <h1 class="hero-title">
        Predict Anything,<br>
        <span class="gradient-text">Decide With Certainty</span>
      </h1>

      <p class="hero-sub">
        Upload a document. Ask like ChatGPT. Chanakya orchestrates thousands of AI agents across
        social surfaces, maps every turning point, and hands you back a living prediction report —
        all in one continuous workflow.
      </p>

      <div class="hero-metrics">
        <div class="metric">
          <div class="metric-val">Text-first</div>
          <div class="metric-label">No file required to start</div>
        </div>
        <div class="metric-sep"></div>
        <div class="metric">
          <div class="metric-val">Multi-agent</div>
          <div class="metric-label">Thousands of AI personas</div>
        </div>
        <div class="metric-sep"></div>
        <div class="metric">
          <div class="metric-val">Interactive</div>
          <div class="metric-label">Deep-dive after the report</div>
        </div>
      </div>

      <button class="scroll-cta" @click="scrollToConsole">
        Start a Simulation <span class="cta-arrow">↓</span>
      </button>
    </section>

    <!-- Use cases -->
    <section class="use-cases-section">
      <div class="section-label">◈ USE CASES</div>
      <div class="use-cases-grid">
        <div class="uc-card" v-for="uc in useCases" :key="uc.title">
          <div class="uc-icon">{{ uc.icon }}</div>
          <div class="uc-title">{{ uc.title }}</div>
          <div class="uc-desc">{{ uc.desc }}</div>
        </div>
      </div>
    </section>

    <!-- Dashboard: workflow + console -->
    <section class="dashboard-section" ref="consoleRef">
      <!-- Left: Steps -->
      <div class="left-panel">
        <div class="panel-label">◈ WORKFLOW SEQUENCE</div>
        <div class="workflow-list">
          <div class="workflow-item" v-for="(step, i) in steps" :key="i">
            <div class="step-connector">
              <span class="step-num">0{{ i + 1 }}</span>
              <div class="step-line" v-if="i < steps.length - 1"></div>
            </div>
            <div class="step-info">
              <div class="step-title">{{ step.title }}</div>
              <div class="step-desc">{{ step.desc }}</div>
            </div>
          </div>
        </div>
      </div>

      <!-- Right: Console -->
      <div class="right-panel">
        <div class="console-box">
          <div class="console-section">
            <div class="console-header">
              <span class="console-label">REALITY SEED</span>
              <span class="console-meta">PDF · MD · TXT</span>
            </div>
            <div
              class="upload-zone"
              :class="{ 'drag-over': isDragOver, 'has-files': files.length > 0 }"
              @dragover.prevent="handleDragOver"
              @dragleave.prevent="handleDragLeave"
              @drop.prevent="handleDrop"
              @click="triggerFileInput"
            >
              <input
                ref="fileInput"
                type="file"
                multiple
                accept=".pdf,.md,.txt"
                @change="handleFileSelect"
                style="display:none"
                :disabled="loading"
              />
              <div v-if="files.length === 0" class="upload-placeholder">
                <div class="upload-icon-wrap">↑</div>
                <div class="upload-title">Drop files here</div>
                <div class="upload-hint">or click to browse</div>
              </div>
              <div v-else class="file-list">
                <div v-for="(file, index) in files" :key="index" class="file-item">
                  <span>📄</span>
                  <span class="file-name">{{ file.name }}</span>
                  <button @click.stop="removeFile(index)" class="remove-btn">×</button>
                </div>
              </div>
            </div>
          </div>

          <div class="console-divider"><span>SIMULATION PARAMETERS</span></div>

          <div class="console-section">
            <div class="console-header">
              <span class="console-label">SCENARIO PROMPT</span>
            </div>
            <div class="input-wrapper">
              <textarea
                v-model="formData.simulationRequirement"
                class="code-input"
                placeholder="Describe the scenario. Include: the decision, the audience, the trigger event, and the time horizon.

Example: If we raise enterprise pricing by 30%, how will the market react over 90 days?"
                rows="6"
                :disabled="loading"
              ></textarea>
              <div class="model-badge">CHANAKYA ENGINE</div>
            </div>
          </div>

          <div class="console-section btn-section">
            <button
              class="start-engine-btn"
              @click="startSimulation"
              :disabled="!canSubmit || loading"
            >
              <span v-if="!loading">Run Simulation</span>
              <span v-else>Initializing...</span>
              <span class="btn-arrow">→</span>
            </button>
          </div>
        </div>
      </div>
    </section>

    <HistoryDatabase />
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'
import { useRouter } from 'vue-router'
import HistoryDatabase from '../components/HistoryDatabase.vue'

const router = useRouter()

const formData = ref({ simulationRequirement: '' })
const files = ref([])
const loading = ref(false)
const isDragOver = ref(false)
const fileInput = ref(null)
const consoleRef = ref(null)

const canSubmit = computed(() =>
  formData.value.simulationRequirement.trim() !== '' && files.value.length > 0
)

const triggerFileInput = () => { if (!loading.value) fileInput.value?.click() }
const handleFileSelect = (e) => addFiles(Array.from(e.target.files))
const handleDragOver = () => { if (!loading.value) isDragOver.value = true }
const handleDragLeave = () => { isDragOver.value = false }
const handleDrop = (e) => {
  isDragOver.value = false
  if (!loading.value) addFiles(Array.from(e.dataTransfer.files))
}
const addFiles = (newFiles) => {
  files.value.push(...newFiles.filter(f =>
    ['pdf', 'md', 'txt'].includes(f.name.split('.').pop().toLowerCase())
  ))
}
const removeFile = (i) => files.value.splice(i, 1)
const scrollToConsole = () => consoleRef.value?.scrollIntoView({ behavior: 'smooth' })

const startSimulation = () => {
  if (!canSubmit.value || loading.value) return
  import('../store/pendingUpload.js').then(({ setPendingUpload }) => {
    setPendingUpload(files.value, formData.value.simulationRequirement)
    router.push({ name: 'Process', params: { projectId: 'new' } })
  })
}

const useCases = [
  {
    icon: '◎',
    title: 'Campaign Stress-Testing',
    desc: 'Pressure-test messaging and narratives before launch. See how audiences react across demographics and media surfaces.'
  },
  {
    icon: '◎',
    title: 'Pricing Reaction Modeling',
    desc: 'Simulate customer segment responses to price changes. Identify churn triggers and willingness-to-pay ceilings.'
  },
  {
    icon: '◎',
    title: 'Policy Impact Analysis',
    desc: 'Model coalition formation, controversy clusters, and stakeholder alignment before a policy goes live.'
  },
  {
    icon: '◎',
    title: 'Market Narrative Mapping',
    desc: 'Explore feedback loops between media, investors, and customers. Surface tipping points before they happen.'
  }
]

const steps = [
  {
    title: 'Seed Material',
    desc: 'Plain-language questions or documents — PDFs, briefs, memos. No structured input required.'
  },
  {
    title: 'Knowledge Graph',
    desc: 'Chanakya extracts actors, relationships, and context automatically from your seed.'
  },
  {
    title: 'Agent Simulation',
    desc: 'Thousands of AI personas interact across modeled social surfaces simultaneously.'
  },
  {
    title: 'Prediction Report',
    desc: 'Turning points, risks, and emergent clusters surfaced as an interactive report.'
  },
  {
    title: 'Deep Interaction',
    desc: 'Continue questioning the generated scenario. Drill into any actor or outcome like a live analyst.'
  }
]
</script>

<style scoped>
.home-container {
  min-height: 100vh;
  background: #07090F;
  color: #F1F5F9;
  font-family: 'Noto Sans', 'Noto Sans SC', system-ui, sans-serif;
  position: relative;
  overflow-x: hidden;
}

/* ── Background ── */
.bg-grid {
  position: fixed;
  inset: 0;
  background-image: radial-gradient(circle, rgba(139, 92, 246, 0.07) 1px, transparent 1px);
  background-size: 32px 32px;
  pointer-events: none;
  z-index: 0;
}
.bg-orb {
  position: fixed;
  border-radius: 50%;
  filter: blur(120px);
  pointer-events: none;
  z-index: 0;
  animation: orb-drift 20s ease-in-out infinite alternate;
}
.bg-orb-1 {
  width: 700px; height: 700px;
  background: radial-gradient(circle, rgba(139, 92, 246, 0.18) 0%, transparent 70%);
  top: -250px; right: -150px;
}
.bg-orb-2 {
  width: 500px; height: 500px;
  background: radial-gradient(circle, rgba(6, 182, 212, 0.12) 0%, transparent 70%);
  bottom: 0; left: -100px;
  animation-delay: -10s;
}
@keyframes orb-drift {
  from { transform: translate(0, 0); }
  to   { transform: translate(30px, 30px); }
}

/* ── Navbar ── */
.navbar {
  position: sticky;
  top: 0;
  z-index: 100;
  height: 64px;
  background: rgba(7, 9, 15, 0.85);
  backdrop-filter: blur(20px);
  -webkit-backdrop-filter: blur(20px);
  border-bottom: 1px solid rgba(139, 92, 246, 0.15);
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0 48px;
}
.nav-brand {
  display: flex;
  align-items: center;
  gap: 12px;
}
.logo-mark {
  width: 36px;
  height: 36px;
  flex-shrink: 0;
}
.brand-name {
  font-size: 1.05rem;
  font-weight: 800;
  letter-spacing: 4px;
  background: linear-gradient(135deg, #8B5CF6, #22D3EE);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
}
.github-link {
  color: #64748B;
  text-decoration: none;
  font-size: 0.85rem;
  font-weight: 500;
  display: flex;
  align-items: center;
  gap: 5px;
  transition: color 0.2s;
}
.github-link:hover { color: #F1F5F9; }

/* ── Hero ── */
.hero-section {
  position: relative;
  z-index: 1;
  max-width: 860px;
  margin: 0 auto;
  padding: 110px 48px 90px;
  text-align: center;
}
.hero-badge {
  display: inline-flex;
  align-items: center;
  gap: 8px;
  border: 1px solid rgba(139, 92, 246, 0.4);
  background: rgba(139, 92, 246, 0.08);
  color: #A78BFA;
  padding: 6px 18px;
  border-radius: 100px;
  font-size: 0.78rem;
  font-weight: 600;
  letter-spacing: 0.5px;
  margin-bottom: 40px;
}
.badge-dot {
  width: 6px; height: 6px;
  background: #8B5CF6;
  border-radius: 50%;
  flex-shrink: 0;
  animation: pulse-dot 2s ease-in-out infinite;
}
@keyframes pulse-dot {
  0%, 100% { opacity: 1; transform: scale(1); }
  50%       { opacity: 0.4; transform: scale(0.75); }
}
.hero-title {
  font-size: 5rem;
  font-weight: 800;
  line-height: 1.1;
  letter-spacing: -2.5px;
  color: #F1F5F9;
  margin-bottom: 28px;
}
.gradient-text {
  background: linear-gradient(135deg, #8B5CF6 0%, #22D3EE 100%);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  display: inline-block;
}
.hero-sub {
  font-size: 1.12rem;
  line-height: 1.85;
  color: #94A3B8;
  max-width: 660px;
  margin: 0 auto 52px;
  font-weight: 400;
}
.hero-metrics {
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 44px;
  margin-bottom: 52px;
}
.metric-val {
  font-size: 1.05rem;
  font-weight: 700;
  color: #F1F5F9;
  margin-bottom: 5px;
}
.metric-label {
  font-size: 0.72rem;
  color: #475569;
  text-transform: uppercase;
  letter-spacing: 0.8px;
}
.metric-sep {
  width: 1px; height: 40px;
  background: rgba(255, 255, 255, 0.07);
  flex-shrink: 0;
}
.scroll-cta {
  display: inline-flex;
  align-items: center;
  gap: 10px;
  background: transparent;
  border: 1px solid rgba(139, 92, 246, 0.45);
  color: #A78BFA;
  padding: 13px 30px;
  border-radius: 8px;
  font-size: 0.95rem;
  font-weight: 600;
  cursor: pointer;
  font-family: inherit;
  transition: all 0.3s ease;
}
.scroll-cta:hover {
  background: rgba(139, 92, 246, 0.1);
  border-color: #8B5CF6;
  color: #F1F5F9;
}
.cta-arrow {
  animation: bounce-down 1.5s ease-in-out infinite;
  display: inline-block;
}
@keyframes bounce-down {
  0%, 100% { transform: translateY(0); }
  50%       { transform: translateY(5px); }
}

/* ── Use cases ── */
.use-cases-section {
  position: relative;
  z-index: 1;
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 48px 80px;
}
.section-label {
  font-size: 0.72rem;
  color: #475569;
  letter-spacing: 2.5px;
  margin-bottom: 20px;
  font-weight: 700;
}
.use-cases-grid {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 16px;
}
.uc-card {
  background: rgba(13, 17, 23, 0.6);
  border: 1px solid rgba(255, 255, 255, 0.06);
  border-radius: 14px;
  padding: 26px 22px;
  transition: border-color 0.3s, background 0.3s, transform 0.3s;
}
.uc-card:hover {
  border-color: rgba(139, 92, 246, 0.35);
  background: rgba(139, 92, 246, 0.06);
  transform: translateY(-3px);
}
.uc-icon {
  font-size: 1.3rem;
  margin-bottom: 14px;
  color: #8B5CF6;
}
.uc-title {
  font-size: 0.92rem;
  font-weight: 700;
  color: #E2E8F0;
  margin-bottom: 10px;
}
.uc-desc {
  font-size: 0.80rem;
  color: #64748B;
  line-height: 1.65;
}

/* ── Dashboard ── */
.dashboard-section {
  position: relative;
  z-index: 1;
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 48px 100px;
  display: flex;
  gap: 60px;
  align-items: flex-start;
}

/* Left panel */
.left-panel { flex: 0.9; }
.panel-label {
  font-size: 0.72rem;
  color: #475569;
  letter-spacing: 2.5px;
  margin-bottom: 32px;
  font-weight: 700;
}
.workflow-list {
  display: flex;
  flex-direction: column;
}
.workflow-item {
  display: flex;
  gap: 20px;
  align-items: flex-start;
}
.step-connector {
  display: flex;
  flex-direction: column;
  align-items: center;
  flex-shrink: 0;
  width: 42px;
}
.step-num {
  font-size: 0.7rem;
  font-weight: 700;
  color: #8B5CF6;
  background: rgba(139, 92, 246, 0.1);
  border: 1px solid rgba(139, 92, 246, 0.3);
  border-radius: 6px;
  padding: 4px 7px;
  letter-spacing: 1px;
  white-space: nowrap;
}
.step-line {
  width: 1px;
  flex: 1;
  min-height: 28px;
  background: linear-gradient(to bottom, rgba(139, 92, 246, 0.35), rgba(139, 92, 246, 0.04));
  margin: 5px 0;
}
.step-info {
  flex: 1;
  padding-bottom: 30px;
}
.step-title {
  font-size: 0.95rem;
  font-weight: 700;
  color: #E2E8F0;
  margin-bottom: 5px;
}
.step-desc {
  font-size: 0.80rem;
  color: #64748B;
  line-height: 1.65;
}

/* Right panel / Console */
.right-panel { flex: 1.2; }
.console-box {
  background: rgba(13, 17, 23, 0.75);
  border: 1px solid rgba(139, 92, 246, 0.25);
  border-radius: 16px;
  overflow: hidden;
  backdrop-filter: blur(24px);
  -webkit-backdrop-filter: blur(24px);
  box-shadow:
    0 0 0 1px rgba(139, 92, 246, 0.05),
    0 0 60px rgba(139, 92, 246, 0.07),
    0 24px 64px rgba(0, 0, 0, 0.45);
}
.console-section { padding: 22px 26px; }
.btn-section { padding-top: 0; }
.console-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 14px;
}
.console-label {
  font-size: 0.68rem;
  font-weight: 800;
  color: #8B5CF6;
  letter-spacing: 2.5px;
}
.console-meta {
  font-size: 0.68rem;
  color: #334155;
  letter-spacing: 1.5px;
  font-weight: 600;
}
.upload-zone {
  border: 1px dashed rgba(139, 92, 246, 0.3);
  border-radius: 10px;
  min-height: 155px;
  max-height: 200px;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  transition: all 0.3s;
  background: rgba(139, 92, 246, 0.03);
  overflow-y: auto;
}
.upload-zone.drag-over,
.upload-zone:hover {
  border-color: rgba(139, 92, 246, 0.55);
  background: rgba(139, 92, 246, 0.07);
}
.upload-zone.has-files { align-items: flex-start; }
.upload-placeholder { text-align: center; }
.upload-icon-wrap {
  width: 36px; height: 36px;
  border: 1px solid rgba(139, 92, 246, 0.35);
  border-radius: 8px;
  display: flex;
  align-items: center;
  justify-content: center;
  margin: 0 auto 12px;
  color: #8B5CF6;
  font-size: 1rem;
}
.upload-title {
  font-size: 0.85rem;
  font-weight: 600;
  color: #94A3B8;
  margin-bottom: 4px;
}
.upload-hint {
  font-size: 0.73rem;
  color: #334155;
}
.file-list {
  width: 100%;
  padding: 12px;
  display: flex;
  flex-direction: column;
  gap: 8px;
}
.file-item {
  display: flex;
  align-items: center;
  gap: 8px;
  background: rgba(139, 92, 246, 0.08);
  border: 1px solid rgba(139, 92, 246, 0.2);
  border-radius: 7px;
  padding: 8px 12px;
  font-size: 0.82rem;
}
.file-name { flex: 1; color: #CBD5E1; min-width: 0; overflow: hidden; text-overflow: ellipsis; white-space: nowrap; }
.remove-btn {
  background: none;
  border: none;
  cursor: pointer;
  color: #475569;
  font-size: 1.1rem;
  line-height: 1;
  padding: 0;
  transition: color 0.2s;
  flex-shrink: 0;
}
.remove-btn:hover { color: #EF4444; }
.console-divider {
  display: flex;
  align-items: center;
  margin: 0 26px;
  border-top: 1px solid rgba(255, 255, 255, 0.04);
}
.console-divider span {
  font-size: 0.63rem;
  color: #1E293B;
  letter-spacing: 2.5px;
  font-weight: 700;
  padding: 10px 0;
}
.input-wrapper {
  position: relative;
  border: 1px solid rgba(139, 92, 246, 0.2);
  border-radius: 10px;
  background: rgba(7, 9, 15, 0.5);
  transition: border-color 0.3s, box-shadow 0.3s;
}
.input-wrapper:focus-within {
  border-color: rgba(139, 92, 246, 0.5);
  box-shadow: 0 0 0 3px rgba(139, 92, 246, 0.08);
}
.code-input {
  width: 100%;
  border: none;
  background: transparent;
  padding: 16px;
  font-family: 'Noto Sans', system-ui, sans-serif;
  font-size: 0.875rem;
  line-height: 1.75;
  resize: vertical;
  outline: none;
  min-height: 140px;
  color: #CBD5E1;
}
.code-input::placeholder { color: #2D3748; }
.model-badge {
  position: absolute;
  bottom: 10px;
  right: 14px;
  font-size: 0.62rem;
  color: #1E293B;
  letter-spacing: 2px;
  font-weight: 700;
}
.start-engine-btn {
  width: 100%;
  background: linear-gradient(135deg, #7C3AED 0%, #0E7490 100%);
  color: #F1F5F9;
  border: none;
  border-radius: 10px;
  padding: 18px 24px;
  font-family: 'Noto Sans', system-ui, sans-serif;
  font-weight: 700;
  font-size: 1rem;
  display: flex;
  justify-content: space-between;
  align-items: center;
  cursor: pointer;
  transition: all 0.3s ease;
  letter-spacing: 0.3px;
  position: relative;
  overflow: hidden;
}
.start-engine-btn::before {
  content: '';
  position: absolute;
  inset: 0;
  background: linear-gradient(135deg, #8B5CF6 0%, #22D3EE 100%);
  opacity: 0;
  transition: opacity 0.3s;
}
.start-engine-btn:hover:not(:disabled)::before { opacity: 1; }
.start-engine-btn > * { position: relative; z-index: 1; }
.start-engine-btn:disabled {
  background: rgba(30, 41, 59, 0.5);
  color: #334155;
  cursor: not-allowed;
}
.btn-arrow {
  font-size: 1.2rem;
  transition: transform 0.3s;
}
.start-engine-btn:hover:not(:disabled) .btn-arrow { transform: translateX(4px); }

/* ── Responsive ── */
@media (max-width: 1024px) {
  .dashboard-section { flex-direction: column; }
  .use-cases-grid { grid-template-columns: repeat(2, 1fr); }
  .hero-title { font-size: 3.8rem; }
}
@media (max-width: 640px) {
  .navbar { padding: 0 20px; }
  .hero-section,
  .use-cases-section,
  .dashboard-section { padding-left: 20px; padding-right: 20px; }
  .hero-title { font-size: 2.6rem; letter-spacing: -1.5px; }
  .hero-sub { font-size: 1rem; }
  .hero-metrics { flex-direction: column; gap: 14px; }
  .metric-sep { display: none; }
  .use-cases-grid { grid-template-columns: 1fr; }
}
</style>
