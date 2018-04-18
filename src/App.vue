<template>
  <div id="app">
    <h1>Memorize Qur'an</h1>
    <MyMuqri/>
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
</style>
