<template>
  <div class="monitor-choose">
    <div class="flex-align choose-box">
      <span class="choose-tip">时间</span>
      <div class="block" v-if="showDateType">
        <span class="demonstration">条件选择</span>
        <el-select v-model="curDateType" placeholder="请选择" @change="handleDateTypeChange">
          <el-option v-for="item in dateTypeList" :key="item.id" :label="item.name" :value="item.id">
          </el-option>
        </el-select>
      </div>
      <div class="block">
        <span class="demonstration">开始时间</span>
        <el-date-picker
          v-model="startTime"
          :type="showDateType?pickerType:dateTime"
          placeholder="选择日期时间"
          value-format="yyyy-MM-dd HH:mm:ss"
          @change="onTimeChange(1)"
        >
        </el-date-picker>
      </div>
      <div class="block">
        <span class="demonstration">结束时间</span>
        <el-date-picker
          v-model="endTime"
          :type="showDateType?pickerType:dateTime"
          placeholder="选择日期时间"
          value-format="yyyy-MM-dd HH:mm:ss"
          @change="onTimeChange(-1)"
        >
        </el-date-picker>
      </div>
    </div>
    <div class="flex-align choose-box">
      <span class="choose-tip">监测器选择</span>
      <div class="block flex-align">
        <span>{{monitorText1}}</span>
        <el-button @click="onClickShowBtn(1)">请选择</el-button>
      </div>
      <div class="block flex-align" v-if="showTwoMonitor">
        <span>{{monitorText2}}</span>
        <el-button @click="onClickShowBtn(2)">请选择</el-button>
      </div>
    </div>
  </div>
</template>

<script>
  import {mapState} from 'vuex'
  import moment from 'moment'
  import CommonApi from '../../../service/api/commonApi'
  import MonitorModal from '../../../components/monitorModal/index'
  export default {
    name: 'DeviceAnalysis',
    components: {
      MonitorModal,
    },
    props:['showDateType','showTwoMonitor'],
    data () {
      return {
        startTime:new Date(new Date().getTime()-5*24*60*60*1000),
        endTime:new Date(),
        dateTypeList:[{
            name:'年',
            id:4
          },{
            name:'月',
            id:3
          },{
            name:'日',
            id:2
        }],
        curDateType:2,
        pickerType:'date', //变化的 年、年月、年月日
        dateTime:'datetime' //年月日时分秒
      }
    },
    computed:{
      ...mapState({
      }),
      monitorText1(){
        return this.$route.path=='/correlationAnalysis'?this.$store.state.analysis.monitor1.text:
               this.$route.path=='/statisCompare'?this.$store.state.analysis.statisMonitor1.text:
               this.$store.state.analysis.historyMonitor.text
      },
      monitorText2(){
        return this.$route.path=='/correlationAnalysis'?this.$store.state.analysis.monitor2.text:
          this.$store.state.analysis.statisMonitor2.text
      },
    },
    watch:{
    },
    methods: {
      onClickShowBtn(flag){
        if(this.$route.path=='/correlationAnalysis'){
          this.$store.commit('analysis/curSelect',flag)
        }else if(this.$route.path=='/statisCompare'){
          this.$store.commit('analysis/statisCurSelect',flag)
        }

        this.$store.commit('analysis/showDialog',true)
      },
      onTimeChange(flag){
        let format = this.pickerType=='year'?'YYYY':this.pickerType=="month"?'YYYY-MM':'YYYY-MM-DD'
        if(this.$route.path=='/correlationAnalysis'){
          if(flag==1){
            this.$store.commit('analysis/startTime',this.startTime)
          }else{
            this.$store.commit('analysis/endTime',this.endTime)
          }
        }else if(this.$route.path=='/statisCompare'){
          if(flag==1){
            this.$store.commit('analysis/statisStartTime',moment(this.startTime).format(format))
          }else{
            this.$store.commit('analysis/statisEndTime',moment(this.endTime).format(format))
          }
        }else{
          if(flag==1){
            this.$store.commit('analysis/historyStartTime',moment(this.startTime).format(format))
          }else{
            this.$store.commit('analysis/historyEndTime',moment(this.endTime).format(format))
          }
        }
      },
      handleDateTypeChange(value){
        this.pickerType=value==4?'year':value==3?"month":'date'
        let format=value==4?'YYYY':value==3?'YYYY-MM':'YYYY-MM-DD'
        console.log(this.startTime,format)
         if(this.$route.path=='/statisCompare'){
            this.$store.commit('analysis/statisFilterType',value)
            this.$store.commit('analysis/statisStartTime',moment(this.startTime).format(format))
            this.$store.commit('analysis/statisEndTime',moment(this.endTime).format(format))
        }else if(this.$route.path=='/historyStatis'){
           this.$store.commit('analysis/historyFilterType',value)
           this.$store.commit('analysis/historyStartTime',moment(this.startTime).format(format))
           this.$store.commit('analysis/historyEndTime',moment(this.endTime).format(format))
        }
      }
    },
    mounted(){
    }
  }
</script>

<style lang="less">
  .monitor-choose{
    .choose-box{
      padding:20px 0;
      border-bottom: 1px solid #eaeaea;
    }
    .block{
      margin:0 40px;
    }
    .choose-tip{
      margin-left: 100px;
      width:80px;
      text-align: right;
    }
    .el-button{
      margin:0 10px;
    }
  }
</style>
