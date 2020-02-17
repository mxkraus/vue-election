<template>
  <div id="app">
    <Election :candata="this.myJson" />
  </div>
</template>

<script>

import Election from './components/Election.vue';
import json from './models/candidates.json';

export default {
  name: 'app',
  data(){
    return{
      myJson: json
    }
  },
  components: {
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
  margin: 10% auto;
}

@media only screen and (min-width: 768px) {
  #app {
    margin: 2% auto;
  }
}
</style>
