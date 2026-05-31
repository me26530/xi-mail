<template>
  <div class="settings-card">
    <div class="card-header">
      <div>
        <h3 class="card-title">{{ $t('domainManagement') }}</h3>
        <p class="card-desc">{{ $t('domainManagementDesc') || 'Manage email domains for your organization' }}</p>
      </div>
    </div>
    <div class="card-body">
      <div class="setting-row">
        <div class="setting-info">
          <span class="setting-label">{{ $t('domainList') }}</span>
          <span class="setting-desc">{{ $t('domainListDesc') || 'View and manage registered domains' }}</span>
        </div>
        <div class="setting-control">
          <el-tag type="info" size="small">{{ domainsCount }} {{ domainsCount === 1 ? $t('domain') : $t('domains') || 'domains' }}</el-tag>
          <el-button size="small" type="primary" @click="$emit('openDomainManagement')">
            {{ $t('manage') || 'Manage' }}
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
