<template>
  <div>
    <!-- Security Settings -->
    <div class="settings-card">
      <div class="card-header">
        <div class="card-title">
          <Icon icon="mdi:shield-lock-outline" width="18" height="18" />
          {{ $t('securitySetting') }}
        </div>
      </div>
      <div class="card-body">
        <div class="setting-row">
          <span class="setting-label">
            {{ $t('autoBan') }}
            <el-tooltip effect="dark" :content="$t('autoBanDesc')">
              <Icon class="tip-icon" icon="fe:warning" width="16" height="16"/>
            </el-tooltip>
          </span>
          <div class="setting-control">
            <el-input-number 
              v-model="setting.autoBanMonths" 
              @change="$emit('change')" 
              :min="0" 
              :max="120" 
              :step="1" 
              style="width: 100px;" 
            />
            <span style="font-size: 13px; color: var(--el-text-color-secondary);">{{ $t('month') }}</span>
          </div>
        </div>
        
        <div class="setting-row">
          <span class="setting-label">
            {{ $t('banMessage') }}
            <el-tooltip effect="dark" :content="$t('banMessageDesc')">
              <Icon class="tip-icon" icon="fe:warning" width="16" height="16"/>
            </el-tooltip>
          </span>
          <div class="setting-control">
            <el-input v-model="setting.banMessage" @change="$emit('change')" style="width: 180px;" />
          </div>
        </div>
        
        <div class="setting-row">
          <span class="setting-label">
            {{ $t('emailKeywordBlacklist') }}
            <el-tooltip effect="dark" :content="$t('emailKeywordBlacklistDesc')">
              <Icon class="tip-icon" icon="fe:warning" width="16" height="16"/>
            </el-tooltip>
          </span>
          <div class="setting-control">
            <el-button class="action-btn" size="small" type="primary" @click="$emit('openKeywordBlacklist')">
              <Icon icon="fluent:settings-48-regular" width="16" height="16"/>
            </el-button>
          </div>
        </div>
        
        <div class="setting-row">
          <span class="setting-label">
            {{ $t('senderDomainBlacklist') }}
            <el-tooltip effect="dark" :content="$t('senderDomainBlacklistDesc')">
              <Icon class="tip-icon" icon="fe:warning" width="16" height="16"/>
            </el-tooltip>
          </span>
          <div class="setting-control">
            <el-button class="action-btn" size="small" type="primary" @click="$emit('openSenderDomainBlacklist')">
              <Icon icon="fluent:settings-48-regular" width="16" height="16"/>
            </el-button>
          </div>
        </div>
      </div>
    </div>

    <!-- Global API Token -->
    <div class="settings-card">
      <div class="card-header">
        <div class="card-title">
          <Icon icon="mdi:key-variant" width="18" height="18" />
          {{ $t('globalToken') }}
        </div>
        <el-switch v-model="globalTokenEnabled" @change="$emit('globalTokenEnabledChange', $event)" />
      </div>
      <div class="card-body">
        <div class="setting-row" style="flex-direction: column; align-items: flex-start;">
          <p style="font-size: 13px; color: var(--el-text-color-secondary); margin: 0 0 12px 0; line-height: 1.5;">
            {{ $t('globalTokenDesc') }}
          </p>
        </div>
        
        <template v-if="globalTokenEnabled">
          <div class="setting-row">
            <div class="code-box" style="flex: 1;">
              <Icon icon="mdi:key-variant" width="15" height="15" style="color: var(--el-color-primary); opacity: 0.7;"/>
              <span class="code-text" :class="{ masked: !globalTokenVisible }">
                {{ globalTokenVisible ? (globalToken || $t('noToken')) : (globalToken ? '••••••••••••••••••••••••••••••••' : $t('noToken')) }}
              </span>
            </div>
            <div class="setting-control">
              <el-tooltip :content="globalTokenVisible ? $t('hide') : $t('show')">
                <el-button size="small" circle plain @click="$emit('toggleTokenVisible')">
                  <Icon :icon="globalTokenVisible ? 'mdi:eye-off-outline' : 'mdi:eye-outline'" width="14" height="14"/>
                </el-button>
              </el-tooltip>
              <el-tooltip :content="$t('copy')" v-if="globalToken">
                <el-button size="small" circle plain @click="$emit('copyToken')">
                  <Icon icon="mdi:content-copy" width="14" height="14"/>
                </el-button>
              </el-tooltip>
              <el-button size="small" type="primary" @click="$emit('generateToken')" :loading="globalTokenGenerating">
                <Icon :icon="globalToken ? 'mdi:refresh' : 'mdi:plus'" width="14" height="14" style="margin-right:4px"/>
                {{ globalToken ? $t('regenerate') : $t('generate') }}
              </el-button>
            </div>
          </div>
          
          <div class="api-box">
            <div class="api-title">{{ $t('globalTokenApiHint') }}</div>
            <div class="api-line">
              <span class="api-method">GET</span>
              <code>/api/admin/mails?limit=20&amp;offset=0&amp;address=xxx@domain.com</code>
            </div>
            <div class="api-line">
              <span class="api-label">Header</span>
              <code>x-admin-auth: {{ globalTokenVisible && globalToken ? globalToken : '&lt;your-token&gt;' }}</code>
            </div>
            <div class="api-line">
              <span class="api-label">{{ $t('globalTokenResp') }}</span>
              <code>{ "results": [...], "count": N }</code>
            </div>
          </div>
        </template>
        
        <div v-else class="empty-state" style="padding: 20px;">
          <Icon icon="mdi:lock-outline" width="24" height="24"/>
          <span>{{ $t('globalTokenDisabledTip') }}</span>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { Icon } from '@iconify/vue'

defineProps({
  setting: { type: Object, required: true },
  globalTokenEnabled: { type: Boolean, default: false },
  globalToken: { type: String, default: '' },
  globalTokenVisible: { type: Boolean, default: false },
  globalTokenGenerating: { type: Boolean, default: false }
})

defineEmits([
  'change', 
  'openKeywordBlacklist', 
  'openSenderDomainBlacklist',
  'globalTokenEnabledChange',
  'toggleTokenVisible',
  'copyToken',
  'generateToken'
])
</script>
