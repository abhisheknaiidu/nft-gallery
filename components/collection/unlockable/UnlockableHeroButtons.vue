<template>
  <div>
    <div
      class="hero-buttons is-flex is-justify-content-flex-start is-align-items-end px-2">
      <div class="is-flex">
        <NeoDropdown append-to-body>
          <NeoButton
            icon="share-alt"
            class="square-32 mr-3"
            data-cy="share-button" />
          <template #items>
            <NeoDropdownItem
              v-clipboard:copy="currentUrl"
              @click.native="toast(`${$i18n.t('toast.urlCopy')}`)">
              {{ $i18n.t('share.copyLink') }}
            </NeoDropdownItem>
            <NeoDropdownItem @click.native="QRModalActive = true">
              {{ $i18n.t('share.qrCode') }}
            </NeoDropdownItem>
            <NeoDropdownItem>
              <ShareNetwork
                tag="div"
                network="twitter"
                :hashtags="hashtags"
                :url="currentUrl"
                :title="sharingLabel"
                twitter-user="KodaDot">
                {{ $i18n.t('share.twitter') }}
              </ShareNetwork>
            </NeoDropdownItem>
          </template>
        </NeoDropdown>
      </div>
    </div>
    <NeoModal v-model="QRModalActive" @close="QRModalActive = false">
      <div class="card">
        <div class="card-content">
          <QRCode :text="currentUrl" color="#db2980" bg-color="#000" />
        </div>
      </div>
    </NeoModal>
  </div>
</template>

<script setup lang="ts">
import {
  NeoButton,
  NeoDropdown,
  NeoDropdownItem,
  NeoModal,
} from '@kodadot1/brick'

const { $i18n, $buefy } = useNuxtApp()
const currentUrl = computed(() => window.location.href)

const QRModalActive = ref(false)

const hashtags = 'KusamaNetwork,KodaDot'
const sharingLabel = $i18n.t('sharing.collection')

const toast = (message: string) => {
  $buefy.toast.open({
    message,
    type: 'is-neo',
  })
}
</script>

<style lang="scss" scoped>
@import '@/styles/abstracts/variables';
.hero-buttons {
  @include mobile {
    justify-content: space-between !important;
    flex: 1;
    margin-top: 0;
    margin-bottom: 1.5rem;
  }
}
.square-32 {
  width: 32px;
  height: 32px;
}
</style>
