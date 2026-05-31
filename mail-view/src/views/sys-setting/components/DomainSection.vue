<template>
  <div class="settings-card">
    <div class="card-header">
      <div class="card-title">
        <Icon icon="mdi:domain" width="18" height="18" />
        {{ $t('domainManagement') }}
      </div>
    </div>
    <div class="card-body">
      <div class="setting-row">
        <span class="setting-label">
          {{ $t('domainList') }}
          <el-tooltip effect="dark" :content="$t('domainManagementDesc')">
            <Icon class="tip-icon" icon="fe:warning" width="16" height="16"/>
          </el-tooltip>
        </span>
        <div class="setting-control">
          <el-tag type="info" size="small">{{ domainsCount }} {{ $t('domain') || 'domains' }}</el-tag>
          <el-button class="action-btn" size="small" type="primary" @click="$emit('openDomainManagement')">
            <Icon icon="fluent:settings-48-regular" width="16" height="16"/>
          </el-button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { Icon } from '@iconify/vue'

defineProps({
  domainsCount: { type: Number, default: 0 }
})

defineEmits(['openDomainManagement'])
</script>
