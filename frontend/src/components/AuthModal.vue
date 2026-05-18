<template>
  <Teleport to="body">
    <Transition name="modal">
      <div v-if="show" class="modal-overlay" @click.self="$emit('close')">
        <div class="modal-box">
          <button class="close-btn" @click="$emit('close')" aria-label="Close">✕</button>

          <div class="modal-logo">
            <svg viewBox="0 0 44 44" fill="none" width="40" height="40">
              <defs>
                <linearGradient id="mGrad" x1="0" y1="0" x2="44" y2="44" gradientUnits="userSpaceOnUse">
                  <stop offset="0%" stop-color="#8B5CF6"/>
                  <stop offset="100%" stop-color="#22D3EE"/>
                </linearGradient>
              </defs>
              <path d="M22 2 L38 11.5 L38 32.5 L22 42 L6 32.5 L6 11.5 Z" stroke="url(#mGrad)" stroke-width="1.5" fill="none" stroke-linejoin="round"/>
              <path d="M22 10 L30 22 L22 34 L14 22 Z" stroke="url(#mGrad)" stroke-width="1.5" fill="rgba(139,92,246,0.1)" stroke-linejoin="round"/>
              <circle cx="22" cy="22" r="3.5" fill="url(#mGrad)"/>
            </svg>
          </div>

          <h2 class="modal-title">{{ isLogin ? 'Welcome back' : 'Join Chanakya' }}</h2>
          <p class="modal-sub">
            {{ isLogin
              ? 'Pick up where you left off.'
              : 'Your first simulation is one click away.' }}
          </p>

          <form @submit.prevent="handleSubmit" class="auth-form" novalidate>
            <div class="field">
              <label for="auth-email">Email address</label>
              <input
                id="auth-email"
                v-model="form.email"
                type="email"
                placeholder="you@company.com"
                autocomplete="email"
                required
                :disabled="loading"
              />
            </div>
            <div class="field">
              <label for="auth-password">Password</label>
              <input
                id="auth-password"
                v-model="form.password"
                type="password"
                placeholder="Min. 8 characters"
                autocomplete="current-password"
                required
                minlength="8"
                :disabled="loading"
              />
            </div>
            <div v-if="!isLogin" class="field">
              <label for="auth-confirm">Confirm password</label>
              <input
                id="auth-confirm"
                v-model="form.confirm"
                type="password"
                placeholder="Repeat your password"
                autocomplete="new-password"
                required
                :disabled="loading"
              />
            </div>

            <p v-if="errorMsg" class="field-error" role="alert">{{ errorMsg }}</p>
            <p v-if="successMsg" class="field-success" role="status">{{ successMsg }}</p>

            <button type="submit" class="auth-btn" :disabled="loading">
              <span v-if="!loading">{{ isLogin ? 'Sign in →' : 'Create account →' }}</span>
              <span v-else class="loading-dots">Please wait</span>
            </button>
          </form>

          <p class="auth-switch">
            {{ isLogin ? "New here?" : "Already have an account?" }}
            <button class="link-btn" @click="toggle" type="button">
              {{ isLogin ? 'Sign up free' : 'Sign in' }}
            </button>
          </p>

          <p class="auth-legal">
            By continuing you agree to Chanakya's
            <a href="#" @click.prevent>Terms of Service</a> and
            <a href="#" @click.prevent>Privacy Policy</a>.
          </p>
        </div>
      </div>
    </Transition>
  </Teleport>
</template>

<script setup>
import { ref, reactive } from 'vue'

defineProps({ show: Boolean })
const emit = defineEmits(['close', 'auth'])

const isLogin = ref(false)
const loading = ref(false)
const errorMsg = ref('')
const successMsg = ref('')
const form = reactive({ email: '', password: '', confirm: '' })

const toggle = () => {
  isLogin.value = !isLogin.value
  errorMsg.value = ''
  successMsg.value = ''
}

const handleSubmit = async () => {
  errorMsg.value = ''
  successMsg.value = ''

  if (!form.email.includes('@')) {
    errorMsg.value = 'Please enter a valid email address.'
    return
  }
  if (form.password.length < 8) {
    errorMsg.value = 'Password must be at least 8 characters.'
    return
  }
  if (!isLogin.value && form.password !== form.confirm) {
    errorMsg.value = 'Passwords do not match.'
    return
  }

  loading.value = true
  await new Promise(r => setTimeout(r, 900))
  loading.value = false

  successMsg.value = isLogin.value
    ? 'Signed in! Setting up your workspace...'
    : 'Account created! Welcome to Chanakya.'

  setTimeout(() => {
    emit('auth', { email: form.email })
    emit('close')
  }, 1200)
}
</script>

<style scoped>
.modal-overlay {
  position: fixed;
  inset: 0;
  background: rgba(0, 0, 0, 0.6);
  backdrop-filter: blur(12px);
  -webkit-backdrop-filter: blur(12px);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1000;
  padding: 20px;
}

.modal-box {
  position: relative;
  background: var(--surface);
  border: 1px solid var(--border);
  border-radius: 24px;
  padding: 40px 36px 32px;
  width: 100%;
  max-width: 420px;
  box-shadow:
    0 0 0 1px rgba(139, 92, 246, 0.08),
    0 32px 80px rgba(0, 0, 0, 0.4),
    0 0 60px rgba(139, 92, 246, 0.1);
  backdrop-filter: blur(24px);
  -webkit-backdrop-filter: blur(24px);
}

.close-btn {
  position: absolute;
  top: 16px;
  right: 16px;
  background: none;
  border: none;
  color: var(--text-muted);
  font-size: 1rem;
  cursor: pointer;
  padding: 6px 8px;
  border-radius: 8px;
  transition: color 0.2s, background 0.2s;
  line-height: 1;
}
.close-btn:hover {
  color: var(--text);
  background: var(--border);
}

.modal-logo {
  display: flex;
  justify-content: center;
  margin-bottom: 20px;
}

.modal-title {
  font-size: 1.5rem;
  font-weight: 800;
  color: var(--text);
  text-align: center;
  margin-bottom: 6px;
  letter-spacing: -0.5px;
}

.modal-sub {
  font-size: 0.88rem;
  color: var(--text-muted);
  text-align: center;
  margin-bottom: 28px;
  line-height: 1.5;
}

.auth-form { display: flex; flex-direction: column; gap: 16px; }

.field { display: flex; flex-direction: column; gap: 6px; }

.field label {
  font-size: 0.78rem;
  font-weight: 700;
  color: var(--text-muted);
  letter-spacing: 0.5px;
  text-transform: uppercase;
}

.field input {
  background: var(--input-bg);
  border: 1px solid var(--border);
  border-radius: 10px;
  padding: 12px 14px;
  font-family: inherit;
  font-size: 0.9rem;
  color: var(--text);
  outline: none;
  transition: border-color 0.2s, box-shadow 0.2s;
  width: 100%;
}
.field input::placeholder { color: var(--text-muted); opacity: 0.6; }
.field input:focus {
  border-color: var(--primary);
  box-shadow: 0 0 0 3px rgba(139, 92, 246, 0.12);
}
.field input:disabled { opacity: 0.5; cursor: not-allowed; }

.field-error {
  font-size: 0.82rem;
  color: #F87171;
  background: rgba(248, 113, 113, 0.08);
  border: 1px solid rgba(248, 113, 113, 0.2);
  border-radius: 8px;
  padding: 8px 12px;
}
.field-success {
  font-size: 0.82rem;
  color: #34D399;
  background: rgba(52, 211, 153, 0.08);
  border: 1px solid rgba(52, 211, 153, 0.2);
  border-radius: 8px;
  padding: 8px 12px;
}

.auth-btn {
  width: 100%;
  background: linear-gradient(135deg, #7C3AED 0%, #0891B2 100%);
  color: #fff;
  border: none;
  border-radius: 12px;
  padding: 14px;
  font-family: inherit;
  font-size: 0.95rem;
  font-weight: 700;
  cursor: pointer;
  transition: all 0.3s;
  margin-top: 4px;
  position: relative;
  overflow: hidden;
}
.auth-btn::before {
  content: '';
  position: absolute;
  inset: 0;
  background: linear-gradient(135deg, #8B5CF6, #22D3EE);
  opacity: 0;
  transition: opacity 0.3s;
}
.auth-btn:hover:not(:disabled)::before { opacity: 1; }
.auth-btn > * { position: relative; }
.auth-btn:disabled { opacity: 0.5; cursor: not-allowed; }

.loading-dots::after {
  content: '...';
  animation: dots 1s steps(3, end) infinite;
}
@keyframes dots {
  0%   { content: '.'; }
  33%  { content: '..'; }
  66%  { content: '...'; }
}

.auth-switch {
  text-align: center;
  font-size: 0.85rem;
  color: var(--text-muted);
  margin-top: 20px;
}
.link-btn {
  background: none;
  border: none;
  color: var(--primary);
  font-family: inherit;
  font-size: inherit;
  font-weight: 700;
  cursor: pointer;
  padding: 0 2px;
  text-decoration: underline;
  text-underline-offset: 2px;
}

.auth-legal {
  text-align: center;
  font-size: 0.72rem;
  color: var(--text-muted);
  margin-top: 16px;
  line-height: 1.5;
  opacity: 0.6;
}
.auth-legal a { color: var(--primary); text-decoration: none; }

/* Transition */
.modal-enter-active, .modal-leave-active { transition: opacity 0.25s ease; }
.modal-enter-active .modal-box, .modal-leave-active .modal-box { transition: transform 0.25s ease, opacity 0.25s ease; }
.modal-enter-from, .modal-leave-to { opacity: 0; }
.modal-enter-from .modal-box, .modal-leave-to .modal-box { transform: scale(0.95) translateY(10px); opacity: 0; }
</style>
