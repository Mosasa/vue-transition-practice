<template>
  <div class="hello">
       <router-link to="/next1">下一页</router-link> 
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  data () {
    return {
      msg: 'Welcome to Your Vue.js App'
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.hello{
  width: 100%;
  height: 650px;
  position: absolute;
  background: yellow;
}
</style>
