<template>
  <div>
    <!-- <video class="content-element-in-game" :class="{ hidden: !showAds }"></video> -->
    <!-- <div class="ad-container-in-game" :class="{ hidden: !showAds }"></div> -->
    <video class="content-element-in-game"></video>
    <div class="ad-container-in-game"></div>
    <button class="ads-btn" @click="playAds">Ads</button>
  </div>



</template>

<script>
export default {
  name: 'AdComponent',

  props: {},

  data() {
    return {
      adsManager: null,
      videoContent: null,
      adDisplayContainer: null,
      showAds: false,
    }
  },

  computed: {},

  mounted() {
    setTimeout(() => {
      this.initializeADS()
    }, 1000)
  },

  methods: {
    initializeADS() {
      this.videoContent = document.querySelector('.content-element-in-game')
      const adContainer = document.querySelector('.ad-container-in-game')
      console.log('elements', this.videoContent, adContainer)
      this.adDisplayContainer = new window.google.ima.AdDisplayContainer(adContainer, this.videoContent)
      console.log('adDisplayContainer', this.adDisplayContainer)

      const adsLoader = new window.google.ima.AdsLoader(this.adDisplayContainer)
      console.log('adsLoader', adsLoader)

      adsLoader.addEventListener(
        window.google.ima.AdsManagerLoadedEvent.Type.ADS_MANAGER_LOADED,
        (adsManagerLoadedEvent) => {
          console.log('adsManagerLoadedEvent', adsManagerLoadedEvent)
          const adsRenderingSettings = new window.google.ima.AdsRenderingSettings()
          adsRenderingSettings.restoreCustomPlaybackStateOnAdBreakComplete = true
          this.adsManager = adsManagerLoadedEvent.getAdsManager(this.videoContent, adsRenderingSettings)
          // this.adsManager.addEventListener(window.google.ima.AdErrorEvent.Type.AD_ERROR, onAdError)
          // this.adsManager.addEventListener(window.google.ima.AdEvent.Type.CONTENT_PAUSE_REQUESTED, onContentPauseRequested)
          // this.adsManager.addEventListener(window.google.ima.AdEvent.Type.CONTENT_RESUME_REQUESTED, onContentResumeRequested)
          this.adsManager.addEventListener(window.google.ima.AdEvent.Type.ALL_ADS_COMPLETED, (onAdEvent) => {
            console.log('complete1', onAdEvent)
          })
          // this.adsManager.addEventListener(window.google.ima.AdEvent.Type.LOADED, onAdEvent)
          // this.adsManager.addEventListener(window.google.ima.AdEvent.Type.STARTED, onAdEvent)
          this.adsManager.addEventListener(window.google.ima.AdEvent.Type.COMPLETE, (onAdEvent) => {
            console.log('complete2', onAdEvent)
            // this.showAds = false
          })
        },
        false
      )

      adsLoader.addEventListener(
        window.google.ima.AdErrorEvent.Type.AD_ERROR,
        (adErrorEvent) => {
          console.log('adErrorEvent', adErrorEvent.getError())
        },
        false
      )
      const contentEndedListener = () => {
        adsLoader.contentComplete()
      }
      this.videoContent.onended = contentEndedListener

      const adsRequest = new window.google.ima.AdsRequest()
      adsRequest.adTagUrl =
        'https://pubads.g.doubleclick.net/gampad/ads?iu=/21775744923/external/single_ad_samples&sz=640x480&cust_params=sample_ct%3Dlinear&ciu_szs=300x250%2C728x90&gdfp_req=1&output=vast&unviewed_position_start=1&env=vp&impl=s&correlator='
      adsRequest.linearAdSlotWidth = this.videoContent.clientWidth
      adsRequest.linearAdSlotHeight = this.videoContent.clientHeight
      adsRequest.nonLinearAdSlotWidth = this.videoContent.clientWidth
      adsRequest.nonLinearAdSlotHeight = this.videoContent.clientHeight / 3
      adsLoader.requestAds(adsRequest)
    },
    playAds() {
      // this.showAds = true
      this.adDisplayContainer.initialize()
      const width = this.videoContent.clientWidth
      const height = this.videoContent.clientHeight

      try {
        this.adsManager.init(width, height, window.google.ima.ViewMode.NORMAL)
        this.adsManager.start()
        setTimeout(() => {
          // this.showAds = false
        }, 11500)
      } catch (adError) {
        // this.showAds = false
      }
    },
  },
}
</script>

