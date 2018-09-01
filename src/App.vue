<template>
  <div id="app">
    <cards></cards>
  </div>
</template>

<script>
import Cards from './components/Cards.vue'

export default {
  name: 'app',
  components: {
    Cards
  }
}
</script>

<style lang="scss">
  @import 'scss/style';

  #app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
  }

  .wrapper {
    max-width: 1200px;
    margin: 30px auto;
    padding: 0 15px;
  }
</style>
