<template>
  <div class="settings-card">
    <div class="card-header">
      <div>
        <h3 class="card-title">{{ $t('appearance') }}</h3>
        <p class="card-desc">{{ $t('appearanceDesc') || 'Customize the look and feel of your application' }}</p>
      </div>
    </div>
    <div class="card-body">
      <!-- Color Theme -->
      <div class="appearance-row">
        <div class="appearance-label">{{ $t('colorTheme') }}</div>
        <div class="swatches">
          <button
            v-for="theme in colorThemes"
            :key="theme.id"
            :class="['swatch', { active: setting.colorTheme === theme.id }]"
            :style="{ background: theme.color }"
            :title="theme.label"
            @click="$emit('applyColorTheme', theme.id)"
          >
            <Icon v-if="setting.colorTheme === theme.id" icon="mingcute:check-fill" width="14" height="14" color="#fff" />
          </button>
        </div>
      </div>

      <!-- Layout Mode -->
      <div class="appearance-row">
        <div class="appearance-label">{{ $t('layoutMode') }}</div>
        <div class="layout-grid">
          <button
            v-for="mode in layoutModes"
            :key="mode.id"
            :class="['layout-option', { active: setting.layoutMode === mode.id }]"
            @click="$emit('applyLayoutMode', mode.id)"
          >
            <div v-if="mode.id === 'default'" class="layout-preview lp-default">
              <div class="lp-sidebar">
                <div class="lp-sb-item"></div>
                <div class="lp-sb-item"></div>
                <div class="lp-sb-item"></div>
              </div>
              <div class="lp-content">
                <div class="lp-bar"></div>
                <div class="lp-body"></div>
              </div>
            </div>
            <div v-else-if="mode.id === 'compact'" class="layout-preview lp-compact">
              <div class="lp-sidebar lp-sidebar-sm">
                <div class="lp-dot"></div>
                <div class="lp-dot"></div>
                <div class="lp-dot"></div>
              </div>
              <div class="lp-content">
                <div class="lp-bar"></div>
                <div class="lp-body"></div>
              </div>
            </div>
            <div v-else class="layout-preview lp-top">
              <div class="lp-full">
                <div class="lp-hbar">
                  <div class="lp-hdot"></div>
                  <div class="lp-hdot"></div>
                  <div class="lp-hdot"></div>
                </div>
                <div class="lp-bar"></div>
                <div class="lp-body"></div>
              </div>
            </div>
            <span class="template-label">{{ mode.label }}</span>
          </button>
        </div>
      </div>

      <!-- Login Template -->
      <div class="appearance-row">
        <div class="appearance-label">{{ $t('loginTemplate') }}</div>
        <div class="template-grid">
          <button
            v-for="tpl in loginTemplates"
            :key="tpl.id"
            :class="['template-card', { active: setting.loginTemplate === tpl.id }]"
            @click="$emit('applyLoginTemplate', tpl.id)"
          >
            <div v-if="tpl.id === 'gradient'" class="template-preview tpl-gradient">
              <div class="tpl-orb tpl-orb-1"></div>
              <div class="tpl-orb tpl-orb-2"></div>
              <div class="tpl-inner"></div>
            </div>
            <div v-else-if="tpl.id === 'minimal'" class="template-preview tpl-minimal">
              <div class="tpl-inner tpl-minimal-inner"></div>
            </div>
            <div v-else class="template-preview tpl-split">
              <div class="tpl-split-left"></div>
              <div class="tpl-split-right">
                <div class="tpl-inner tpl-split-inner"></div>
              </div>
            </div>
            <span class="template-label">{{ tpl.label }}</span>
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { Icon } from '@iconify/vue'
import { useI18n } from 'vue-i18n'

const { t } = useI18n()

defineProps({
  setting: { type: Object, required: true },
  colorThemes: { type: Array, required: true },
  layoutModes: { type: Array, required: true },
  loginTemplates: { type: Array, required: true }
})

defineEmits(['applyColorTheme', 'applyLayoutMode', 'applyLoginTemplate'])
</script>

<style scoped lang="scss">
/* Layout Preview Styles */
.layout-preview {
  width: 100px;
  height: 64px;
  border-radius: 6px;
  overflow: hidden;
  display: flex;
  background: #1a1a22;
}

.lp-sidebar {
  width: 24px;
  background: #1a1a22;
  display: flex;
  flex-direction: column;
  gap: 4px;
  padding: 6px 4px;
}

.lp-sidebar-sm {
  width: 12px;
  align-items: center;
  padding: 6px 2px;
}

.lp-sb-item {
  height: 4px;
  width: 100%;
  background: #3f3f52;
  border-radius: 2px;
}

.lp-dot {
  width: 4px;
  height: 4px;
  background: #3f3f52;
  border-radius: 50%;
}

.lp-content {
  flex: 1;
  display: flex;
  flex-direction: column;
  background: #f4f5f7;
  padding: 4px;
  gap: 3px;
}

.lp-bar {
  height: 6px;
  background: #e5e7eb;
  border-radius: 2px;
}

.lp-body {
  flex: 1;
  background: #fff;
  border-radius: 2px;
}

.lp-full {
  flex: 1;
  display: flex;
  flex-direction: column;
  background: #f4f5f7;
  padding: 4px;
  gap: 3px;
}

.lp-hbar {
  height: 6px;
  background: #1a1a22;
  border-radius: 2px;
  display: flex;
  align-items: center;
  gap: 3px;
  padding: 0 4px;
}

.lp-hdot {
  width: 8px;
  height: 2px;
  background: #3f3f52;
  border-radius: 1px;
}

/* Template Preview Styles */
.template-preview {
  width: 100px;
  height: 64px;
  border-radius: 6px;
  overflow: hidden;
  position: relative;
}

.tpl-gradient {
  background: #0a0a10;
  
  .tpl-orb {
    position: absolute;
    border-radius: 50%;
    filter: blur(12px);
  }
  
  .tpl-orb-1 {
    width: 60px;
    height: 60px;
    background: rgba(99, 102, 241, 0.5);
    top: -20px;
    right: -10px;
  }
  
  .tpl-orb-2 {
    width: 50px;
    height: 50px;
    background: rgba(139, 92, 246, 0.4);
    bottom: -20px;
    left: -10px;
  }
  
  .tpl-inner {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    width: 40px;
    height: 28px;
    background: rgba(255, 255, 255, 0.08);
    backdrop-filter: blur(4px);
    border-radius: 4px;
    border: 1px solid rgba(255, 255, 255, 0.1);
  }
}

.tpl-minimal {
  background: #f4f5f7;
  display: flex;
  align-items: center;
  justify-content: center;
  
  .tpl-minimal-inner {
    width: 40px;
    height: 28px;
    background: #fff;
    border-radius: 4px;
    border: 1px solid #e5e7eb;
  }
}

.tpl-split {
  display: flex;
  
  .tpl-split-left {
    width: 40%;
    background: linear-gradient(135deg, var(--el-color-primary), var(--el-color-primary-light-3));
  }
  
  .tpl-split-right {
    flex: 1;
    background: #f4f5f7;
    display: flex;
    align-items: center;
    justify-content: center;
    
    .tpl-split-inner {
      width: 36px;
      height: 24px;
      background: #fff;
      border-radius: 3px;
      border: 1px solid #e5e7eb;
    }
  }
}

.dark {
  .lp-content,
  .lp-full {
    background: #1e1e24;
  }
  
  .lp-bar {
    background: #27272a;
  }
  
  .lp-body {
    background: #111114;
  }
  
  .tpl-minimal {
    background: #1e1e24;
    
    .tpl-minimal-inner {
      background: #111114;
      border-color: #27272a;
    }
  }
  
  .tpl-split-right {
    background: #1e1e24;
    
    .tpl-split-inner {
      background: #111114;
      border-color: #27272a;
    }
  }
}
</style>
