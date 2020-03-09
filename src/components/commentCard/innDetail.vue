<template>
  <!-- class="inn-Detail" -->
  <div>
    <!-- class="table-list" -->
    <div class="whiteBGC">
      <h3 class="border-bottom ">团队信息: </h3>
      <el-row :gutter="20">
        <el-col :span="24">&ensp;&ensp;团队名称：{{inList.team_name}}</el-col>
        <el-col :span="24">团队负责人：{{inList.user_name}}</el-col>
      </el-row>
      <h3 class="border-bottom ">企业信息： </h3>
      <el-row :gutter="20">
        <el-col :span="24">&ensp;&ensp;企业名称：{{inList.company_name}}</el-col>
      </el-row>
      <h3 class="border-bottom ">岗位信息：</h3>
      <el-row>
        <el-col :span="24">&ensp;&ensp;岗位名称：{{inList.job_name}}</el-col>
      </el-row>
      <el-row>
        <el-col :span="24">&ensp;&ensp;薪资类型：{{inList.offermoney_type}}</el-col>
      </el-row>
      <h3 class="border-bottom ">招聘信息：</h3>
      <el-row :gutter="20">
        <el-col :span="24">&ensp;&ensp;招聘类型：{{inList.jobType}}</el-col>
        <el-col :span="24">&ensp;&ensp;招聘人数：{{inList.number}}</el-col>
      </el-row>
      <el-row :gutter="20">
        <el-col :span="24">
          &ensp;&ensp;创建日期：{{$moment.unix(inList.ctime).format('YYYY-MM-DD HH:mm')}}
          <!-- 创建日期: {{getTime(inList.ctime)}} -->
        </el-col>
      </el-row>
      <div style="margin: 30px 0;"></div>
      <el-button plain @click="closeMe">关闭</el-button>
    </div>
  </div>
</template>
<script>
import { commentSort } from '../../base/base'
import { seeTeamJobInfo } from '../../api/comment'
export default {
  data () {
    return {
      uid: localStorage.getItem('sys_uid'),
      username: localStorage.getItem('username'),
      inList: {},//发单列表
      id: '',
      uid: localStorage.getItem('sys_uid'),
    }
  },
  created () {
    console.log(this.inList,'this.inList')
    this.inList = this.$route.query.val
    console.log(this.uid)
    this.getDetail(this.uid)
  },
  methods: {
    //获取详情
    getDetail (uid) {
      seeTeamJobInfo({ uid }).then(res => {
        this.inList = this.$route.query.val

      })
    },
    closeMe () {
      this.$router.go(-1)
    },
  }
}
</script>
<style scoped>
  .el-col {
    margin: 15px 0 !important;
  }
  h3{
    padding: 20px 0;
    font-family:'Alibaba-PuHuiTi-Bold';
    font-weight: bold !important;
    font-size: large;
  }
  .border-bottom {
    border-bottom: 1px solid #eee;
  }
  .row-bg {
    padding: 50px 0;
    background-color: #f9fafc;
  }
  .whiteBGC{
    background: #fff;
    border-radius:5px;
    padding: 15px;
  }
</style>