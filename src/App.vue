<template>
  <div id="app">
    <company-header></company-header>
    <articles-dashboard></articles-dashboard>
  </div>
</template>

<script>
import CompanyHeader from './components/CompanyHeader/CompanyHeader'
import ArticlesDashboard from './components/ArticlesDashboard/ArticlesDashboard'

export default {
  name: 'app',
  components: {
      ArticlesDashboard,
      CompanyHeader
  }
}
</script>

<style lang="scss">
  @import "~@/style";
#app {
  font-family: 'PT Sans', sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  padding: 40px 0px;
  background-color: $bodyColor;
}
</style>
