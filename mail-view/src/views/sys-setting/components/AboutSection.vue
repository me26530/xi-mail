<template>
  <div class="settings-card">
    <div class="card-header">
      <div>
        <h3 class="card-title">{{ $t('about') }}</h3>
        <p class="card-desc">{{ $t('aboutDesc') || 'Version information, community links, and support' }}</p>
      </div>
    </div>
    <div class="card-body">
      <!-- Version & Links -->
      <div class="about-grid">
        <div class="about-item" @click="$emit('openRelease')">
          <div class="about-icon">
            <Icon icon="mingcute:version-line" width="20" height="20"/>
          </div>
          <div class="about-info">
            <div class="about-label">{{ $t('version') }}</div>
            <div class="about-value">
              {{ currentVersion }}
              <el-badge v-if="hasUpdate" is-dot style="margin-left: 8px;" />
            </div>
          </div>
        </div>
        
        <div class="about-item" @click="$emit('openGithub')">
          <div class="about-icon" style="background: #24292f15; color: #24292f;">
            <Icon icon="mingcute:github-line" width="20" height="20"/>
          </div>
          <div class="about-info">
            <div class="about-label">GitHub</div>
            <div class="about-value">xi-mail</div>
          </div>
        </div>
        
        <div class="about-item" @click="$emit('openTelegram')">
          <div class="about-icon" style="background: #0088cc15; color: #0088cc;">
            <Icon icon="mingcute:telegram-line" width="20" height="20"/>
          </div>
          <div class="about-info">
            <div class="about-label">Telegram</div>
            <div class="about-value">{{ $t('community') }}</div>
          </div>
        </div>
      </div>
      
      <!-- Donate Section -->
      <div class="donate-section">
        <div class="appearance-label" style="margin-bottom: 14px;">{{ $t('donate') }}</div>
        <div class="donate-row">
          <span class="donate-chain bep20">BEP20</span>
          <code class="donate-addr" @click="$emit('copy', '0x555390f5c07cf76cc344f42612196e8669e3586b')">
            0x555390f5c07cf76cc344f42612196e8669e3586b
          </code>
          <el-button size="small" text @click="$emit('copy', '0x555390f5c07cf76cc344f42612196e8669e3586b')">
            <Icon icon="mingcute:copy-2-line" width="14" height="14"/>
          </el-button>
        </div>
        <div class="donate-row">
          <span class="donate-chain trc20">TRC20</span>
          <code class="donate-addr" @click="$emit('copy', 'TVqK4thJCsaaWvp1Dah9F5CFZ1iqw75f4G')">
            TVqK4thJCsaaWvp1Dah9F5CFZ1iqw75f4G
          </code>
          <el-button size="small" text @click="$emit('copy', 'TVqK4thJCsaaWvp1Dah9F5CFZ1iqw75f4G')">
            <Icon icon="mingcute:copy-2-line" width="14" height="14"/>
          </el-button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { Icon } from '@iconify/vue'

defineProps({
  currentVersion: { type: String, required: true },
  hasUpdate: { type: Boolean, default: false }
})

defineEmits(['openRelease', 'openGithub', 'openTelegram', 'copy'])
</script>
