<template>
  <div class="settings-card">
    <div class="card-header">
      <div>
        <h3 class="card-title">{{ $t('subWorkerManage') }}</h3>
        <p class="card-desc">{{ $t('subWorkerManageDesc') || 'Manage distributed sub-worker instances' }}</p>
      </div>
      <el-button type="primary" size="small" @click="$emit('addSubWorker')">
        {{ $t('subWorkerAdd') || 'Add Worker' }}
      </el-button>
    </div>
    <div class="card-body">
      <div v-if="subWorkers.length === 0" class="empty-state">
        <Icon icon="mingcute:server-line" width="36" height="36" class="icon"/>
        <span>{{ $t('subWorkerEmpty') || 'No sub-workers configured' }}</span>
      </div>

      <template v-else>
        <div v-for="sw in subWorkers" :key="sw.id" class="list-row">
          <div class="list-info">
            <span class="list-name">{{ sw.name }}</span>
            <el-tag v-for="d in sw.domains" :key="d" size="small" type="info" style="margin-left: 8px;">{{ d }}</el-tag>
          </div>
          <div class="list-actions">
            <el-switch
              :model-value="!!sw.status"
              size="small"
              @change="$emit('toggleStatus', sw)"
            />
            <el-button size="small" type="danger" text @click="$emit('deleteSubWorker', sw)">
              <Icon icon="mingcute:delete-2-line" width="16" height="16" />
            </el-button>
          </div>
        </div>
      </template>
    </div>
  </div>
</template>

<script setup>
import { Icon } from '@iconify/vue'

defineProps({
  subWorkers: { type: Array, default: () => [] }
})

defineEmits(['addSubWorker', 'toggleStatus', 'deleteSubWorker'])
</script>
