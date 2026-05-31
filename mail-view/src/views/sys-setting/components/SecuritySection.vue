<template>
  <div>
    <!-- Security Settings -->
    <div class="settings-card">
      <div class="card-header">
        <div>
          <h3 class="card-title">{{ $t('securitySetting') }}</h3>
          <p class="card-desc">{{ $t('securitySettingDesc') || 'Configure account security and protection rules' }}</p>
        </div>
      </div>
      <div class="card-body">
        <div class="setting-row">
          <div class="setting-info">
            <span class="setting-label">
              {{ $t('autoBan') }}
              <el-tooltip effect="dark" :content="$t('autoBanDesc')">
                <Icon class="tip-icon" icon="mingcute:information-line" width="15" height="15"/>
              </el-tooltip>
            </span>
            <span class="setting-desc">{{ $t('autoBanShortDesc') || 'Automatically ban inactive accounts' }}</span>
          </div>
          <div class="setting-control">
            <el-input-number 
              v-model="setting.autoBanMonths" 
              @change="$emit('change')" 
              :min="0" 
              :max="120" 
              :step="1" 
              style="width: 100px;" 
            />
            <span style="font-size: 13px; color: var(--el-text-color-secondary); margin-left: 8px;">{{ $t('month') }}</span>
          </div>
        </div>
        
        <div class="setting-row">
          <div class="setting-info">
            <span class="setting-label">
              {{ $t('banMessage') }}
              <el-tooltip effect="dark" :content="$t('banMessageDesc')">
                <Icon class="tip-icon" icon="mingcute:information-line" width="15" height="15"/>
              </el-tooltip>
            </span>
            <span class="setting-desc">{{ $t('banMessageShortDesc') || 'Message shown to banned users' }}</span>
          </div>
          <div class="setting-control">
            <el-input v-model="setting.banMessage" @change="$emit('change')" style="width: 200px;" />
          </div>
        </div>
        
        <div class="setting-row">
          <div class="setting-info">
            <span class="setting-label">{{ $t('emailKeywordBlacklist') }}</span>
            <span class="setting-desc">{{ $t('emailKeywordBlacklistDesc') || 'Block emails containing specific keywords' }}</span>
          </div>
          <div class="setting-control">
            <el-button size="small" @click="$emit('openKeywordBlacklist')">
              {{ $t('configure') || 'Configure' }}
            </el-button>
          </div>
        </div>
        
        <div class="setting-row">
          <div class="setting-info">
            <span class="setting-label">{{ $t('senderDomainBlacklist') }}</span>
            <span class="setting-desc">{{ $t('senderDomainBlacklistDesc') || 'Block emails from specific domains' }}</span>
          </div>
          <div class="setting-control">
            <el-button size="small" @click="$emit('openSenderDomainBlacklist')">
              {{ $t('configure') || 'Configure' }}
            </el-button>
          </div>
        </div>
      </div>
    </div>

    <!-- Global API Token -->
    <div class="settings-card">
      <div class="card-header">
        <div>
          <h3 class="card-title">{{ $t('globalToken') }}</h3>
          <p class="card-desc">{{ $t('globalTokenDesc') || 'API token for external access to mail data' }}</p>
        </div>
        <el-switch :model-value="globalTokenEnabled" @change="$emit('globalTokenEnabledChange', $event)" />
      </div>
      <div class="card-body">
        <template v-if="globalTokenEnabled">
          <div class="setting-row" style="flex-wrap: wrap;">
            <div class="code-box" style="flex: 1; min-width: 280px;">
              <Icon icon="mingcute:key-2-line" class="code-icon" width="16" height="16"/>
              <span class="code-text" :class="{ masked: !globalTokenVisible }">
                {{ globalTokenVisible ? (globalToken || $t('noToken')) : (globalToken ? '••••••••••••••••••••••••••••••••' : $t('noToken')) }}
              </span>
              <div class="code-actions">
                <el-tooltip :content="globalTokenVisible ? $t('hide') : $t('show')">
                  <el-button size="small" text @click="$emit('toggleTokenVisible')">
                    <Icon :icon="globalTokenVisible ? 'mingcute:eye-close-line' : 'mingcute:eye-2-line'" width="16" height="16"/>
                  </el-button>
                </el-tooltip>
                <el-tooltip :content="$t('copy')" v-if="globalToken">
                  <el-button size="small" text @click="$emit('copyToken')">
                    <Icon icon="mingcute:copy-2-line" width="16" height="16"/>
                  </el-button>
                </el-tooltip>
              </div>
            </div>
            <el-button type="primary" @click="$emit('generateToken')" :loading="globalTokenGenerating">
              {{ globalToken ? $t('regenerate') : $t('generate') }}
            </el-button>
          </div>
          
          <div class="api-section">
            <div class="api-title">{{ $t('globalTokenApiHint') || 'API Usage' }}</div>
            <div class="api-line">
              <span class="api-method">GET</span>
              <code>/api/admin/mails?limit=20&amp;offset=0&amp;address=xxx@domain.com</code>
            </div>
            <div class="api-line">
              <span class="api-label">Header</span>
              <code>x-admin-auth: {{ globalTokenVisible && globalToken ? globalToken : '&lt;your-token&gt;' }}</code>
            </div>
            <div class="api-line">
              <span class="api-label">{{ $t('globalTokenResp') || 'Response' }}</span>
              <code>{ "results": [...], "count": N }</code>
            </div>
          </div>
        </template>
        
        <div v-else class="disabled-state">
          <Icon icon="mingcute:lock-line" width="20" height="20"/>
          <span>{{ $t('globalTokenDisabledTip') || 'Enable to use the Global API Token' }}</span>
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
