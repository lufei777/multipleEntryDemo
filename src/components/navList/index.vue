<template>
    <div class="father-nav flex-align-around">
      <div class="sys-title">{{sysTitle}}</div>
      <div class="father-nav-list flex-align-around">
        <div v-for="(item,index) in menuData.children" :key="index" class="father-item"
             @mouseenter="changeBg(item,1)" @mouseleave="changeBg(item,-1)"
             @click="clickChangeBg(index)"
             :class="(item.bgFlag==-1 && item.clickFlag!=1)?'':'activeBg'"
        >
          <span>{{item.caption}}</span>
          <div class="child-nav-list" v-if="item.children && item.children.length && item.showChild==1">
            <div v-for="(child,i) in item.children" :key="i" class="child-item"
                 @mouseenter="changeBg(child,1)" @mouseleave="changeBg(child,-1)"
                 @click.stop="clickChangeChild(index,i)"
                 :class="(child.bgFlag==-1 && child.clickFlag!=1)?'':'activeChild'">
              {{child.caption}}
            </div>
          </div>
        </div>
      </div>
      <div class="operator-box">
        <i class="fa fa-bell-o"></i>
        <i class="fa fa-question-circle-o"></i>
        <i class="fa fa-user-o"></i>
        <span>admin</span>
      </div>
    </div>
</template>

<script>
  import CommonApi from '../../service/api/commonApi'
  import CommonData from '../../utils/commonData'
  export default {
    name: 'navList',
    components: {
    },
    data () {
      return {
        menuData:{},
        sysTitle:{},
      }
    },
    computed:{
    },
    methods: {
      onClickNavItem(val){
        console.log(val)
        this.$router.push(val)
      },
      async getMenus(){
        // let res = await CommonApi.getMenus()
        let res = CommonData.menuData
        res.children.map((item)=>{
           item.bgFlag=-1 //鼠标移入移出背景
           item.clickFlag=-1 //鼠标点击背景
           item.showChild=-1 //是否显示子菜单
           item.children.map((child)=>{
              child.bgFlag=-1
              child.clickFlag=-1
           })
        })
        let activeNav =Cookies.get('activeNav') && JSON.parse(Cookies.get('activeNav'))
        console.log(activeNav)
        if(activeNav){
          let tmp=res.children[activeNav.fatherIndex]
              tmp.clickFlag=1
              tmp.showChild=1
          if(tmp.children.length){
            tmp.children[activeNav.childIndex].clickFlag=1
            this.$router.push(tmp.children[activeNav.childIndex].url)
          }else{
            this.$router.push(tmp.url)
          }
        }else{
          res.children[0].clickFlag=1
          res.children[0].showChild=1
          this.$router.push(res.children[0].url)
        }
        this.menuData = res
      },
      async login(){
        let res = await CommonApi.login({
          login_id:'admin',
          password:'123456'
        })
      },
      async getSystemSetting(){
        let res = await CommonApi.getSystemSetting()
        res.map((item)=>{
          if(item.name=='home.title'){
            this.sysTitle = item.value
          }
        })
      },
      changeBg(item,flag){
        item.bgFlag=flag
      },
      clickChangeBg(index){
        this.menuData.children.map((item)=>{
          item.clickFlag=-1
          item.bgFlag=-1
          item.showChild=-1
        })
        this.menuData.children[index].clickFlag=1
        this.menuData.children[index].showChild=1
        if(this.menuData.children[index].children.length){
          this.menuData.children[index].children.map((item)=>{
            item.clickFlag=-1
          })
          this.menuData.children[index].children[0].bgFlag=1
          this.menuData.children[index].children[0].clickFlag=1
        }
        this.$router.push(this.menuData.children[index].url)
        Cookies.set('activeNav',{fatherIndex:index,childIndex:0})
      },
      clickChangeChild(index,i){
        this.menuData.children.map((item)=>{
            item.children.map((child)=>{
              child.clickFlag=-1
              child.bgFlag=-1
            })
        })
        this.menuData.children[index].children[i].clickFlag=1
        this.$router.push(this.menuData.children[index].children[i].url)
        Cookies.set('activeNav',{fatherIndex:index,childIndex:i})
      }
    },
    async mounted(){
      // await this.login()
       this.getMenus()
       this.getSystemSetting()
    }
  }
</script>

<style lang="less">
  .father-nav{
    background: url("../../../static/image/nav1.png");
    width:100%;
    height: 50px;
    padding: 0 20px;
    position: fixed;
    left:0;
    top:0;
    z-index: 99;
    box-sizing: border-box;
    .sys-title{
      flex-shrink: 0;
      font-size: 24px;
      color:@white;
      font-weight: 600;
    }
    .father-nav-list{
      width:100%;
      flex-shrink: 1;
      padding:0 15px;
      .father-item{
        text-align: center;
        color:@navFontColor;
        cursor: pointer;
        font-size: 18px;
        width:100%;
        height:50px;
        line-height: 50px;
      }
      .activeBg{
        background-image: url("../../../static/image/nav_hover.png");
        background-position: 0 0;
        background-size: 100% 100%;
      }
    }
    .operator-box{
      flex-shrink: 0;
      color:@navFontColor;
      font-size: 20px;
      i{
        margin: 0 2.5px;
      }
      &:hover{
        cursor: pointer;
      }
    }
  }
  .child-nav-list{
    background: url('../../../static/image/nav2.png');
    height: 35px;
    width: 100%;
    position: absolute;
    left:0;
    display: flex;
    justify-content: center;
    .child-item{
      float: left;
      line-height: 35px;
      padding:0 20px;
      margin:0 10px;
      font-size: 16px;
      color:@white;
      background: rgba(0,0,0,.3);
      &:hover{
        cursor: pointer;
      }
    }
    .activeChild{
      color:@navChildColor;
    }
  }
</style>
