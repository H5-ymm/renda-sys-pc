<template>
  <div class="team-main-view">
    <el-container>
      <el-aside width="210px" class="team-aside">
        <home-aside></home-aside>
      </el-aside>
      <el-container>
        <el-header height="75px">
          <div style="height:37px;" class="x-flex-between team-header">
            <i class="el-icon-refresh-right"></i>
            <div class="x-flex-center">
              <el-link :underline="false" @click="outLogin">退出登录</el-link>

              <i class="el-icon-bell unRead"></i>
              <span>{{userinfo}}</span>
            </div>
          </div>
          <breadcrumb :breadcrumbs="breadcrumb"></breadcrumb>
        </el-header>
        <el-main class="team-main">
          <router-view class="team-box"></router-view>
        </el-main>
      </el-container>
    </el-container>
  </div>
</template>

<script>
// @ is an alias to /src
import homeAside from '@/components/Aside' //侧边栏
import Breadcrumb from '@/components/breadcrumb/Breadcrumb'
export default {
  name: 'team',
  data () {
    return {
      userinfo:'',
      breadcrumb: [],
    }
  },
  components: {
    homeAside,
    Breadcrumb
  },
  methods: {
    outLogin(){
      this.$router.push('load')
      localStorage.removeItem('sys_uid')
      localStorage.removeItem('sys_userInfo')
      localStorage.removeItem('sys_token')
    }
  },
  created () {
    this.breadcrumb = JSON.parse(sessionStorage.getItem('menus'))
    this.userinfo = JSON.parse(localStorage.getItem('sys_userInfo')).name
    console.log(this.userinfo,'userinfo')
    console.log(typeof JSON.parse(localStorage.getItem('sys_userInfo')),'typeof localStorage.getItem.userinfo')
    console.log(JSON.parse(localStorage.getItem('sys_userInfo')).name,'localStorage.getItem.userinfo')
  }
}
</script>

<style scoped lang="scss">
.team-main-view{
  width: 100vw;
  height: 100vh;
  /* overflow-y: hidden; */
  /* overflow: auto; */
  overflow: hidden;
  position: relative;
  .unRead {
    margin: 0 10px;
  }
  .el-header {
    padding: 0;
  }
  .team-header {
    padding: 0 20px;
  }
}
.team .team-header {
  padding: 0 38px;
}
.team-aside{
  /* height: 100vh; */
  overflow: hidden;
}
.team-main{
  height: 100vh;
  /* overflow: hidden; */
  background: #F0F2F5;
  padding: 30px;
  box-sizing: border-box;
}
</style>
