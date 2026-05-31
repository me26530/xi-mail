<template>
  <div>
    <!-- OSS Storage -->
    <div class="settings-card">
      <div class="card-header">
        <div class="card-title">
          <Icon icon="mdi:cloud-outline" width="18" height="18" />
          {{ $t('oss') }}
        </div>
      </div>
      <div class="card-body">
        <div class="setting-row">
          <span class="setting-label">
            {{ $t('osDomain') }}
            <el-tooltip effect="dark" :content="$t('ossDomainDesc')">
              <Icon class="tip-icon" icon="fe:warning" width="16" height="16"/>
            </el-tooltip>
          </span>
          <div class="setting-control">
            <span class="setting-value" v-if="setting.r2Domain">{{ setting.r2Domain }}</span>
            <el-button class="action-btn" size="small" type="primary" @click="$emit('openR2Domain')">
              <Icon icon="lsicon:edit-outline" width="14" height="14"/>
            </el-button>
          </div>
        </div>
        
        <div class="setting-row">
          <span class="setting-label">{{ $t('s3Configuration') }}</span>
          <div class="setting-control">
            <el-button class="action-btn" size="small" type="primary" @click="$emit('openS3Config')">
              <Icon icon="fluent:settings-48-regular" width="14" height="14"/>
            </el-button>
          </div>
        </div>
        
        <div class="setting-row">
          <span class="setting-label">{{ $t('storageType') }}</span>
          <div class="setting-control">
            <el-tag>{{ setting.storageType }}</el-tag>
          </div>
        </div>
      </div>
    </div>

    <!-- Email Push -->
    <div class="settings-card">
      <div class="card-header">
        <div class="card-title">
          <Icon icon="mdi:bell-outline" width="18" height="18" />
          {{ $t('emailPush') }}
        </div>
      </div>
      <div class="card-body">
        <div class="setting-row">
          <span class="setting-label">{{ $t('tgBot') }}</span>
          <div class="setting-control">
            <el-tag :type="setting.tgBotStatus === 0 ? 'success' : 'info'" size="small">
              {{ setting.tgBotStatus === 0 ? $t('enabled') : $t('disabled') }}
            </el-tag>
            <el-button class="action-btn" size="small" type="primary" @click="$emit('openTgSetting')">
              <Icon icon="fluent:settings-48-regular" width="16" height="16"/>
            </el-button>
          </div>
        </div>
        
        <div class="setting-row">
          <span class="setting-label">{{ $t('otherEmail') }}</span>
          <div class="setting-control">
            <el-tag :type="setting.forwardStatus === 0 ? 'success' : 'info'" size="small">
              {{ setting.forwardStatus === 0 ? $t('enabled') : $t('disabled') }}
            </el-tag>
            <el-button class="action-btn" size="small" type="primary" @click="$emit('openThirdEmail')">
              <Icon icon="fluent:settings-48-regular" width="16" height="16"/>
            </el-button>
          </div>
        </div>
        
        <div class="setting-row">
          <span class="setting-label">{{ $t('forwardingRules') }}</span>
          <div class="setting-control">
            <el-tag size="small">{{ setting.ruleType === 0 ? $t('forwardAll') : $t('rules') }}</el-tag>
            <el-button class="action-btn" size="small" type="primary" @click="$emit('openForwardRules')">
              <Icon icon="fluent:settings-48-regular" width="16" height="16"/>
            </el-button>
          </div>
        </div>
      </div>
    </div>

    <!-- Turnstile Verification -->
    <div class="settings-card">
      <div class="card-header">
        <div class="card-title">
          <Icon icon="mdi:shield-check-outline" width="18" height="18" />
          {{ $t('turnstileSetting') }}
        </div>
      </div>
      <div class="card-body">
        <div class="setting-row">
          <span class="setting-label">{{ $t('signUpVerification') }}</span>
          <div class="setting-control">
            <el-button class="action-btn" size="small" type="primary" @click="$emit('openRegVerifyCount')">
              <Icon icon="fluent:settings-48-regular" width="16" height="16"/>
            </el-button>
            <el-select
              @change="$emit('change')"
              style="width: 90px;"
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
          <span class="setting-label">{{ $t('addEmailVerification') }}</span>
          <div class="setting-control">
            <el-button class="action-btn" size="small" type="primary" @click="$emit('openAddVerifyCount')">
              <Icon icon="fluent:settings-48-regular" width="16" height="16"/>
            </el-button>
            <el-select
              @change="$emit('change')"
              style="width: 90px;"
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
          <span class="setting-label">Site Key</span>
          <div class="setting-control">
            <span class="setting-value">{{ setting.siteKey || '-' }}</span>
            <el-button class="action-btn" size="small" type="primary" @click="$emit('openTurnstile')">
              <Icon icon="lsicon:edit-outline" width="14" height="14"/>
            </el-button>
          </div>
        </div>
        
        <div class="setting-row">
          <span class="setting-label">Secret Key</span>
          <div class="setting-control">
            <span class="setting-value">{{ setting.secretKey || '-' }}</span>
            <el-button class="action-btn" size="small" type="primary" @click="$emit('openTurnstile')">
              <Icon icon="lsicon:edit-outline" width="14" height="14"/>
            </el-button>
          </div>
        </div>
      </div>
    </div>

    <!-- Notice Popup -->
    <div class="settings-card">
      <div class="card-header">
        <div class="card-title">
          <Icon icon="mdi:bullhorn-outline" width="18" height="18" />
          {{ $t('noticeTitle') }}
        </div>
      </div>
      <div class="card-body">
        <div class="setting-row">
          <span class="setting-label">{{ $t('noticePopup') }}</span>
          <div class="setting-control">
            <el-tag :type="setting.notice === 0 ? 'success' : 'info'" size="small">
              {{ setting.notice === 0 ? $t('enabled') : $t('disabled') }}
            </el-tag>
            <el-button class="action-btn" size="small" type="primary" @click="$emit('openNoticeSetting')">
              <Icon icon="fluent:settings-48-regular" width="16" height="16"/>
            </el-button>
          </div>
        </div>
        
        <div class="setting-row">
          <span class="setting-label">{{ $t('popUp') }}</span>
          <div class="setting-control">
            <el-button class="action-btn" size="small" type="primary" @click="$emit('openNoticePopup')">
              <Icon icon="mynaui:click-solid" width="16" height="16"/>
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
