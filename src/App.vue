<template>
  <div id="app">
    <div class="color-list">
      <el-tag :color="item.color" v-for="(item,index) in colorList"
              :key="index"
              @click="onChangeThemeColor(item,index)"
      ></el-tag>
    </div>
    <router-view/>
  </div>
</template>

<script>
import { updateTheme} from './utils/setting'
export default {
  name: 'App',
  data(){
    return{
      colorList:[{
        key: '薄暮', color: '#F5222D',
        }, {
          key: '火山', color: '#FA541C',
        }, {
          key: '日暮', color: '#FAAD14',
        }, {
          key: '明青', color: '#13C2C2',
        }, {
          key: '极光绿', color: '#52C41A',
        }, {
          key: '拂晓蓝（默认）', color: '#1890FF',
        }, {
          key: '极客蓝', color: '#2F54EB',
        }, {
          key: '默认', color: '#013b4e',
        }]
    }
  },
  created(){
    this.$store.dispatch('analysis/setDefaultNode')
    // window.less = {
    //   async: true,
    //   env: 'development',
    //   javascriptEnabled: true
    // };
  },
  methods:{
    onChangeThemeColor(item,index){
      document.body.style.setProperty('--mainBg',item.color)
    }
  }
}
</script>
<style lang="less">
@import url('./less/index.less');
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  color: #606266;
}
.color-list{
  overflow: hidden;
  margin:100px 0 0 30px;
  position: fixed;
  right:20px;
  top:100px;
  z-index:999;
  .el-tag{
    padding:0 15px;
  }
}
:root{
  --mainBg:#013b4e;
}
</style>
