<template>
  <div class="hello">
    <NavList />
    <router-view></router-view>
  </div>
</template>

<script>
  import NavList from './coms/navList'
  export default {
    name: 'HelloWorld',
    components: {
      NavList
    },
    data () {
      return {
        msg: 'Welcome to my basic vue demo'
      }
    },
    methods: {
    },
    mounted(){
    }
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="less">
</style>
