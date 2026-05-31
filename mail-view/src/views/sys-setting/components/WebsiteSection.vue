<template>
  <div class="settings-card">
    <div class="card-header">
      <div>
        <h3 class="card-title">{{ $t('websiteSetting') }}</h3>
        <p class="card-desc">{{ $t('websiteSettingDesc') || 'Configure your website registration and access settings' }}</p>
      </div>
    </div>
    <div class="card-body">
      <div class="setting-row">
        <div class="setting-info">
          <span class="setting-label">{{ $t('websiteReg') }}</span>
          <span class="setting-desc">{{ $t('websiteRegDesc') || 'Allow new users to register on this website' }}</span>
        </div>
        <div class="setting-control">
          <el-switch 
            @change="$emit('change')" 
            :before-change="beforeChange" 
            :active-value="0" 
            :inactive-value="1"
            v-model="setting.register"
          />
        </div>
      </div>
      
      <div class="setting-row">
        <div class="setting-info">
          <span class="setting-label">{{ $t('loginDomain') }}</span>
          <span class="setting-desc">{{ $t('loginDomainDesc') || 'Enable domain-based login' }}</span>
        </div>
        <div class="setting-control">
          <el-switch 
            @change="$emit('change')" 
            :before-change="beforeChange" 
            :active-value="0" 
            :inactive-value="1"
            v-model="setting.loginDomain"
          />
        </div>
      </div>
      
      <div class="setting-row">
        <div class="setting-info">
          <span class="setting-label">{{ $t('regKey') }}</span>
          <span class="setting-desc">{{ $t('regKeyDesc') || 'Require registration key for new accounts' }}</span>
        </div>
        <div class="setting-control">
          <el-select
            @change="$emit('change')"
            style="width: 120px;"
            v-model="setting.regKey"
            placeholder="Select"
          >
            <el-option
              v-for="item in regKeyOptions"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            />
          </el-select>
        </div>
      </div>
      
      <div class="setting-row" v-if="setting.regKey === 0 || setting.regKey === 2">
        <div class="setting-info">
          <span class="setting-label">
            {{ $t('regKeyHint') }}
            <el-tooltip effect="dark" :content="$t('regKeyHintDesc')">
              <Icon class="tip-icon" icon="mingcute:information-line" width="15" height="15"/>
            </el-tooltip>
          </span>
        </div>
        <div class="setting-control">
          <el-input
            v-model="setting.regKeyHint"
            :placeholder="$t('regKeyHintPlaceholder')"
            style="width: 200px;"
            clearable
            @change="$emit('change')"
          />
        </div>
      </div>
      
      <div class="setting-row" v-if="setting.regKey === 0 || setting.regKey === 2">
        <div class="setting-info">
          <span class="setting-label">
            {{ $t('regKeyLink') }}
            <el-tooltip effect="dark" :content="$t('regKeyLinkDesc')">
              <Icon class="tip-icon" icon="mingcute:information-line" width="15" height="15"/>
            </el-tooltip>
          </span>
        </div>
        <div class="setting-control">
          <el-input
            v-model="setting.regKeyLink"
            :placeholder="$t('regKeyLinkPlaceholder')"
            style="width: 200px;"
            clearable
            @change="$emit('change')"
          />
        </div>
      </div>
      
      <div class="setting-row">
        <div class="setting-info">
          <span class="setting-label">{{ $t('addAccount') }}</span>
          <span class="setting-desc">{{ $t('addAccountDesc') || 'Allow users to add additional email accounts' }}</span>
        </div>
        <div class="setting-control">
          <el-switch 
            @change="$emit('change')" 
            :before-change="beforeChange" 
            :active-value="0" 
            :inactive-value="1"
            v-model="setting.addEmail"
          />
        </div>
      </div>
      
      <div class="setting-row">
        <div class="setting-info">
          <span class="setting-label">
            {{ $t('multipleEmail') }}
            <el-tooltip effect="dark" :content="$t('multipleEmailDesc')">
              <Icon class="tip-icon" icon="mingcute:information-line" width="15" height="15"/>
            </el-tooltip>
          </span>
          <span class="setting-desc">{{ $t('multipleEmailShortDesc') || 'Support multiple email addresses per account' }}</span>
        </div>
        <div class="setting-control">
          <el-switch 
            @change="$emit('change')" 
            :before-change="beforeChange" 
            :active-value="0" 
            :inactive-value="1"
            v-model="setting.manyEmail"
          />
        </div>
      </div>
      
      <div class="setting-row">
        <div class="setting-info">
          <span class="setting-label">{{ $t('websiteTitle') }}</span>
          <span class="setting-desc">{{ setting.title || 'Not set' }}</span>
        </div>
        <div class="setting-control">
          <el-button size="small" @click="$emit('editTitle')">
            {{ $t('edit') || 'Edit' }}
          </el-button>
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

const emit = defineEmits(['change', 'editTitle'])

const regKeyOptions = computed(() => [
  { label: t('enable'), value: 0 },
  { label: t('disable'), value: 1 },
  { label: t('optional'), value: 2 },
])

function beforeChange() {
  if (props.loading) return false
  return true
}
</script>
