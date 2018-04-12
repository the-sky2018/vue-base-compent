<template>
  
  <div class="hello">
    <div @click="show=!show">click me</div>
    <div v-if="show">
      <window></window>  
    </div>
    
    <!-- <window></window> -->
  </div>
</template>

<script>

import moveBox from './move-box/moveBxo';
import window from './window'
export default {

  name: 'HelloWorld',
  components: {
    window
  },
  data () {
    return {
      msg: 'Welcome to Your Vue.js App',
      show:true
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1, h2 {
  font-weight: normal;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
