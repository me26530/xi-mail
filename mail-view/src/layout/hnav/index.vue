<template>
  <nav class="hnav">
    <div class="hnav-inner">
      <!-- Logo -->
      <div class="hnav-logo">
        <div class="logo-mark">
          <Icon icon="mingcute:mail-send-fill" width="15" height="15" />
        </div>
        <span class="logo-text">{{ settingStore.settings.title }}</span>
      </div>

      <div class="hnav-divider-v" />

      <!-- Main nav items -->
      <div class="hnav-items">
        <div
          v-for="item in mainNav"
          :key="item.name"
          v-show="item.sendOnly ? canSend : (!item.perm || hasPerm(item.perm))"
          class="hnav-item"
          :class="{ active: route.meta.name === item.name }"
          @click="router.push({ name: item.name })"
        >
          <div class="hnav-icon">
            <Icon :icon="item.icon" :width="item.size || 16" :height="item.size || 16" />
            <el-badge
              v-if="item.name === 'transfer' && transferStore.pendingCount > 0"
              :value="transferStore.pendingCount"
              class="hnav-badge"
            />
          </div>
          <span class="hnav-label">{{ $t(item.label) }}</span>
        </div>

        <!-- Admin nav -->
        <div
          v-for="item in adminNav"
          :key="'admin-' + item.name"
          class="hnav-item"
          :class="{ active: route.meta.name === item.name }"
          @click="router.push({ name: item.name })"
          v-perm="item.perm"
        >
          <div class="hnav-icon">
            <Icon :icon="item.icon" :width="item.size || 16" :height="item.size || 16" />
          </div>
          <span class="hnav-label">{{ $t(item.label) }}</span>
        </div>
      </div>
    </div>
  </nav>
</template>

<script setup>
import router from '@/router/index.js'
import { useRoute } from 'vue-router'
import { computed } from 'vue'
import { Icon } from '@iconify/vue'
import { useSettingStore } from '@/store/setting.js'
import { useUserStore } from '@/store/user.js'
import { useTransferStore } from '@/store/transfer.js'
import { hasPerm } from '@/perm/perm.js'

const settingStore = useSettingStore()
const userStore = useUserStore()
const transferStore = useTransferStore()
const route = useRoute()

const canSend = computed(() => {
  if (settingStore.settings.send === 1) return false
  const role = userStore.user?.role
  if (role?.sendType === 'ban') return false
  return hasPerm('email:send')
})

const mainNav = [
  { name: 'email',    icon: 'mingcute:inbox-line',      label: 'inbox' },
  { name: 'send',     icon: 'mingcute:send-line',       label: 'sent',          sendOnly: true },
  { name: 'draft',    icon: 'mingcute:file-line',       label: 'drafts',        sendOnly: true },
  { name: 'star',     icon: 'mingcute:star-line',       label: 'starred' },
  { name: 'setting',  icon: 'mingcute:settings-3-line', label: 'settings' },
  { name: 'transfer', icon: 'mingcute:transfer-3-line', label: 'transferPending' },
]

const adminNav = [
  { name: 'analysis',    icon: 'mingcute:chart-pie-2-line', label: 'analytics',     perm: 'analysis:query' },
  { name: 'user',        icon: 'mingcute:group-line',        label: 'allUsers',      perm: 'user:query' },
  { name: 'all-email',   icon: 'mingcute:mail-open-line',    label: 'allMail',       perm: 'all-email:query' },
  { name: 'role',        icon: 'mingcute:shield-line',       label: 'permissions',   perm: 'role:query' },
  { name: 'reg-key',     icon: 'mingcute:key-2-line',        label: 'inviteCode',    perm: 'reg-key:query' },
  { name: 'sys-setting', icon: 'mingcute:settings-6-line',   label: 'SystemSettings',perm: 'setting:query' },
]
</script>

<style lang="scss" scoped>
.hnav {
  height: 44px;
  flex-shrink: 0;
  background: var(--el-bg-color);
  border-bottom: 1px solid var(--el-border-color-lighter);
  overflow: hidden;
  position: relative;
}

.hnav-inner {
  display: flex;
  align-items: center;
  height: 100%;
  padding: 0 12px;
  gap: 2px;
  overflow-x: auto;
  scrollbar-width: none;
  &::-webkit-scrollbar { display: none; }
}

.hnav-logo {
  display: flex;
  align-items: center;
  gap: 8px;
  flex-shrink: 0;
  padding: 0 10px 0 2px;
}

.logo-mark {
  width: 26px;
  height: 26px;
  border-radius: 6px;
  background: var(--xi-gradient);
  display: flex;
  align-items: center;
  justify-content: center;
  color: #fff;
  flex-shrink: 0;
  box-shadow: 0 1px 4px rgba(0,0,0,0.12);
}

.logo-text {
  font-size: 13px;
  font-weight: 700;
  color: var(--el-text-color-primary);
  white-space: nowrap;
}

.hnav-divider-v {
  width: 1px;
  height: 16px;
  background: var(--el-border-color-lighter);
  flex-shrink: 0;
  margin: 0 6px;
}

.hnav-items {
  display: flex;
  align-items: center;
  gap: 1px;
  flex: 1;
  min-width: 0;
}

.hnav-item {
  display: flex;
  align-items: center;
  gap: 6px;
  height: 30px;
  padding: 0 10px;
  border-radius: 6px;
  color: var(--el-text-color-regular);
  cursor: pointer;
  transition: all 0.13s ease;
  font-size: 13px;
  font-weight: 450;
  white-space: nowrap;
  flex-shrink: 0;
  position: relative;

  &:hover {
    background: var(--el-fill-color);
    color: var(--el-text-color-primary);
  }

  &.active {
    background: var(--el-color-primary-light-9);
    color: var(--el-color-primary);
    font-weight: 550;

    .hnav-icon { color: var(--el-color-primary); }
  }
}

.hnav-icon {
  position: relative;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-shrink: 0;
}

.hnav-badge {
  position: absolute;
  top: -5px;
  right: -7px;

  :deep(.el-badge__content) {
    font-size: 9px;
    height: 14px;
    line-height: 14px;
    padding: 0 4px;
    min-width: 14px;
    border: none;
  }
}
</style>
