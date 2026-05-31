<template>
  <div>
    <!-- Email Address Settings -->
    <div class="settings-card">
      <div class="card-header">
        <div>
          <h3 class="card-title">{{ $t('emailAddressSetting') }}</h3>
          <p class="card-desc">{{ $t('emailAddressSettingDesc') || 'Configure email address format and mapping rules' }}</p>
        </div>
      </div>
      <div class="card-body">
        <div class="setting-row">
          <div class="setting-info">
            <span class="setting-label">{{ $t('emailPrefix') }}</span>
            <span class="setting-desc">{{ $t('emailPrefixDesc') || 'Set minimum prefix length and character restrictions' }}</span>
          </div>
          <div class="setting-control">
            <el-button size="small" @click="$emit('openEmailPrefix')">
              {{ $t('configure') || 'Configure' }}
            </el-button>
          </div>
        </div>
        
        <div class="setting-row">
          <div class="setting-info">
            <span class="setting-label">
              {{ $t('randomPrefixLength') }}
              <el-tooltip effect="dark" :content="$t('randomPrefixDesc')">
                <Icon class="tip-icon" icon="mingcute:information-line" width="15" height="15"/>
              </el-tooltip>
            </span>
            <span class="setting-desc">{{ $t('randomPrefixLengthDesc') || 'Length of randomly generated email prefix' }}</span>
          </div>
          <div class="setting-control">
            <el-input-number 
              v-model="setting.randomPrefixLength" 
              @change="$emit('change')" 
              :min="4" 
              :max="32" 
              :step="1" 
              style="width: 100px;" 
            />
          </div>
        </div>
        
        <div class="setting-row">
          <div class="setting-info">
            <span class="setting-label">
              {{ $t('domainMapping') }}
              <el-tooltip effect="dark" :content="$t('domainMappingDesc')">
                <Icon class="tip-icon" icon="mingcute:information-line" width="15" height="15"/>
              </el-tooltip>
            </span>
            <span class="setting-desc">{{ $t('domainMappingShortDesc') || 'Map internal domains to display domains' }}</span>
          </div>
          <div class="setting-control">
            <el-button size="small" @click="$emit('openDomainMapping')">
              {{ $t('configure') || 'Configure' }}
            </el-button>
          </div>
        </div>
      </div>
    </div>

    <!-- Email Sending Settings -->
    <div class="settings-card">
      <div class="card-header">
        <div>
          <h3 class="card-title">{{ $t('emailSetting') }}</h3>
          <p class="card-desc">{{ $t('emailSettingDesc') || 'Configure email receiving and sending behavior' }}</p>
        </div>
      </div>
      <div class="card-body">
        <div class="setting-row">
          <div class="setting-info">
            <span class="setting-label">{{ $t('receiveEmail') }}</span>
            <span class="setting-desc">{{ $t('receiveEmailDesc') || 'Allow users to receive incoming emails' }}</span>
          </div>
          <div class="setting-control">
            <el-switch 
              @change="$emit('change')" 
              :before-change="beforeChange" 
              :active-value="0" 
              :inactive-value="1"
              v-model="setting.receive"
            />
          </div>
        </div>
        
        <div class="setting-row">
          <div class="setting-info">
            <span class="setting-label">
              {{ $t('autoRefresh') }}
              <el-tooltip effect="dark" :content="$t('autoRefreshDesc')">
                <Icon class="tip-icon" icon="mingcute:information-line" width="15" height="15"/>
              </el-tooltip>
            </span>
            <span class="setting-desc">{{ $t('autoRefreshShortDesc') || 'Auto-refresh inbox at specified interval' }}</span>
          </div>
          <div class="setting-control">
            <el-select
              @change="$emit('change')"
              style="width: 100px;"
              v-model="setting.autoRefresh"
              placeholder="Select"
            >
              <el-option
                v-for="item in authRefreshOptions"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              />
            </el-select>
          </div>
        </div>
        
        <div class="setting-row">
          <div class="setting-info">
            <span class="setting-label">{{ $t('sendEmail') }}</span>
            <span class="setting-desc">{{ $t('sendEmailDesc') || 'Allow users to send outgoing emails' }}</span>
          </div>
          <div class="setting-control">
            <el-switch 
              @change="$emit('change')" 
              :before-change="beforeChange" 
              :active-value="0" 
              :inactive-value="1"
              v-model="setting.send"
            />
          </div>
        </div>
        
        <div class="setting-row">
          <div class="setting-info">
            <span class="setting-label">
              {{ $t('noRecipientTitle') }}
              <el-tooltip effect="dark" :content="$t('noRecipientDesc')">
                <Icon class="tip-icon" icon="mingcute:information-line" width="15" height="15"/>
              </el-tooltip>
            </span>
            <span class="setting-desc">{{ $t('noRecipientShortDesc') || 'Accept emails without specific recipient' }}</span>
          </div>
          <div class="setting-control">
            <el-switch 
              @change="$emit('change')" 
              :before-change="beforeChange" 
              :active-value="0" 
              :inactive-value="1"
              v-model="setting.noRecipient"
            />
          </div>
        </div>
        
        <div class="setting-row">
          <div class="setting-info">
            <span class="setting-label">{{ $t('resendToken') }}</span>
            <span class="setting-desc">{{ $t('resendTokenDesc') || 'Manage Resend API tokens for sending emails' }}</span>
          </div>
          <div class="setting-control">
            <el-button size="small" @click="$emit('openResendList')">
              {{ $t('viewList') || 'View List' }}
            </el-button>
            <el-button size="small" type="primary" @click="$emit('openResendForm')">
              {{ $t('add') || 'Add' }}
            </el-button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { Icon } from '@iconify/vue'
import { computed } from 'vue'
import { useI18n } from 'vue-i18n'

const { t } = useI18n()

const props = defineProps({
  setting: { type: Object, required: true },
  loading: { type: Boolean, default: false }
})

defineEmits([
  'change', 
  'openEmailPrefix', 
  'openDomainMapping', 
  'openResendList', 
  'openResendForm'
])

const authRefreshOptions = computed(() => [
  { label: t('disable'), value: 0 },
  { label: '3s', value: 3 },
  { label: '5s', value: 5 },
  { label: '10s', value: 10 },
  { label: '15s', value: 15 },
  { label: '20s', value: 20 },
])

function beforeChange() {
  if (props.loading) return false
  return true
}
</script>
