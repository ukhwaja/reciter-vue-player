<template>
  <div id="app">
    <MyMuqri/>
    <footer>
      <div>Text JSON from <a href="https://github.com/semarketir/quranjson" target="_blank">github</a></div>
      <div>Audio from <a href="http://everyayah.com/data/status.php" target="_blank">everyayah.com</a></div>
      <div>Fonts from <a href="http://fonts.qurancomplex.gov.sa/" target="_blank">qurancomplex.gov.sa</a></div>
      <div><i class="vuejs icon big" title="Vuejs"></i><a href="https://vuejs.org" target="_blank">Vue.js</a> project</div>
      <div><i class="code icon"></i> with <i class="heart icon"></i> by <a href="https://numbfall.github.io" target="_blank">Usman</a></div>
    </footer>
  </div>
</template>

<script>
import MyMuqri from './components/myMuqri'

export default {
  name: 'App',
  components: {
    MyMuqri
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
footer {
  margin-top: 60px;
}
</style>
