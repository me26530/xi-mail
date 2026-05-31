<template>
  <div>
    <!-- OSS Storage -->
    <div class="settings-card">
      <div class="card-header">
        <div>
          <h3 class="card-title">{{ $t('oss') }}</h3>
          <p class="card-desc">{{ $t('ossDesc') || 'Configure object storage for attachments and files' }}</p>
        </div>
      </div>
      <div class="card-body">
        <div class="setting-row">
          <div class="setting-info">
            <span class="setting-label">
              {{ $t('osDomain') }}
              <el-tooltip effect="dark" :content="$t('ossDomainDesc')">
                <Icon class="tip-icon" icon="mingcute:information-line" width="15" height="15"/>
              </el-tooltip>
            </span>
            <span class="setting-desc" v-if="setting.r2Domain">{{ setting.r2Domain }}</span>
            <span class="setting-desc" v-else>{{ $t('notConfigured') || 'Not configured' }}</span>
          </div>
          <div class="setting-control">
            <el-button size="small" @click="$emit('openR2Domain')">
              {{ $t('configure') || 'Configure' }}
            </el-button>
          </div>
        </div>
        
        <div class="setting-row">
          <div class="setting-info">
            <span class="setting-label">{{ $t('s3Configuration') }}</span>
            <span class="setting-desc">{{ $t('s3ConfigurationDesc') || 'Configure S3-compatible storage' }}</span>
          </div>
          <div class="setting-control">
            <el-button size="small" @click="$emit('openS3Config')">
              {{ $t('configure') || 'Configure' }}
            </el-button>
          </div>
        </div>
        
        <div class="setting-row">
          <div class="setting-info">
            <span class="setting-label">{{ $t('storageType') }}</span>
            <span class="setting-desc">{{ $t('storageTypeDesc') || 'Current storage backend type' }}</span>
          </div>
          <div class="setting-control">
            <el-tag type="info">{{ setting.storageType || 'Default' }}</el-tag>
          </div>
        </div>
      </div>
    </div>

    <!-- Email Push -->
    <div class="settings-card">
      <div class="card-header">
        <div>
          <h3 class="card-title">{{ $t('emailPush') }}</h3>
          <p class="card-desc">{{ $t('emailPushDesc') || 'Configure email forwarding and notifications' }}</p>
        </div>
      </div>
      <div class="card-body">
        <div class="setting-row">
          <div class="setting-info">
            <span class="setting-label">{{ $t('tgBot') }}</span>
            <span class="setting-desc">{{ $t('tgBotDesc') || 'Forward emails to Telegram bot' }}</span>
          </div>
          <div class="setting-control">
            <el-tag :type="setting.tgBotStatus === 0 ? 'success' : 'info'" size="small">
              {{ setting.tgBotStatus === 0 ? $t('enabled') : $t('disabled') }}
            </el-tag>
            <el-button size="small" @click="$emit('openTgSetting')">
              {{ $t('configure') || 'Configure' }}
            </el-button>
          </div>
        </div>
        
        <div class="setting-row">
          <div class="setting-info">
            <span class="setting-label">{{ $t('otherEmail') }}</span>
            <span class="setting-desc">{{ $t('otherEmailDesc') || 'Forward emails to other addresses' }}</span>
          </div>
          <div class="setting-control">
            <el-tag :type="setting.forwardStatus === 0 ? 'success' : 'info'" size="small">
              {{ setting.forwardStatus === 0 ? $t('enabled') : $t('disabled') }}
            </el-tag>
            <el-button size="small" @click="$emit('openThirdEmail')">
              {{ $t('configure') || 'Configure' }}
            </el-button>
          </div>
        </div>
        
        <div class="setting-row">
          <div class="setting-info">
            <span class="setting-label">{{ $t('forwardingRules') }}</span>
            <span class="setting-desc">{{ $t('forwardingRulesDesc') || 'Set rules for email forwarding' }}</span>
          </div>
          <div class="setting-control">
            <el-tag size="small" type="info">{{ setting.ruleType === 0 ? $t('forwardAll') : $t('rules') }}</el-tag>
            <el-button size="small" @click="$emit('openForwardRules')">
              {{ $t('configure') || 'Configure' }}
            </el-button>
          </div>
        </div>
      </div>
    </div>

    <!-- Turnstile Verification -->
    <div class="settings-card">
      <div class="card-header">
        <div>
          <h3 class="card-title">{{ $t('turnstileSetting') }}</h3>
          <p class="card-desc">{{ $t('turnstileSettingDesc') || 'Configure Cloudflare Turnstile for CAPTCHA verification' }}</p>
        </div>
      </div>
      <div class="card-body">
        <div class="setting-row">
          <div class="setting-info">
            <span class="setting-label">{{ $t('signUpVerification') }}</span>
            <span class="setting-desc">{{ $t('signUpVerificationDesc') || 'Verify users during registration' }}</span>
          </div>
          <div class="setting-control">
            <el-button size="small" text @click="$emit('openRegVerifyCount')">
              <Icon icon="mingcute:settings-3-line" width="16" height="16"/>
            </el-button>
            <el-select
              @change="$emit('change')"
              style="width: 110px;"
              v-model="setting.registerVerify"
              placeholder="Select"
            >
              <el-option :value="0" :label="$t('enable')"/>
              <el-option :value="1" :label="$t('disable')"/>
              <el-option :value="2" :label="$t('rulesVerify')"/>
            </el-select>
          </div>
        </div>
        
        <div class="setting-row">
          <div class="setting-info">
            <span class="setting-label">{{ $t('addEmailVerification') }}</span>
            <span class="setting-desc">{{ $t('addEmailVerificationDesc') || 'Verify users when adding new email' }}</span>
          </div>
          <div class="setting-control">
            <el-button size="small" text @click="$emit('openAddVerifyCount')">
              <Icon icon="mingcute:settings-3-line" width="16" height="16"/>
            </el-button>
            <el-select
              @change="$emit('change')"
              style="width: 110px;"
              v-model="setting.addEmailVerify"
              placeholder="Select"
            >
              <el-option :value="0" :label="$t('enable')"/>
              <el-option :value="1" :label="$t('disable')"/>
              <el-option :value="2" :label="$t('rulesVerify')"/>
            </el-select>
          </div>
        </div>
        
        <div class="setting-row">
          <div class="setting-info">
            <span class="setting-label">Site Key / Secret Key</span>
            <span class="setting-desc">{{ setting.siteKey ? $t('configured') || 'Configured' : $t('notConfigured') || 'Not configured' }}</span>
          </div>
          <div class="setting-control">
            <el-button size="small" @click="$emit('openTurnstile')">
              {{ $t('configure') || 'Configure' }}
            </el-button>
          </div>
        </div>
      </div>
    </div>

    <!-- Notice Popup -->
    <div class="settings-card">
      <div class="card-header">
        <div>
          <h3 class="card-title">{{ $t('noticeTitle') }}</h3>
          <p class="card-desc">{{ $t('noticeTitleDesc') || 'Configure announcement and notice popups' }}</p>
        </div>
      </div>
      <div class="card-body">
        <div class="setting-row">
          <div class="setting-info">
            <span class="setting-label">{{ $t('noticePopup') }}</span>
            <span class="setting-desc">{{ $t('noticePopupDesc') || 'Show popup notice to users' }}</span>
          </div>
          <div class="setting-control">
            <el-tag :type="setting.notice === 0 ? 'success' : 'info'" size="small">
              {{ setting.notice === 0 ? $t('enabled') : $t('disabled') }}
            </el-tag>
            <el-button size="small" @click="$emit('openNoticeSetting')">
              {{ $t('configure') || 'Configure' }}
            </el-button>
          </div>
        </div>
        
        <div class="setting-row">
          <div class="setting-info">
            <span class="setting-label">{{ $t('popUp') }}</span>
            <span class="setting-desc">{{ $t('popUpDesc') || 'Preview the notice popup' }}</span>
          </div>
          <div class="setting-control">
            <el-button size="small" @click="$emit('openNoticePopup')">
              {{ $t('preview') || 'Preview' }}
            </el-button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { Icon } from '@iconify/vue'

defineProps({
  setting: { type: Object, required: true }
})

defineEmits([
  'change',
  'openR2Domain',
  'openS3Config',
  'openTgSetting',
  'openThirdEmail',
  'openForwardRules',
  'openRegVerifyCount',
  'openAddVerifyCount',
  'openTurnstile',
  'openNoticeSetting',
  'openNoticePopup'
])
</script>
