<template>
  <div>
    <!-- Email Address Settings -->
    <div class="settings-card">
      <div class="card-header">
        <div class="card-title">
          <Icon icon="mdi:at" width="18" height="18" />
          {{ $t('emailAddressSetting') }}
        </div>
      </div>
      <div class="card-body">
        <div class="setting-row">
          <span class="setting-label">{{ $t('emailPrefix') }}</span>
          <div class="setting-control">
            <el-button class="action-btn" size="small" type="primary" @click="$emit('openEmailPrefix')">
              <Icon icon="fluent:settings-48-regular" width="16" height="16"/>
            </el-button>
          </div>
        </div>
        
        <div class="setting-row">
          <span class="setting-label">
            {{ $t('randomPrefixLength') }}
            <el-tooltip effect="dark" :content="$t('randomPrefixDesc')">
              <Icon class="tip-icon" icon="fe:warning" width="16" height="16"/>
            </el-tooltip>
          </span>
          <div class="setting-control">
            <el-input-number 
              size="small" 
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
          <span class="setting-label">
            {{ $t('domainMapping') }}
            <el-tooltip effect="dark" :content="$t('domainMappingDesc')">
              <Icon class="tip-icon" icon="fe:warning" width="16" height="16"/>
            </el-tooltip>
          </span>
          <div class="setting-control">
            <el-button class="action-btn" size="small" type="primary" @click="$emit('openDomainMapping')">
              <Icon icon="fluent:settings-48-regular" width="16" height="16"/>
            </el-button>
          </div>
        </div>
      </div>
    </div>

    <!-- Email Sending Settings -->
    <div class="settings-card">
      <div class="card-header">
        <div class="card-title">
          <Icon icon="mdi:email-send-outline" width="18" height="18" />
          {{ $t('emailSetting') }}
        </div>
      </div>
      <div class="card-body">
        <div class="setting-row">
          <span class="setting-label">{{ $t('receiveEmail') }}</span>
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
          <span class="setting-label">
            {{ $t('autoRefresh') }}
            <el-tooltip effect="dark" :content="$t('autoRefreshDesc')">
              <Icon class="tip-icon" icon="fe:warning" width="16" height="16"/>
            </el-tooltip>
          </span>
          <div class="setting-control">
            <el-select
              @change="$emit('change')"
              style="width: 80px;"
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
          <span class="setting-label">{{ $t('sendEmail') }}</span>
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
          <span class="setting-label">
            {{ $t('noRecipientTitle') }}
            <el-tooltip effect="dark" :content="$t('noRecipientDesc')">
              <Icon class="tip-icon" icon="fe:warning" width="16" height="16"/>
            </el-tooltip>
          </span>
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
          <span class="setting-label">{{ $t('resendToken') }}</span>
          <div class="setting-control">
            <el-button class="action-btn" @click="$emit('openResendList')" size="small" type="primary">
              <Icon icon="ic:round-list" width="16" height="16"/>
            </el-button>
            <el-button class="action-btn" @click="$emit('openResendForm')" size="small" type="primary">
              <Icon icon="material-symbols:add-rounded" width="14" height="14"/>
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
