<template>
  <div id="app">
    <Election />
  </div>
</template>

<script>

import Calculator from './components/Calculator.vue';
import Election from './components/Election.vue';

export default {
  name: 'app',
  components: {
    Calculator,
    Election
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #111;
  margin: 20% auto;
}

@media only screen and (min-width: 768px) {
  #app {
    margin: 5% auto;
  }
}
</style>
