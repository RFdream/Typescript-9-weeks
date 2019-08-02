<template>
  <div class="home">
    <div id="nav">
      <router-link to="/">Home</router-link> |
      <router-link to="/clock">Week 1</router-link> | 
      <router-link to="/clock">Week 2</router-link> | 
      <a href="https://xd.adobe.com/spec/ce0b0dad-a4b8-4035-4881-7075520f913c-b83a/grid" target="_blank">Week 1參考</a>
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator' // @ is an alias to /src
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

