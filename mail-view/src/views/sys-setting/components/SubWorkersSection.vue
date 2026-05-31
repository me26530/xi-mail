<template>
  <div class="settings-card">
    <div class="card-header">
      <div class="card-title">
        <Icon icon="mdi:server-network" width="18" height="18" />
        {{ $t('subWorkerManage') }}
      </div>
      <el-button type="primary" size="small" @click="$emit('addSubWorker')">
        <Icon icon="mingcute:add-line" width="14" height="14" style="margin-right: 4px;" />
        {{ $t('subWorkerAdd') }}
      </el-button>
    </div>
    <div class="card-body">
      <div v-if="subWorkers.length === 0" class="empty-state">
        <Icon icon="mdi:server-off" width="32" height="32" class="icon"/>
        <span>{{ $t('subWorkerEmpty') }}</span>
      </div>

      <template v-else>
        <div v-for="sw in subWorkers" :key="sw.id" class="list-item">
          <div class="list-item-info">
            <span class="list-item-name">{{ sw.name }}</span>
            <span class="list-item-tag" v-for="d in sw.domains" :key="d">{{ d }}</span>
          </div>
          <div class="list-item-actions">
            <el-tag :type="sw.status ? 'success' : 'info'" size="small">
              {{ sw.status ? $t('subWorkerEnabled') : $t('subWorkerDisabled') }}
            </el-tag>
            <el-switch
              :model-value="!!sw.status"
              size="small"
              @change="$emit('toggleStatus', sw)"
            />
            <el-button size="small" type="danger" plain @click="$emit('deleteSubWorker', sw)">
              <Icon icon="mingcute:delete-2-line" width="14" height="14" />
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
