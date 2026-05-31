<template>
  <div class="settings-card">
    <div class="card-header">
      <div class="card-title">
        <Icon icon="mdi:web" width="18" height="18" />
        {{ $t('websiteSetting') }}
      </div>
    </div>
    <div class="card-body">
      <div class="setting-row">
        <span class="setting-label">{{ $t('websiteReg') }}</span>
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
        <span class="setting-label">{{ $t('loginDomain') }}</span>
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
        <span class="setting-label">{{ $t('regKey') }}</span>
        <div class="setting-control">
          <el-select
            @change="$emit('change')"
            style="width: 100px;"
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
        <span class="setting-label">
          {{ $t('regKeyHint') }}
          <el-tooltip effect="dark" :content="$t('regKeyHintDesc')">
            <Icon class="tip-icon" icon="fe:warning" width="16" height="16"/>
          </el-tooltip>
        </span>
        <div class="setting-control">
          <el-input
            v-model="setting.regKeyHint"
            :placeholder="$t('regKeyHintPlaceholder')"
            style="width: 180px;"
            clearable
            @change="$emit('change')"
          />
        </div>
      </div>
      
      <div class="setting-row" v-if="setting.regKey === 0 || setting.regKey === 2">
        <span class="setting-label">
          {{ $t('regKeyLink') }}
          <el-tooltip effect="dark" :content="$t('regKeyLinkDesc')">
            <Icon class="tip-icon" icon="fe:warning" width="16" height="16"/>
          </el-tooltip>
        </span>
        <div class="setting-control">
          <el-input
            v-model="setting.regKeyLink"
            :placeholder="$t('regKeyLinkPlaceholder')"
            style="width: 180px;"
            clearable
            @change="$emit('change')"
          />
        </div>
      </div>
      
      <div class="setting-row">
        <span class="setting-label">{{ $t('addAccount') }}</span>
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
        <span class="setting-label">
          {{ $t('multipleEmail') }}
          <el-tooltip effect="dark" :content="$t('multipleEmailDesc')">
            <Icon class="tip-icon" icon="fe:warning" width="16" height="16"/>
          </el-tooltip>
        </span>
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
        <span class="setting-label">{{ $t('websiteTitle') }}</span>
        <div class="setting-control">
          <span class="setting-value">{{ setting.title }}</span>
          <el-button class="action-btn" size="small" type="primary" @click="$emit('editTitle')">
            <Icon icon="lsicon:edit-outline" width="14" height="14"/>
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
