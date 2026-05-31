<template>
  <div class="settings-card">
    <div class="card-header">
      <div class="card-title">
        <Icon icon="mdi:information-outline" width="18" height="18" />
        {{ $t('about') }}
      </div>
    </div>
    <div class="card-body">
      <div class="setting-row">
        <span class="setting-label">{{ $t('version') }}</span>
        <div class="setting-control">
          <el-badge is-dot :hidden="!hasUpdate">
            <el-button @click="$emit('openRelease')">
              {{ currentVersion }}
              <template #icon>
                <Icon icon="qlementine-icons:version-control-16" style="font-size: 18px" color="#1890FF"/>
              </template>
            </el-button>
          </el-badge>
        </div>
      </div>
      
      <div class="setting-row">
        <span class="setting-label">{{ $t('community') }}</span>
        <div class="setting-control">
          <el-button @click="$emit('openGithub')">
            <template #icon>
              <Icon icon="codicon:github-inverted" width="18" height="18"/>
            </template>
            Github
          </el-button>
          <el-button @click="$emit('openTelegram')">
            <template #icon>
              <Icon icon="logos:telegram" width="18" height="18"/>
            </template>
            Telegram
          </el-button>
        </div>
      </div>
      
      <div class="setting-row" style="flex-direction: column; align-items: flex-start; gap: 12px;">
        <span class="setting-label">{{ $t('donate') }}</span>
        <div class="donate-box">
          <div class="donate-row">
            <span class="donate-chain bep20">BEP20</span>
            <code class="donate-addr" @click="$emit('copy', '0x555390f5c07cf76cc344f42612196e8669e3586b')">
              0x555390f5c07cf76cc344f42612196e8669e3586b
            </code>
            <el-tooltip :content="$t('copy')">
              <el-button circle size="small" plain @click="$emit('copy', '0x555390f5c07cf76cc344f42612196e8669e3586b')">
                <Icon icon="mdi:content-copy" width="12" height="12"/>
              </el-button>
            </el-tooltip>
          </div>
          <div class="donate-row">
            <span class="donate-chain trc20">TRC20</span>
            <code class="donate-addr" @click="$emit('copy', 'TVqK4thJCsaaWvp1Dah9F5CFZ1iqw75f4G')">
              TVqK4thJCsaaWvp1Dah9F5CFZ1iqw75f4G
            </code>
            <el-tooltip :content="$t('copy')">
              <el-button circle size="small" plain @click="$emit('copy', 'TVqK4thJCsaaWvp1Dah9F5CFZ1iqw75f4G')">
                <Icon icon="mdi:content-copy" width="12" height="12"/>
              </el-button>
            </el-tooltip>
          </div>
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
