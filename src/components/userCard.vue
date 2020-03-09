<template>
  <div>
    <p class="company-title">账户信息</p>
    <section class="section-box">
      <el-form-item label="账户名称" prop="team_name">
        <el-input v-model="userInfo.name" class="width408" placeholder="请输入团队名称"></el-input>
      </el-form-item>
      <el-form-item label="账号密码">
        <el-button type="primary" @click="handlePassword">重置密码</el-button>
        <span class="password-tip">
          <i class="el-icon-warning-outline" /> 密码重置:12345，不可逆转，谨慎操作
        </span>
      </el-form-item>
    </section>
  </div>
</template>
<script>
import { resetPassword } from '../api/company'
export default {
  data () {
    return {
      userInfo: {}
    }
  },
  created () {
    if (localStorage.getItem('sys_userInfo')) {
      this.userInfo = JSON.parse(localStorage.getItem('sys_userInfo'))
    }
    console.log(this.userInfo,'userInfo')
  },
  methods: {
    handlePassword () {
      console.log(1111)
      this.$alert('密码将设置为123456<br>确定重置吗?', '密码重置', {
        dangerouslyUseHTMLString: true,
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        let uid = this.userInfo.id
        resetPassword({ uid }).then(res => {
          this.$message.success('重置成功')
          // this.$parent.getList(this.$parent.formParams) 
        })
      }).catch(() => {
        console.log(2)
      })
    },
  }
}
</script>