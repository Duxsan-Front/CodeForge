<template>
  <div id="app">
    <Header v-if="showHeader" />
    <router-view />
  </div>
</template>

<script>
import Header from '@/components/Header/Header.vue'

export default {
  name: 'App',
  components: {
    Header
  },
  computed: {
    showHeader() {
      return this.$route.name !== 'auth'
    }
  }
}
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  background-color: #0E1117;
  font-family: Arial, sans-serif;
}
</style>