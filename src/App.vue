<template>
  <div id="app">
    <Election :candata="this.myJson" />
    <Footer></Footer>
  </div>
</template>

<script>

import Election from './components/Election.vue';
import Footer from './components/Footer.vue';
import json from './models/candidates.json';

export default {
  name: 'app',
  data(){
    return{
      myJson: json
    }
  },
  components: {
    Election,
    Footer
  }
}
</script>

<style>
body{
  margin: 0;
}
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #111;
  /* margin: 10% auto; */
}

footer{
  width: 100%;
  height: 100px;
  background-color: #ecf0f1;
  position: relative;
  bottom: 0;
}

@media only screen and (min-width: 768px) {
  #app {
    margin-top: 3%;
  }
}

@media only screen and (max-width: 768px) {
  /* body {
    margin: 0 2%;
  } */
} 


</style>
