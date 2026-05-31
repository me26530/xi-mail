<template>
  <div class="settings-page">
    <!-- Loading -->
    <div class="loading-overlay" :class="firstLoading ? 'loading-show' : 'loading-hide'">
      <loading/>
    </div>

    <template v-if="!firstLoading">
      <!-- Header -->
      <header class="settings-header">
        <h1 class="settings-title">{{ $t('systemSettings') || 'Settings' }}</h1>
        <p class="settings-subtitle">{{ $t('systemSettingsDesc') || 'Manage your system preferences and configurations' }}</p>
      </header>

      <!-- Tab Navigation -->
      <nav class="settings-tabs">
        <button
          v-for="sec in sections"
          :key="sec.id"
          class="tab-btn"
          :class="{ active: activeSection === sec.id }"
          @click="activeSection = sec.id"
        >
          <Icon :icon="sec.icon" width="18" height="18" />
          <span>{{ $t(sec.label) }}</span>
        </button>
      </nav>

      <!-- Content Area -->
      <el-scrollbar class="settings-content">
        <div class="content-inner">
          <!-- Website Section -->
          <WebsiteSection 
            v-show="activeSection === 'website'"
            :setting="setting"
            :loading="settingLoading"
            @change="change"
            @editTitle="editTitleShow = true"
          />

          <!-- Security Section -->
          <SecuritySection 
            v-show="activeSection === 'security'"
            :setting="setting"
            :globalTokenEnabled="globalTokenEnabled"
            :globalToken="globalToken"
            :globalTokenVisible="globalTokenVisible"
            :globalTokenGenerating="globalTokenGenerating"
            @change="change"
            @openKeywordBlacklist="keywordBlacklistShow = true"
            @openSenderDomainBlacklist="senderDomainBlacklistShow = true"
            @globalTokenEnabledChange="onGlobalTokenEnabledChange"
            @toggleTokenVisible="globalTokenVisible = !globalTokenVisible"
            @copyToken="copyGlobalToken"
            @generateToken="onGenerateGlobalToken"
          />

          <!-- Email/Registration Section -->
          <EmailSection 
            v-show="activeSection === 'registration'"
            :setting="setting"
            :loading="settingLoading"
            @change="change"
            @openEmailPrefix="emailPrefixShow = true"
            @openDomainMapping="domainMappingShow = true"
            @openResendList="showResendList = true"
            @openResendForm="resendTokenFormShow = true"
          />

          <!-- Domain Section -->
          <DomainSection 
            v-show="activeSection === 'domain'"
            :domainsCount="managedDomainsData.length"
            @openDomainManagement="domainManagementShow = true"
          />

          <!-- Integration Section -->
          <IntegrationSection 
            v-show="activeSection === 'integration'"
            :setting="setting"
            @change="change"
            @openR2Domain="r2DomainShow = true"
            @openS3Config="addS3Show = true"
            @openTgSetting="openTgSetting"
            @openThirdEmail="openThirdEmailSetting"
            @openForwardRules="openForwardRules"
            @openRegVerifyCount="regVerifyCountShow = true"
            @openAddVerifyCount="addVerifyCountShow = true"
            @openTurnstile="turnstileShow = true"
            @openNoticeSetting="noticePopupShow = true"
            @openNoticePopup="openNoticePopup"
          />

          <!-- Sub-workers Section -->
          <SubWorkersSection 
            v-show="activeSection === 'sub-workers'"
            :subWorkers="subWorkers"
            @addSubWorker="subWorkerDialogShow = true"
            @toggleStatus="toggleSubWorkerStatus"
            @deleteSubWorker="deleteSubWorker"
          />

          <!-- Servers Section (Standalone Only) -->
          <div v-show="activeSection === 'servers'" class="settings-card">
            <div class="card-header">
              <div class="card-title">
                <Icon icon="mdi:server-outline" width="18" height="18" />
                {{ $t('serverManage') }}
              </div>
            </div>
            <div class="card-body" style="padding: 16px 20px;">
              <ServerManager />
            </div>
          </div>

          <!-- Appearance Section -->
          <AppearanceSection 
            v-show="activeSection === 'appearance'"
            :setting="setting"
            :colorThemes="colorThemes"
            :layoutModes="layoutModes"
            :loginTemplates="loginTemplates"
            @applyColorTheme="applyColorTheme"
            @applyLayoutMode="applyLayoutMode"
            @applyLoginTemplate="applyLoginTemplate"
          />

          <!-- About Section -->
          <AboutSection 
            v-show="activeSection === 'about'"
            :currentVersion="currentVersion"
            :hasUpdate="hasUpdate"
            @openRelease="jump('https://github.com/PastKing/xi-mail/releases')"
            @openGithub="jump('https://github.com/PastKing/xi-mail')"
            @openTelegram="jump('https://t.me/pk_oa')"
            @copy="copyAddr"
          />
        </div>
      </el-scrollbar>
    </template>

    <!-- All Dialogs -->
    <el-dialog v-model="editTitleShow" :title="$t('changeTitle')" width="340" @closed="editTitle = setting.title">
      <form>
        <el-input type="text" :placeholder="$t('websiteTitle')" v-model="editTitle"/>
        <el-button type="primary" :loading="settingLoading" @click="saveTitle">{{ $t('save') }}</el-button>
      </form>
    </el-dialog>

    <el-dialog v-model="resendTokenFormShow" :title="$t('resendToken')" width="340" @closed="cleanResendTokenForm">
      <form>
        <el-select style="margin-bottom: 15px" v-model="resendTokenForm.domain" placeholder="Select">
          <el-option v-for="item in settingStore.domainList" :key="item" :label="item" :value="item"/>
        </el-select>
        <el-input type="text" :placeholder="$t('addResendTokenDesc')" v-model="resendTokenForm.token"/>
        <el-button type="primary" :loading="settingLoading" @click="saveResendToken">{{ $t('save') }}</el-button>
      </form>
    </el-dialog>

    <el-dialog v-model="r2DomainShow" :title="$t('addOsDomain')" width="340" @closed="r2DomainInput = setting.r2Domain">
      <form>
        <el-input type="text" :placeholder="$t('domainDesc')" v-model="r2DomainInput"/>
        <el-button type="primary" :loading="settingLoading" @click="saveR2domain">{{ $t('save') }}</el-button>
      </form>
    </el-dialog>

    <el-dialog v-model="turnstileShow" :title="$t('addTurnstileSecret')" width="340" @closed="turnstileForm.secretKey = '';turnstileForm.siteKey = ''">
      <form>
        <el-input type="text" placeholder="Site Key" v-model="turnstileForm.siteKey"/>
        <el-input type="text" style="margin-top: 15px" placeholder="Secret Key" v-model="turnstileForm.secretKey"/>
        <el-button type="primary" :loading="settingLoading" @click="saveTurnstileKey">{{ $t('save') }}</el-button>
      </form>
    </el-dialog>

    <el-dialog v-model="tgSettingShow" class="forward-dialog">
      <template #header>
        <div class="forward-head">
          <span class="forward-set-title">{{ $t('tgBot') }}</span>
          <el-tooltip effect="dark" :content="$t('tgBotDesc')">
            <Icon class="warning" icon="fe:warning" width="18" height="18"/>
          </el-tooltip>
        </div>
      </template>
      <div class="forward-set-body">
        <el-input :placeholder="$t('tgBotToken')" v-model="tgBotToken"/>
        <el-input-tag tag-type="warning" :placeholder="$t('toBotTokenDesc')" v-model="tgChatId" @add-tag="addChatTag"/>
        <el-input tag-type="warning" :placeholder="$t('customDomainDesc')" v-model="customDomain"/>
        <div class="tg-msg-label">
          <span>{{ t('from') }}</span>
          <el-select v-model="tgMsgFrom">
            <el-option v-for="item in tgMsgFromOption" :key="item.value" :label="item.label" :value="item.value"/>
          </el-select>
        </div>
        <div class="tg-msg-label">
          <span>{{ t('recipient') }}</span>
          <el-select v-model="tgMsgTo">
            <el-option v-for="item in tgMsgToOption" :key="item.value" :label="item.label" :value="item.value"/>
          </el-select>
        </div>
        <div class="tg-msg-label">
          <span>{{ t('emailText') }}</span>
          <el-select v-model="tgMsgText">
            <el-option v-for="item in tgMsgTextOption" :key="item.value" :label="item.label" :value="item.value"/>
          </el-select>
        </div>
      </div>
      <template #footer>
        <div class="dialog-footer">
          <el-switch v-model="tgBotStatus" :active-value="0" :inactive-value="1" :active-text="$t('enable')" :inactive-text="$t('disable')"/>
          <el-button :loading="settingLoading" type="primary" @click="tgBotSave">{{ $t('save') }}</el-button>
        </div>
      </template>
    </el-dialog>

    <el-dialog v-model="thirdEmailShow" class="forward-dialog">
      <template #header>
        <div class="forward-head">
          <span class="forward-set-title">{{ $t('otherEmail') }}</span>
          <el-tooltip effect="dark" :content="$t('otherEmailDesc')">
            <Icon class="warning" icon="fe:warning" width="18" height="18"/>
          </el-tooltip>
        </div>
      </template>
      <div class="forward-set-body">
        <el-input-tag tag-type="warning" :placeholder="$t('otherEmailInputDesc')" v-model="forwardEmail" @add-tag="emailAddTag"/>
      </div>
      <template #footer>
        <div class="dialog-footer">
          <el-switch v-model="forwardStatus" :active-value="0" :inactive-value="1" :active-text="$t('enable')" :inactive-text="$t('disable')"/>
          <el-button :loading="settingLoading" type="primary" @click="forwardEmailSave">{{ $t('save') }}</el-button>
        </div>
      </template>
    </el-dialog>

    <el-dialog v-model="forwardRulesShow" class="forward-dialog">
      <template #header>
        <div class="forward-head">
          <span class="forward-set-title">{{ $t('forwardingRules') }}</span>
          <el-tooltip effect="dark" :content="$t('forwardingRulesDesc')">
            <Icon class="warning" icon="fe:warning" width="18" height="18"/>
          </el-tooltip>
        </div>
      </template>
      <div class="forward-set-body">
        <el-input-tag :placeholder="$t('ruleEmailsInputDesc')" tag-type="success" v-model="ruleEmail" @add-tag="ruleEmailAddTag"/>
      </div>
      <template #footer>
        <div class="dialog-footer">
          <el-radio-group v-model="ruleType">
            <el-radio :value="0">{{ $t('forwardAll') }}</el-radio>
            <el-radio :value="1">{{ $t('rules') }}</el-radio>
          </el-radio-group>
          <el-button :loading="settingLoading" type="primary" @click="ruleEmailSave">{{ $t('save') }}</el-button>
        </div>
      </template>
    </el-dialog>

    <el-dialog class="resend-table" v-model="showResendList" :title="$t('resendTokenList')">
      <el-table :data="resendList">
        <el-table-column :min-width="emailColumnWidth" property="key" :label="$t('domain')" :show-overflow-tooltip="true"/>
        <el-table-column :width="tokenColumnWidth" property="value" label="Token" fixed="right" :show-overflow-tooltip="true"/>
      </el-table>
    </el-dialog>

    <el-dialog v-model="regVerifyCountShow" :title="$t('rulesVerifyTitle', { count: regVerifyCount })" @closed="regVerifyCount = setting.regVerifyCount">
      <form>
        <el-input-number type="text" v-model="regVerifyCount" :min="1"/>
        <el-button type="primary" :loading="settingLoading" @click="saveRegVerifyCount">{{ $t('save') }}</el-button>
      </form>
    </el-dialog>

    <el-dialog v-model="addVerifyCountShow" :title="$t('rulesVerifyTitle', { count: addVerifyCount })" @closed="addVerifyCount = setting.addVerifyCount">
      <form>
        <el-input-number type="text" v-model="addVerifyCount" :min="1"/>
        <el-button type="primary" :loading="settingLoading" @click="saveAddVerifyCount">{{ $t('save') }}</el-button>
      </form>
    </el-dialog>

    <el-dialog top="5vh" v-model="noticePopupShow" :title="$t('noticePopup')" class="notice-popup" @closed="resetNoticeForm">
      <form>
        <div class="notice-form-row">
          <span class="notice-form-label">{{ $t('title') }}</span>
          <el-input v-model="noticeForm.noticeTitle" :placeholder="t('titleDesc')"/>
        </div>
        <div class="notice-form-row">
          <span class="notice-form-label">{{ $t('width') }}</span>
          <el-input-number v-model="noticeForm.noticeWidth" :min="300" :max="1200" style="width:100%">
            <template #suffix>px</template>
          </el-input-number>
        </div>
        <div class="notice-popup-item">
          <el-input v-model="noticeForm.noticeContent" :autosize="{ minRows: 15, maxRows: 25 }" type="textarea" :placeholder="t('noticeContentDesc')"/>
        </div>
      </form>
      <template #footer>
        <div class="dialog-footer">
          <el-switch v-model="noticeForm.notice" :active-value="0" :inactive-value="1" :active-text="$t('enable')" :inactive-text="$t('disable')"/>
          <div>
            <el-button @click="previewNoticePopup">{{ $t('preview') }}</el-button>
            <el-button :loading="settingLoading" type="primary" @click="saveNoticePopup">{{ $t('save') }}</el-button>
          </div>
        </div>
      </template>
    </el-dialog>

    <el-dialog v-model="addS3Show" :title="t('s3Configuration')" width="340" @closed="resetAddS3Form">
      <form>
        <el-input class="dialog-input" type="text" placeholder="Bucket" v-model="s3.bucket"/>
        <el-input class="dialog-input" type="text" placeholder="Endpoint" v-model="s3.endpoint"/>
        <el-input class="dialog-input" type="text" placeholder="Region" v-model="s3.region"/>
        <el-input class="dialog-input" type="text" :placeholder="setting.s3AccessKey || 'Access Key'" v-model="s3.s3AccessKey"/>
        <el-input style="margin-bottom: 10px" type="text" :placeholder="setting.s3SecretKey || 'Secret Key'" v-model="s3.s3SecretKey"/>
        <div class="force-path-style">
          <div class="force-path-style-left">
            <span>ForcePathStyle</span>
            <el-tooltip effect="dark" :content="$t('forcePathStyleDesc')">
              <Icon class="warning" icon="fe:warning" width="18" height="18"/>
            </el-tooltip>
          </div>
          <el-switch :before-change="beforeChange" :active-value="0" :inactive-value="1" v-model="s3.forcePathStyle"/>
        </div>
        <div class="s3-button">
          <el-button :loading="clearS3Loading" @click="clearS3">{{ t('clear') }}</el-button>
          <el-button type="primary" :loading="settingLoading && !clearS3Loading" @click="saveS3">{{ t('save') }}</el-button>
        </div>
      </form>
    </el-dialog>

    <el-dialog v-model="domainManagementShow" :title="$t('domainManagement')" width="460" @closed="resetDomainForm">
      <div class="domain-management">
        <div class="domain-add-row">
          <el-input v-model="newDomainInput" :placeholder="$t('domainPlaceholder')" @keyup.enter="addDomain" style="flex:1"/>
          <el-button type="primary" @click="addDomain">{{ $t('add') }}</el-button>
        </div>
        <div v-if="managedDomainsData.length === 0" class="domain-empty">{{ $t('noDomains') }}</div>
        <div v-else class="domain-list">
          <div v-for="(item, idx) in managedDomainsData" :key="item.domain" class="domain-row">
            <span class="domain-name">{{ item.domain }}</span>
            <div class="domain-actions">
              <el-switch v-model="item.enabled" :active-value="true" :inactive-value="false" size="small" @change="() => domainItemChange()"/>
              <el-button size="small" type="danger" text @click="removeDomain(idx)">
                <Icon icon="material-symbols:delete-outline-rounded" width="16" height="16"/>
              </el-button>
            </div>
          </div>
        </div>
      </div>
      <template #footer>
        <div class="dialog-footer">
          <el-button @click="domainManagementShow = false">{{ $t('cancel') }}</el-button>
          <el-button type="primary" :loading="settingLoading" @click="saveDomains">{{ $t('save') }}</el-button>
        </div>
      </template>
    </el-dialog>

    <el-dialog v-model="emailPrefixShow" :title="t('emailPrefix')" @closed="resetEmailPrefix">
      <div class="email-prefix">
        <div>{{ t('atLeast') }}</div>
        <el-input-number v-model="minEmailPrefix" :min="1" :max="20" style="width: 150px">
          <template #suffix><span>{{ t('character') }}</span></template>
        </el-input-number>
      </div>
      <div class="prefix-filter">
        <div style="margin-bottom: 10px;">{{ t('mustNotContain') }}</div>
        <el-input-tag style="margin-bottom: 10px;" v-model="emailPrefixFilter" :placeholder="t('mustNotContainDesc')"/>
      </div>
      <el-button type="primary" style="width: 100%;" :loading="settingLoading" @click="saveEmailPrefix">{{ $t('save') }}</el-button>
    </el-dialog>

    <el-dialog v-model="domainMappingShow" :title="t('domainMapping')" class="mapping-dialog">
      <div class="mapping-list">
        <div v-for="(val, key) in domainMappingData" :key="key" class="mapping-row">
          <el-tag size="small" type="info" class="mapping-source">{{ key }}</el-tag>
          <Icon icon="mingcute:arrow-right-line" width="14" height="14" style="flex-shrink: 0; color: var(--el-text-color-secondary);"/>
          <el-input size="small" v-model="domainMappingData[key]" :placeholder="t('displayDomain')" style="flex: 1;"/>
          <el-button size="small" type="danger" link @click="delete domainMappingData[key]">
            <Icon icon="material-symbols:delete-outline-rounded" width="16" height="16"/>
          </el-button>
        </div>
        <div class="mapping-row">
          <el-select size="small" v-model="newMappingSource" filterable allow-create default-first-option :placeholder="t('sourceDomain')" style="flex: 1;">
            <el-option v-for="d in systemDomains" :key="d" :label="d" :value="d" :disabled="!!domainMappingData[d]"/>
          </el-select>
          <Icon icon="mingcute:arrow-right-line" width="14" height="14" style="flex-shrink: 0; color: var(--el-text-color-secondary);"/>
          <el-input size="small" v-model="newMappingDisplay" :placeholder="t('displayDomain')" style="flex: 1;"/>
          <el-button size="small" type="primary" link @click="addDomainMapping">
            <Icon icon="material-symbols:add-rounded" width="16" height="16"/>
          </el-button>
        </div>
        <el-button type="primary" style="width: 100%;" :loading="settingLoading" @click="saveDomainMapping">{{ $t('save') }}</el-button>
      </div>
    </el-dialog>

    <el-dialog v-model="keywordBlacklistShow" :title="t('emailKeywordBlacklist')" @closed="resetKeywordBlacklist">
      <div class="keyword-blacklist">
        <div style="margin-bottom: 10px; font-size: 13px; color: var(--el-text-color-secondary);">{{ t('emailKeywordBlacklistHint') }}</div>
        <el-input-tag style="margin-bottom: 10px;" v-model="keywordBlacklistData" :placeholder="t('emailKeywordBlacklistPlaceholder')"/>
        <el-button type="primary" style="width: 100%;" :loading="settingLoading" @click="saveKeywordBlacklist">{{ $t('save') }}</el-button>
      </div>
    </el-dialog>

    <el-dialog v-model="senderDomainBlacklistShow" :title="t('senderDomainBlacklist')" @closed="resetSenderDomainBlacklist">
      <div class="keyword-blacklist">
        <div style="margin-bottom: 10px; font-size: 13px; color: var(--el-text-color-secondary);">{{ t('senderDomainBlacklistHint') }}</div>
        <el-input-tag style="margin-bottom: 10px;" v-model="senderDomainBlacklistData" :placeholder="t('senderDomainBlacklistPlaceholder')"/>
        <el-button type="primary" style="width: 100%;" :loading="settingLoading" @click="saveSenderDomainBlacklist">{{ $t('save') }}</el-button>
      </div>
    </el-dialog>

    <el-dialog v-model="subWorkerDialogShow" :title="t('subWorkerAdd')" width="420" @closed="resetSubWorkerForm">
      <div class="sub-worker-form">
        <div class="setup-field">
          <label>{{ $t('subWorkerName') }}</label>
          <el-input v-model="subWorkerForm.name" :placeholder="$t('subWorkerNamePlaceholder')"/>
        </div>
        <div class="setup-field">
          <label>{{ $t('subWorkerUrl') }}</label>
          <el-input v-model="subWorkerForm.url" :placeholder="$t('subWorkerUrlPlaceholder')"/>
        </div>
        <div class="setup-field">
          <label>{{ $t('subWorkerToken') }}</label>
          <el-input v-model="subWorkerForm.apiToken" :placeholder="$t('subWorkerTokenPlaceholder')" show-password/>
        </div>
        <div class="sub-worker-form-actions">
          <el-button @click="testSubWorker" :loading="subWorkerTesting" :disabled="!subWorkerForm.url || !subWorkerForm.apiToken">{{ $t('subWorkerTestConnect') }}</el-button>
          <el-button type="primary" @click="addSubWorker" :loading="subWorkerAdding" :disabled="!subWorkerForm.url || !subWorkerForm.apiToken || !subWorkerForm.name">{{ $t('save') }}</el-button>
        </div>
      </div>
    </el-dialog>
  </div>
</template>

<script setup>
import { computed, defineOptions, reactive, ref } from "vue"
import { settingQuery, settingSet, getGlobalToken, setGlobalTokenEnabled, generateGlobalToken } from "@/request/setting.js"
import { useSettingStore } from "@/store/setting.js"
import { useUiStore } from "@/store/ui.js"
import { useUserStore } from "@/store/user.js"
import { useAccountStore } from "@/store/account.js"
import { Icon } from "@iconify/vue"
import { storeToRefs } from "pinia"
import { isEmail } from "@/utils/verify-utils.js"
import loading from "@/components/loading/index.vue"
import { getTextWidth } from "@/utils/text.js"
import { useI18n } from 'vue-i18n'
import axios from "axios"
import { useServerStore as useServerStoreRef } from "@/store/server.js"
import ServerManager from "@/components/server-manager/index.vue"
import { subWorkerList, subWorkerAdd as subWorkerAddApi, subWorkerTest as subWorkerTestApi, subWorkerDelete as subWorkerDeleteApi, subWorkerSetStatus } from "@/request/sub-worker.js"

// Section Components
import WebsiteSection from './components/WebsiteSection.vue'
import SecuritySection from './components/SecuritySection.vue'
import EmailSection from './components/EmailSection.vue'
import DomainSection from './components/DomainSection.vue'
import IntegrationSection from './components/IntegrationSection.vue'
import SubWorkersSection from './components/SubWorkersSection.vue'
import AppearanceSection from './components/AppearanceSection.vue'
import AboutSection from './components/AboutSection.vue'

defineOptions({
  name: 'sys-setting'
})

const currentVersion = 'v3.1.0'

/* ── Settings navigation ── */
const activeSection = ref('website')

const serverStoreRef = useServerStoreRef()
const sections = computed(() => {
  const list = [
    { id: 'website',     icon: 'mingcute:home-4-line',      label: 'websiteSetting' },
    { id: 'security',    icon: 'mingcute:shield-line',       label: 'securitySetting' },
    { id: 'registration',icon: 'mingcute:user-add-line',     label: 'emailAddressSetting' },
    { id: 'domain',      icon: 'mingcute:world-2-line',      label: 'domainManagement' },
    { id: 'integration', icon: 'mingcute:plug-2-line',       label: 'integration' },
    { id: 'sub-workers', icon: 'mingcute:server-line',       label: 'subWorkerManage' },
    { id: 'appearance',  icon: 'mingcute:palette-line',      label: 'appearance' },
  ]
  if (serverStoreRef.isStandalone) {
    list.push({ id: 'servers', icon: 'mingcute:cloud-line', label: 'serverManage' })
  }
  list.push({ id: 'about', icon: 'mingcute:information-line', label: 'about' })
  return list
})

const hasUpdate = ref(false)
let getUpdateErrorCount = 1
const { t, locale } = useI18n()
const firstLoading = ref(true)
const accountStore = useAccountStore()
const userStore = useUserStore()
const editTitleShow = ref(false)
const resendTokenFormShow = ref(false)
const r2DomainShow = ref(false)
const turnstileShow = ref(false)
const tgSettingShow = ref(false)
const noticePopupShow = ref(false)
const thirdEmailShow = ref(false)
const forwardRulesShow = ref(false)
const emailPrefixShow = ref(false)
const showResendList = ref(false)
const settingStore = useSettingStore()
const uiStore = useUiStore()
const { settings: setting } = storeToRefs(settingStore)
const editTitle = ref('')
const settingLoading = ref(false)
const clearS3Loading = ref(false)
const r2DomainInput = ref('')
const minEmailPrefix = ref(0)
const emailPrefixFilter = ref([])
const domainMappingShow = ref(false)
const domainMappingData = reactive({})
const newMappingSource = ref('')
const newMappingDisplay = ref('')
const keywordBlacklistShow = ref(false)
const keywordBlacklistData = ref([])
const senderDomainBlacklistShow = ref(false)
const senderDomainBlacklistData = ref([])
const subWorkerDialogShow = ref(false)
const subWorkerTesting = ref(false)
const subWorkerAdding = ref(false)
const subWorkers = ref([])
const subWorkerForm = reactive({ name: '', url: '', apiToken: '' })
const domainManagementShow = ref(false)
const managedDomainsData = ref([])
const newDomainInput = ref('')

const systemDomains = computed(() => {
  return (settingStore.domainList || []).map(d => d.replace(/^@/, ''))
})

const colorThemes = computed(() => [
  { id: 'indigo',  color: '#6366f1', label: 'Indigo'  },
  { id: 'rose',    color: '#f43f5e', label: 'Rose'    },
  { id: 'emerald', color: '#10b981', label: 'Emerald' },
  { id: 'amber',   color: '#f59e0b', label: 'Amber'   },
  { id: 'sky',     color: '#0ea5e9', label: 'Sky'     },
  { id: 'purple',  color: '#a855f7', label: 'Purple'  },
])

const loginTemplates = computed(() => [
  { id: 'gradient', label: t('templateGradient') },
  { id: 'minimal',  label: t('templateMinimal')  },
  { id: 'split',    label: t('templateSplit')     },
])

function applyColorTheme(id) {
  setting.value.colorTheme = id
  document.documentElement.dataset.colorTheme = id
  editSetting({ colorTheme: id })
}

function applyLoginTemplate(id) {
  setting.value.loginTemplate = id
  editSetting({ loginTemplate: id })
}

const layoutModes = computed(() => [
  { id: 'default', label: t('layoutDefault') },
  { id: 'compact', label: t('layoutCompact') },
  { id: 'top',     label: t('layoutTop') },
])

function applyLayoutMode(id) {
  setting.value.layoutMode = id
  editSetting({ layoutMode: id })
}

let regVerifyCount = ref(1)
let addVerifyCount = ref(1)
let backup = '{}'
const addS3Show = ref(false)
const addVerifyCountShow = ref(false)
const regVerifyCountShow = ref(false)

const globalToken = ref('')
const globalTokenEnabled = ref(false)
const globalTokenVisible = ref(false)
const globalTokenGenerating = ref(false)
const resendTokenForm = reactive({ domain: '', token: '' })
const turnstileForm = reactive({ siteKey: '', secretKey: '' })

const s3 = reactive({
  bucket: '',
  endpoint: '',
  region: '',
  s3AccessKey: '',
  s3SecretKey: '',
  forcePathStyle: 1
})

const noticeForm = reactive({
  noticeTitle: '',
  noticeContent: '',
  noticeType: '',
  noticeDuration: '',
  noticePosition: '',
  noticeOffset: 0,
  notice: 0,
  noticeWidth: 0
})

const tgChatId = ref([])
const customDomain = ref('')
const tgBotStatus = ref(0)
const tgBotToken = ref('')
const forwardEmail = ref([])
const forwardStatus = ref(0)
const emailColumnWidth = ref(0)
const tokenColumnWidth = ref(0)
const ruleType = ref(0)
const ruleEmail = ref([])
const tgMsgFrom = ref('')
const tgMsgTo = ref('')
const tgMsgText = ref('')

const tgMsgFromOption = [{ label: t('show'), value: 'show' }, { label: t('hide'), value: 'hide' }, { label: t('onlyName'), value: 'only-name' }]
const tgMsgToOption = [{ label: t('show'), value: 'show' }, { label: t('hide'), value: 'hide' }]
const tgMsgTextOption = [{ label: t('show'), value: 'show' }, { label: t('hide'), value: 'hide' }]

getSettings()
getUpdate()
loadGlobalToken()

function loadGlobalToken() {
  getGlobalToken().then(data => {
    globalToken.value = data.token || ''
    globalTokenEnabled.value = !!data.enabled
  }).catch(() => {})
}

function onGlobalTokenEnabledChange(val) {
  setGlobalTokenEnabled(val).catch(() => {
    globalTokenEnabled.value = !val
    ElMessage.error(t('saveFail'))
  })
}

async function onGenerateGlobalToken() {
  globalTokenGenerating.value = true
  try {
    const data = await generateGlobalToken()
    globalToken.value = data.token
    globalTokenVisible.value = true
    ElMessage.success(t('generateSuccess'))
  } catch {
    ElMessage.error(t('saveFail'))
  } finally {
    globalTokenGenerating.value = false
  }
}

function copyGlobalToken() {
  if (!globalToken.value) return
  navigator.clipboard.writeText(globalToken.value).then(() => {
    ElMessage.success(t('copySuccess'))
  }).catch(() => {
    ElMessage.error(t('copyFail'))
  })
}

function copyAddr(addr) {
  navigator.clipboard.writeText(addr).then(() => {
    ElMessage.success(t('copySuccess'))
  }).catch(() => {
    ElMessage.error(t('copyFail'))
  })
}

function getSettings() {
  settingQuery().then(settingData => {
    setting.value = settingData
    settingStore.domainList = settingData.domainList
    Object.assign(domainMappingData, setting.value.domainMapping || {})
    keywordBlacklistData.value = Array.isArray(setting.value.emailKeywordBlacklist)
      ? [...setting.value.emailKeywordBlacklist]
      : (setting.value.emailKeywordBlacklist || '').split(',').filter(Boolean)
    senderDomainBlacklistData.value = Array.isArray(setting.value.senderDomainBlacklist)
      ? [...setting.value.senderDomainBlacklist]
      : (setting.value.senderDomainBlacklist || '').split(',').filter(Boolean)
    resendTokenForm.domain = setting.value.domainList[0]
    minEmailPrefix.value = setting.value.minEmailPrefix
    if (setting.value.managedDomains && setting.value.managedDomains.length > 0) {
      managedDomainsData.value = setting.value.managedDomains.map(d =>
        typeof d === 'string' ? { domain: d, enabled: true } : { ...d }
      )
    } else {
      managedDomainsData.value = (setting.value.domainList || []).map(d => ({
        domain: d.replace(/^@/, ''),
        enabled: true
      }))
    }
    firstLoading.value = false
    if (setting.value.colorTheme) {
      document.documentElement.dataset.colorTheme = setting.value.colorTheme
    }
    editTitle.value = setting.value.title
    r2DomainInput.value = setting.value.r2Domain
    addVerifyCount.value = setting.value.addVerifyCount
    regVerifyCount.value = setting.value.regVerifyCount
    resetNoticeForm()
    resetAddS3Form()
    resetEmailPrefix()
  })
}

function openDomainManagement() {
  domainManagementShow.value = true
}

function resetDomainForm() {
  newDomainInput.value = ''
}

function addDomain() {
  const d = newDomainInput.value.trim().replace(/^@/, '').toLowerCase()
  if (!d) return
  if (managedDomainsData.value.some(item => item.domain === d)) {
    ElMessage.warning(t('domainExists'))
    return
  }
  managedDomainsData.value.push({ domain: d, enabled: true })
  newDomainInput.value = ''
}

function removeDomain(idx) {
  managedDomainsData.value.splice(idx, 1)
}

function domainItemChange() {}

function saveDomains() {
  const domains = managedDomainsData.value.filter(d => d.domain)
  editSetting({ managedDomains: domains })
  domainManagementShow.value = false
}

function openNoticePopup() {
  uiStore.showNotice()
}

function openAddVerifyCount() {
  if (settingLoading.value) return
  addVerifyCountShow.value = true
}

function openRegVerifyCount() {
  if (settingLoading.value) return
  regVerifyCountShow.value = true
}

function resetAddS3Form() {
  s3.bucket = setting.value.bucket
  s3.endpoint = setting.value.endpoint
  s3.region = setting.value.region
  s3.s3AccessKey = ''
  s3.s3SecretKey = ''
  s3.forcePathStyle = setting.value.forcePathStyle
}

const resendList = computed(() => {
  let list = Object.keys(setting.value.resendTokens).map(key => ({ key, value: setting.value.resendTokens[key] }))
  if (list.length > 0) {
    const key = list.reduce((a, b) => compareByLengthAndUpperCase(a, b, 'key')).key
    emailColumnWidth.value = getTextWidth(key) + 30
    const value = list.reduce((a, b) => compareByLengthAndUpperCase(a, b, 'value')).value
    tokenColumnWidth.value = getTextWidth(value) + 30
  }
  return list
})

function loadSubWorkers() {
  subWorkerList().then(list => { subWorkers.value = list || [] }).catch(() => {})
}
loadSubWorkers()

function resetSubWorkerForm() {
  subWorkerForm.name = ''
  subWorkerForm.url = ''
  subWorkerForm.apiToken = ''
}

function testSubWorker() {
  subWorkerTesting.value = true
  subWorkerTestApi({ url: subWorkerForm.url, apiToken: subWorkerForm.apiToken }).then(data => {
    const domains = data?.domains || []
    ElMessage({ message: t('subWorkerTestSuccess', { count: domains.length }), type: 'success', plain: true })
  }).catch(() => {
    ElMessage({ message: t('subWorkerTestFail'), type: 'error', plain: true })
  }).finally(() => { subWorkerTesting.value = false })
}

function addSubWorker() {
  subWorkerAdding.value = true
  subWorkerAddApi(subWorkerForm).then(() => {
    ElMessage({ message: t('subWorkerAddSuccess'), type: 'success', plain: true })
    subWorkerDialogShow.value = false
    loadSubWorkers()
  }).catch(() => {}).finally(() => { subWorkerAdding.value = false })
}

function deleteSubWorker(sw) {
  ElMessageBox.confirm(t('subWorkerDeleteConfirm'), { type: 'warning' }).then(() => {
    subWorkerDeleteApi(sw.id).then(() => { loadSubWorkers() })
  }).catch(() => {})
}

function toggleSubWorkerStatus(sw) {
  subWorkerSetStatus(sw.id, sw.status ? 0 : 1).then(() => { loadSubWorkers() })
}

function getUpdate() {
  if (getUpdateErrorCount > 5 || !getUpdateErrorCount) return
  axios.get('https://api.github.com/repos/PastKing/xi-mail/releases/latest').then(({ data }) => {
    hasUpdate.value = data.name !== currentVersion
    getUpdateErrorCount = 0
  }).catch(e => {
    getUpdateErrorCount++
    setTimeout(() => { getUpdate() }, 2000)
    console.error('Check update failed:', e)
  })
}

function saveAddVerifyCount() {
  if (!addVerifyCount.value) addVerifyCount.value = 1
  editSetting({ addVerifyCount: addVerifyCount.value })
}

function saveRegVerifyCount() {
  if (!regVerifyCount.value) regVerifyCount.value = 1
  editSetting({ regVerifyCount: regVerifyCount.value })
}

const compareByLengthAndUpperCase = (a, b, key) => {
  const getUpperCaseCount = (str) => (str.match(/[A-Z]/g) || []).length
  if (a[key].length === b[key].length) {
    return getUpperCaseCount(a[key]) > getUpperCaseCount(b[key]) ? a : b
  }
  return a[key].length > b[key].length ? a : b
}

function openTgSetting() {
  tgBotStatus.value = setting.value.tgBotStatus
  tgBotToken.value = setting.value.tgBotToken
  customDomain.value = setting.value.customDomain
  tgMsgFrom.value = setting.value.tgMsgFrom
  tgMsgText.value = setting.value.tgMsgText
  tgMsgTo.value = setting.value.tgMsgTo
  tgChatId.value = []
  if (setting.value.tgChatId) {
    const list = setting.value.tgChatId.split(',')
    tgChatId.value.push(...list)
  }
  tgSettingShow.value = true
}

function openNoticePopupSetting() {
  noticePopupShow.value = true
}

function openResendList() {
  showResendList.value = true
}

function resetNoticeForm() {
  noticeForm.notice = setting.value.notice
  noticeForm.noticeContent = setting.value.noticeContent
  noticeForm.noticeDuration = setting.value.noticeDuration
  noticeForm.noticeTitle = setting.value.noticeTitle
  noticeForm.noticePosition = setting.value.noticePosition
  noticeForm.noticeType = setting.value.noticeType
  noticeForm.noticeOffset = setting.value.noticeOffset
  noticeForm.noticeWidth = setting.value.noticeWidth
}

function saveNoticePopup() {
  noticeForm.noticeOffset = noticeForm.noticeOffset || 0
  noticeForm.noticeWidth = noticeForm.noticeWidth || 0
  noticeForm.noticeDuration = noticeForm.noticeDuration || 0
  editSetting({ ...noticeForm })
}

function previewNoticePopup() {
  uiStore.previewNotice({ ...noticeForm })
}

function openThirdEmailSetting() {
  forwardEmail.value = []
  forwardStatus.value = setting.value.forwardStatus
  if (setting.value.forwardEmail) {
    const list = setting.value.forwardEmail.split(',')
    forwardEmail.value.push(...list)
  }
  thirdEmailShow.value = true
}

function openEmailPrefix() {
  emailPrefixShow.value = true
}

function openForwardRules() {
  ruleType.value = setting.value.ruleType
  ruleEmail.value = []
  if (setting.value.ruleEmail) {
    const list = setting.value.ruleEmail.split(',')
    ruleEmail.value.push(...list)
  }
  forwardRulesShow.value = true
}

function emailAddTag(val) {
  const emails = Array.from(new Set(val.split(/[,，]/).map(item => item.trim()).filter(item => item)))
  forwardEmail.value.splice(forwardEmail.value.length - 1, 1)
  emails.forEach(email => {
    if (isEmail(email) && !forwardEmail.value.includes(email)) {
      forwardEmail.value.push(email)
    }
  })
}

function ruleEmailAddTag(val) {
  const emails = Array.from(new Set(val.split(/[,，]/).map(item => item.trim()).filter(item => item)))
  ruleEmail.value.splice(ruleEmail.value.length - 1, 1)
  emails.forEach(email => {
    if (isEmail(email) && !ruleEmail.value.includes(email)) {
      ruleEmail.value.push(email)
    }
  })
}

function addChatTag(val) {
  const chatIds = Array.from(new Set(val.split(/[,，]/).map(item => item.trim()).filter(item => item)))
  tgChatId.value.splice(tgChatId.value.length - 1, 1)
  chatIds.forEach(id => {
    if (!isNaN(Number(id))) {
      tgChatId.value.push(id)
    }
  })
}

function clearS3() {
  const form = { bucket: '', endpoint: '', region: '', s3AccessKey: '', s3SecretKey: '', forcePathStyle: 1 }
  clearS3Loading.value = true
  editSetting(form)
}

function saveS3() {
  const form = { bucket: s3.bucket, endpoint: s3.endpoint, region: s3.region, forcePathStyle: s3.forcePathStyle }
  if (s3.s3AccessKey) form.s3AccessKey = s3.s3AccessKey
  if (s3.s3SecretKey) form.s3SecretKey = s3.s3SecretKey
  editSetting(form)
}

function tgBotSave() {
  const form = {
    tgBotToken: tgBotToken.value,
    customDomain: customDomain.value,
    tgBotStatus: tgBotStatus.value,
    tgChatId: tgChatId.value + '',
    tgMsgFrom: tgMsgFrom.value,
    tgMsgText: tgMsgText.value,
    tgMsgTo: tgMsgTo.value
  }
  editSetting(form)
}

function forwardEmailSave() {
  const form = { forwardStatus: forwardStatus.value, forwardEmail: forwardEmail.value + '' }
  editSetting(form)
}

function ruleEmailSave() {
  const form = { ruleEmail: ruleEmail.value + '', ruleType: ruleType.value }
  editSetting(form)
}

function resetEmailPrefix() {
  minEmailPrefix.value = setting.value.minEmailPrefix
  emailPrefixFilter.value = setting.value.emailPrefixFilter
}

function saveEmailPrefix() {
  const form = {}
  form.minEmailPrefix = minEmailPrefix.value
  form.emailPrefixFilter = emailPrefixFilter.value
  editSetting(form, true)
}

function addDomainMapping() {
  if (newMappingSource.value && newMappingDisplay.value) {
    domainMappingData[newMappingSource.value] = newMappingDisplay.value
    newMappingSource.value = ''
    newMappingDisplay.value = ''
  }
}

function saveDomainMapping() {
  editSetting({ domainMapping: { ...domainMappingData } }, true)
  domainMappingShow.value = false
}

function resetKeywordBlacklist() {
  keywordBlacklistData.value = Array.isArray(setting.value.emailKeywordBlacklist)
    ? [...setting.value.emailKeywordBlacklist]
    : (setting.value.emailKeywordBlacklist || '').split(',').filter(Boolean)
}

function saveKeywordBlacklist() {
  editSetting({ emailKeywordBlacklist: keywordBlacklistData.value }, true)
  keywordBlacklistShow.value = false
}

function resetSenderDomainBlacklist() {
  senderDomainBlacklistData.value = Array.isArray(setting.value.senderDomainBlacklist)
    ? [...setting.value.senderDomainBlacklist]
    : (setting.value.senderDomainBlacklist || '').split(',').filter(Boolean)
}

function saveSenderDomainBlacklist() {
  editSetting({ senderDomainBlacklist: senderDomainBlacklistData.value }, true)
  senderDomainBlacklistShow.value = false
}

function saveTurnstileKey() {
  const settingForm = {}
  settingForm.siteKey = turnstileForm.siteKey
  settingForm.secretKey = turnstileForm.secretKey
  editSetting(settingForm)
}

function saveR2domain() {
  const settingForm = { r2Domain: r2DomainInput.value }
  editSetting(settingForm)
}

function openResendForm() {
  resendTokenFormShow.value = true
}

function saveResendToken() {
  const settingForm = { resendTokens: {} }
  const domain = resendTokenForm.domain.slice(1)
  settingForm.resendTokens[domain] = resendTokenForm.token
  editSetting(settingForm)
}

function backupSetting() {
  const settingForm = { ...setting.value }
  delete settingForm.resendTokens
  delete settingForm.siteKey
  delete settingForm.secretKey
  backup = JSON.stringify(setting.value)
}

function cleanResendTokenForm() {
  resendTokenForm.token = ''
}

function beforeChange() {
  if (settingLoading.value) return false
  backupSetting()
  return true
}

function change() {
  const settingForm = { ...setting.value }
  delete settingForm.siteKey
  delete settingForm.secretKey
  delete settingForm.s3AccessKey
  delete settingForm.s3SecretKey
  delete settingForm.resendTokens
  editSetting(settingForm, false)
}

function saveTitle() {
  editSetting({ title: editTitle.value })
}

function jump(href) {
  const doc = document.createElement('a')
  doc.href = href
  doc.target = '_blank'
  doc.click()
}

function editSetting(settingForm, refreshStatus = true) {
  if (settingLoading.value) return
  settingLoading.value = true

  settingSet(settingForm).then(() => {
    settingLoading.value = false
    ElMessage({ message: t('saveSuccessMsg'), type: "success", plain: true })
    if (setting.value.manyEmail === 1) {
      accountStore.currentAccountId = userStore.user.account.accountId
    }
    if (refreshStatus) {
      getSettings()
    }
    editTitleShow.value = false
    r2DomainShow.value = false
    resendTokenFormShow.value = false
    turnstileShow.value = false
    tgSettingShow.value = false
    thirdEmailShow.value = false
    forwardRulesShow.value = false
    addVerifyCountShow.value = false
    regVerifyCountShow.value = false
    noticePopupShow.value = false
    addS3Show.value = false
    emailPrefixShow.value = false
    clearS3Loading.value = false
  }).catch(() => {
    settingLoading.value = false
    clearS3Loading.value = false
    setting.value = JSON.parse(backup)
    ElMessage({ message: t('saveFail'), type: 'error', plain: true })
  })
}
</script>

<style scoped lang="scss">
@import './styles/settings.scss';

.settings-page {
  height: 100%;
  display: flex;
  flex-direction: column;
  background: var(--el-bg-color);
  position: relative;
}

.loading-overlay {
  position: absolute;
  inset: 0;
  display: flex;
  align-items: center;
  justify-content: center;
  background: var(--el-bg-color);
  z-index: 10;
}

.loading-show {
  opacity: 1;
  transition: opacity 200ms ease 200ms;
}

.loading-hide {
  opacity: 0;
  pointer-events: none;
  transition: var(--loading-hide-transition);
}

/* Header */
.settings-header {
  padding: 24px 32px 0;
  flex-shrink: 0;
  
  @media (max-width: 640px) {
    padding: 20px 16px 0;
  }
}

.settings-title {
  font-size: 24px;
  font-weight: 700;
  color: var(--el-text-color-primary);
  margin: 0 0 4px;
  letter-spacing: -0.02em;
}

.settings-subtitle {
  font-size: 14px;
  color: var(--el-text-color-secondary);
  margin: 0;
}

/* Tab Navigation */
.settings-tabs {
  display: flex;
  gap: 4px;
  padding: 20px 32px 0;
  border-bottom: 1px solid var(--el-border-color-lighter);
  overflow-x: auto;
  flex-shrink: 0;
  scrollbar-width: none;
  
  &::-webkit-scrollbar {
    display: none;
  }
  
  @media (max-width: 640px) {
    padding: 16px 16px 0;
  }
}

.tab-btn {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 10px 16px;
  border: none;
  background: none;
  font-size: 14px;
  font-weight: 500;
  color: var(--el-text-color-secondary);
  cursor: pointer;
  border-radius: 8px 8px 0 0;
  transition: all 0.15s ease;
  white-space: nowrap;
  position: relative;
  
  &:hover {
    color: var(--el-text-color-primary);
    background: var(--el-fill-color-light);
  }
  
  &.active {
    color: var(--el-color-primary);
    background: var(--el-fill-color);
    
    &::after {
      content: '';
      position: absolute;
      bottom: -1px;
      left: 0;
      right: 0;
      height: 2px;
      background: var(--el-color-primary);
      border-radius: 2px 2px 0 0;
    }
  }
  
  @media (max-width: 640px) {
    padding: 8px 12px;
    font-size: 13px;
    
    span {
      display: none;
    }
  }
}

/* Content Area */
.settings-content {
  flex: 1;
  min-height: 0;
}

.content-inner {
  padding: 24px 32px;
  max-width: 800px;
  
  @media (max-width: 640px) {
    padding: 16px;
  }
}

/* Dialog Styles (preserved from original) */
form {
  display: flex;
  flex-direction: column;
  gap: 15px;
}

.dialog-input {
  margin-bottom: 10px;
}

.forward-head {
  display: flex;
  align-items: center;
  gap: 8px;
}

.forward-set-title {
  font-size: 16px;
  font-weight: 600;
}

.forward-set-body {
  display: flex;
  flex-direction: column;
  gap: 15px;
}

.tg-msg-label {
  display: flex;
  align-items: center;
  gap: 10px;
  
  span {
    min-width: 80px;
    font-size: 14px;
    color: var(--el-text-color-regular);
  }
  
  .el-select {
    flex: 1;
  }
}

.dialog-footer {
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.force-path-style {
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-bottom: 15px;
}

.force-path-style-left {
  display: flex;
  align-items: center;
  gap: 6px;
}

.s3-button {
  display: flex;
  gap: 10px;
  
  .el-button {
    flex: 1;
  }
}

.domain-management {
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.domain-add-row {
  display: flex;
  gap: 10px;
}

.domain-empty {
  text-align: center;
  padding: 20px;
  color: var(--el-text-color-placeholder);
  font-size: 14px;
}

.domain-list {
  display: flex;
  flex-direction: column;
  gap: 8px;
  max-height: 300px;
  overflow-y: auto;
}

.domain-row {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 10px 12px;
  background: var(--el-fill-color-light);
  border-radius: 8px;
}

.domain-name {
  font-size: 14px;
  font-weight: 500;
}

.domain-actions {
  display: flex;
  align-items: center;
  gap: 8px;
}

.email-prefix {
  display: flex;
  align-items: center;
  gap: 10px;
  margin-bottom: 15px;
}

.prefix-filter {
  margin-bottom: 15px;
}

.mapping-list {
  display: flex;
  flex-direction: column;
  gap: 10px;
}

.mapping-row {
  display: flex;
  align-items: center;
  gap: 10px;
}

.mapping-source {
  min-width: 100px;
}

.keyword-blacklist {
  display: flex;
  flex-direction: column;
}

.notice-form-row {
  display: flex;
  align-items: center;
  gap: 10px;
  margin-bottom: 12px;
}

.notice-form-label {
  min-width: 60px;
  font-size: 14px;
}

.notice-popup-item {
  margin-bottom: 15px;
}

.sub-worker-form {
  display: flex;
  flex-direction: column;
  gap: 15px;
}

.setup-field {
  display: flex;
  flex-direction: column;
  gap: 6px;
  
  label {
    font-size: 13px;
    font-weight: 500;
    color: var(--el-text-color-regular);
  }
}

.sub-worker-form-actions {
  display: flex;
  gap: 10px;
  justify-content: flex-end;
  margin-top: 10px;
}

.warning {
  color: var(--el-text-color-placeholder);
  cursor: help;
}
</style>
