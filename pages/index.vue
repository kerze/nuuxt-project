<template>
  <Ad-component />
  <!-- <Tutorial /> -->
</template>

<script lang="ts">
import Vue from 'vue'
import AdComponent from '@/components/Ad.vue'
export default Vue.extend({
  name: 'IndexPage',
  components: {
    AdComponent,
  },
})
</script>
