<template>
  <div class="monitor-modal">
    <el-dialog
      title="选择监控器"
      :visible.sync="showDialog"
      :show-close="false"
      width="30%"
    >
      <el-tree
        :data="monitorTree"
        :props="treeProps"
        node-key="id"
        :default-expanded-keys="[1]"
        ref="navTree"
        highlight-current
        @node-click="onClickTree"
      >
      </el-tree>
      <span slot="footer" class="dialog-footer">
    <el-button @click="onClickCancelBtn">取 消</el-button>
    <el-button type="primary" @click="onClickSureBtn">确 定</el-button>
  </span>
    </el-dialog>
  </div>
</template>

<script>
  import {mapState} from 'vuex'
   import CommonApi from '../../service/api/commonApi'
  export default {
    name: 'Monitor',
    components: {
    },
    data () {
      return {
        monitorTree:{},
        treeProps:{
          label:'text',
          children: 'children',
        },
        curNode:{},
        curRoute:this.$route.path
      }
    },
    computed:{
      ...mapState({
        showDialog:state=>state.analysis.showDialog,
        curSelect:state=>state.analysis.curSelect
      }),
      curSelect(){
         return this.curRoute=='/correlationAnalysis'?this.$store.state.analysis.curSelect:
                this.curRoute=='/statisCompare'?this.$store.state.analysis.statisCurSelect:1
      }
     },
    methods: {
      async getMonitorTree(){
        let res =  await CommonApi.getMonitorTree()
        this.monitorTree=res
      },
      onClickSureBtn(){
        let val = this.curNode
        if(this.curNode.id){
          if(this.curRoute=='/correlationAnalysis'){
            if(this.curSelect==1){
              this.$store.commit('analysis/monitor1',{id:val.id,text:val.text})
            }else{
              this.$store.commit('analysis/monitor2',{id:val.id,text:val.text})
            }
          }else if(this.curRoute=='/statisCompare'){
            if(this.curSelect==1){
              this.$store.commit('analysis/statisMonitor1',{id:val.id,text:val.text})
            }else{
              this.$store.commit('analysis/statisMonitor2',{id:val.id,text:val.text})
            }
          }else{
            this.$store.commit('analysis/historyMonitor',{id:val.id,text:val.text})
          }
        }
        this.$store.commit('analysis/showDialog',false)
      },
      onClickCancelBtn(){
        this.$store.commit('analysis/showDialog',false)
      },
      onClickTree(val){
        this.curNode=val
      }
    },
    mounted(){
      this.getMonitorTree()
    }
  }
</script>

<style lang="less">
  .monitor-modal{
    .el-dialog__body{
      height:400px;
      overflow: auto;
    }
    .el-tree-node__content:hover{
      background: #e6e6e6;
    }

  }

</style>
