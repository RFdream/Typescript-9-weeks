<template>
  <div class="home">
    
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator'; // @ is an alias to /src
import Clock from '@/components/Clock.vue'

@Component({
  components: {
    Clock
  },
})
export default class Home extends Vue {}
</script>

<style lang="scss">
  .home {
    text-align: center;
  }
</style>

