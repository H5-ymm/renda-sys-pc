<template>
  <div class="tables-box">
    <div class="table-list team-forum">
      <h1 style="margin-top: 15px;">查看简历</h1>
      <div style="margin: 30px 0;"></div>
      <h3>基本信息</h3>
      <el-row :gutter="20">
        <el-col :span="12">姓&emsp;&emsp;名: &ensp;{{listdata.name}}</el-col>
        <el-col :span="12">年&emsp;&emsp;龄: &ensp;{{listdata.age}}</el-col>
      </el-row>
      <el-row :gutter="20">
        <el-col :span="12">手机号码: &ensp;{{listdata.mobile}}</el-col>
        <el-col :span="12">性&emsp;&emsp;别: &ensp;
          <span v-if="listdata.sex == 1">男</span>
          <span v-if="listdata.sex == 2">女</span>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="24">学&emsp;&emsp;历: &ensp;{{listdata.education}}</el-col>
      </el-row>
      <el-row>
        <el-col :span="24">详细地址: &ensp;{{listdata.address}}</el-col>
      </el-row>
      <h3 style="margin-top: 15px;">意向信息</h3>
      <el-row :gutter="20">
        <el-col :span="12">期望城市:&ensp; {{listdata.expect_cityid}}</el-col>
        <el-col :span="12">期望岗位:&ensp; {{listdata.desired_position}}</el-col>
      </el-row>
      <el-row :gutter="20">
        <el-col :span="12">期望薪资: &ensp;{{listdata.money}}</el-col>
        <el-col :span="12">期望模式: &ensp;{{listdata.mode}}</el-col>
      </el-row>
      <el-row>
        <el-col :span="24">预计入职时间:&ensp; </el-col>
      </el-row><el-row>
        <el-col :span="24">缴纳五险: &ensp;
            <span v-if="listdata.is_five_risks == 1" >是</span>
            <span v-if="listdata.is_five_risks == 2" >否</span>
            <span v-if="listdata.is_five_risks == 3" >试用期过后</span>
        </el-col>
      </el-row><el-row>
        <el-col :span="24">
            <span>缴纳公积金: &ensp;</span>
            <span v-if="listdata.is_fund == 1" >是</span>
            <span v-if="listdata.is_fund == 2" >否</span>
            <span v-if="listdata.is_fund == 3" >试用期过后</span>
        </el-col>
      </el-row><el-row>
        <el-col :span="24">备&emsp;&emsp;注: &ensp;{{listdata.remark}}</el-col>
      </el-row>
      <div style="margin: 30px 0;"></div>
      <el-button plain @click="closeMe">关闭</el-button>
    </div>
  </div>
</template>
<script>
  import {seeUserResume} from '../api/cvlook';
  export default {
    // props: [startPlaceholder, endPlaceholder],
    data () {
      return {
        fiveOneList:[
          { label: '是', value: 1 },
          { label: '否', value: 2 },
          { label: '试用期过后', value: 3 }
        ],
        listdata:{},
        params: {
          uid: localStorage.getItem('sys_uid'),
          resumeId: '1'
        }
      }
    },
    created () {
      this.params.resumeId = this.$route.query.id
      this.getList(this.params)
    },
    methods: {
      closeMe () {
        this.$router.go(-1)
      },
      getList (params) {
        seeUserResume(params).then(res => {
          if (res.data) {
            this.listdata = res.data || []
          }
          else {
            this.commentInfo = null
          }
        }).catch(error => {
          this.$message.error(error.status.remind)
        })
      },
    }
  }
</script>
<style scoped>
  .el-col {margin: 15px 0 !important;}
  h1{font-family:'Alibaba-PuHuiTi-Heavy';font-weight: bold !important;font-size: larger;}
  h3{font-family:'Alibaba-PuHuiTi-Bold';font-weight: bold !important;font-size: large;}
</style>