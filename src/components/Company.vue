<template>
  <div class="tables-box">
    <div class="table-list company-list">
      <el-form :model="formParams" class="demo-form-inline">
        <el-form-item label="搜索类型:">
          <div class="x-flex-start searchvalue-query">
            <el-select v-model="value" placeholder="请选择" class="width100">
              <el-option label="企业名称" value="com_name"></el-option>
              <el-option label="手机号" value="telphone"></el-option>
              <el-option label="用户编码" value="id"></el-option>
            </el-select>
            <el-input class="query-input" v-model="keyword" @change="changeInput" placeholder="请输入关键词"></el-input>
            <el-button type="primary" @click="getList(formParams)" class="select-btn">搜索</el-button>
            <el-button type="primary" @click="show=!show" class="collapse-btn">{{show?'收起':'展开'}}</el-button>
          </div>
        </el-form-item>
        <el-collapse-transition>
          <div v-show="show">
            <el-form-item label="登录时间:">
              <el-link :underline="false" :class="{'active': formParams.login_data==item.value}" @click="selectQuery('login_data',item)" v-for="(item,index) in timeList" :key="index">{{item.label}}</el-link>
            </el-form-item>
            <el-form-item label="注册时间:">
              <el-link :underline="false" :class="{'active': formParams.reg_date==item.value}" @click="selectQuery('reg_date',item)" v-for="(item,index) in timeList" :key="index">{{item.label}}</el-link>
            </el-form-item>
            <el-form-item label="状态筛选:">
              <el-link :underline="false" :class="{'active': formParams.status==item.value}" @click="selectQuery('status',item)" v-for="(item,index) in statusList" :key="index">{{item.label}}</el-link>
            </el-form-item>
          </div>
        </el-collapse-transition>
        <el-form-item class="hanble-btn">
          <el-button type="primary" @click="handleTeam('check',uid)" class="select-btn">审核</el-button>
          <el-button @click="handleTeam('')" class="select-btn">删除</el-button>
          <el-button @click="handleTeam('lock',uid)" class="select-btn">锁定</el-button>
        </el-form-item>
      </el-form>
      <el-table :data="tableData" ref="multipleTable" style="width: 100%" @selection-change="handleSelectionChange">
        <el-table-column type="selection" align="center" width="60"></el-table-column>
        <el-table-column label="用户编号" prop="id" align="center" width="80"></el-table-column>
        <el-table-column label="企业名称" prop="com_name" align="center" width="200"></el-table-column>
        <el-table-column label="登录/注册" prop="login_date" align="center" width="150">
          <template slot-scope="prop">
            <p>{{ $moment.unix(prop.row.login_date).format('YYYY-MM-DD HH:mm') }}</p>
            <p>{{$moment.unix(prop.row.reg_date).format('YYYY-MM-DD HH:mm') }}</p>
          </template>
        </el-table-column>
        <el-table-column label="手机号" prop="phone" align="center" width="150"></el-table-column>
        <el-table-column label="认证信息" align="center" width="120">
          <template slot-scope="scope">
            <div class="caozuo">
              <div>
                <img v-if="scope.row.phone" src="../assets/img/member/phone.png" title="手机">
                <img v-else src="../assets/img/member/phone1.png" title="手机">
                <img v-if="scope.row.bank_status==0" src="../assets/img/member/card1.png" title="银行卡">
                <img v-else-if="scope.row.bank_status==1" src="../assets/img/member/card.png" title="银行卡">
                <img v-if="scope.row.wx_status==1" src="../assets/img/member/wx.png" title="微信">
                <img v-else-if="scope.row.wx_status==0" src="../assets/img/member/wx1.png" title="微信">
                <div>
                  <img v-if="scope.row.idcard_status==1" src="../assets/img/member/IDCard.png" title="身份证">
                  <img v-else-if="scope.row.idcard_status==0" src="../assets/img/member/IDCard1.png" title="身份证">
                  <img v-if="scope.row.email_status==1" src="../assets/img/member/email.png" title="邮箱">
                  <img v-else-if="scope.row.email_status==0" src="../assets/img/member/email1.png" title="邮箱">
                  <img v-if="scope.row.alipay_status==1" src="../assets/img/member/zfb.png" title="支付宝">
                  <img v-else-if="scope.row.alipay_status==0" src="../assets/img/member/zfb1.png" title="支付宝">
                </div>
              </div>
            </div>
          </template>
        </el-table-column>
        <el-table-column label="状态" align="center" width="100">
          <!-- <template slot-scope="props">
            <span v-if="props.row.status==1 || props.row.status==2 || props.row.status==3">{{ props.row.status === 1 ? '待审核': props.row.status === 2 ? '已通过': '未通过'}}</span>
            <span v-else-if="props.row.is_lock==1">{{ '已锁定' }}</span>
          </template>-->

          <template slot-scope="props">
            <p :class="props.row.status === 0 ? 'grayyuan': props.row.status === 1 ? 'greenyuan': 'redyuan'"></p>
            {{ props.row.status == 0&& props.row.is_lock == 0 ? '待审核': props.row.status == 1 && props.row.is_lock == 0 ? '已通过': props.row.status == 2 && props.row.is_lock == 0 ? '未通过': props.row.is_lock == 1 ? '已锁定':''}}
          </template>
        </el-table-column>
        <el-table-column label="操作" align="center" min-width="120">
          <template slot-scope="scope">
            <div class="table-button-box">
              <div class="x-flex-center x-flex-wrap">
                <el-button @click="handleTeam('check',scope.row)" v-if="scope.row.status==0" type="text" size="small">审&emsp;核</el-button>
                <el-button @click="handleTeam('check',scope.row)" v-if="scope.row.status==1 || scope.row.status==2" type="text" size="small">已审核</el-button>
                <el-button @click="handleEdit(scope.row)" type="text" size="small">查&emsp;看</el-button>
                <el-button @click="handleLog(scope.row)" type="text" size="small">日&emsp;志</el-button>
              </div>
              <div class="x-flex-center x-flex-wrap">
                <el-button @click="handleDel(scope.row.uid)" type="text" size="small">删&emsp;除</el-button>
                <el-button v-if="scope.row.is_lock==0" @click="handleTeam('lock',scope.row)" type="text" size="small">锁&emsp;定</el-button>
                <el-button v-else-if="scope.row.is_lock==1" @click="handleTeam('lock',scope.row)" type="text" size="small">解&emsp;锁</el-button>
                <el-button @click="handlePassword(scope.row)" type="text" size="small">密&emsp;码</el-button>
              </div>
            </div>
          </template>
        </el-table-column>
      </el-table>
      <el-pagination class="team-pagination" @size-change="handleSizeChange" @current-change="handleCurrentChange" :current-page="formParams.page" :page-sizes="[10, 30, 50, 100]" :page-size="formParams.limit" layout="total, sizes, prev, pager, next, jumper" :total="total"></el-pagination>
    </div>
    <Log :dialogTableVisible="visible" @handleClose="visible=false" :uid="uid"></Log>
    <confirmDialog :dialogTableVisible="dialogTableVisible" @submit="submit" :objRow="objRow" :dialogType="dialogType" @handleClose="dialogTableVisible=false;uid='';dialogType=''"></confirmDialog>
    <!-- :status="status" -->
  </div>
</template>

<script>
import Log from './company/companyLog'
import confirmDialog from './common/confirmDialog'

import { getCompanyList, companyLog, resetPassword, companyLock, companyCheck, companyDel, getCompanyInfo } from '../api/company'
export default {
  components: {
    Log,
    confirmDialog
  },
  data () {
    return {
      // lockStatus: false,
      // lockStatusNum: 0,
      uid: '',
      multipleSelection: [],
      delData: {},
      checkData: {},
      show: true,
      visible: false,
      dialogTableVisible: false,
      tableData: [],
      userType: 1,
      keyword: '',
      type: '',
      value: 'com_name',
      formParams: {
        limit: 10,
        page: 1,
        status: 0,
        reg_date: 0,
        login_data: 0
      },
      total: 0,
      lockData: {},
      formMember: {},
      timeList: [
        { label: '全部', value: 0 },
        { label: '今天', value: 1 },
        { label: '最近三天', value: 2 },
        { label: '最近七天', value: 3 },
        { label: '最近半月', value: 4 },
        { label: '最近一个月', value: 5 }
      ],
      statusList: [
        { label: '全部', value: 0 },
        { label: '待审核', value: 1 },
        { label: '已通过', value: 2 },
        { label: '未通过', value: 3 },
      ],
      logList: [],
      teamType: [
        { label: '个人', value: 2 },
        { label: '团队', value: 1 }
      ],
      dialogType: '',
      objRow: {}
    }
  },
  created () {
    // 初始化查询标签数据
    console.log(this.formParams, 'this.formParams')
    this.getList(this.formParams)
  },
  watch: {
    keyword (v) {
      console.log(v, 1234)
      if (v) {
        this.formParams['reg_date'] = 0
        this.formParams['status'] = 0
        this.formParams['login_data'] = 0
      }
    }
  },
  methods: {
    changeInput (e) {
      console.log(this.value, 'this.value')
      console.log(e, 'e')
      if (this.value == 'com_name') {
        this.formParams['telphone'] = ''
        this.formParams['id'] = ''
      } else if (this.value == 'telphone') {
        this.formParams['id'] = ''
        this.formParams['com_name'] = ''
      } else {
        this.formParams['com_name'] = ''
        this.formParams['telphone'] = ''
      }
      this.formParams[this.value] = e
    },
    // 多选
    handleSelectionChange (val) {
      this.multipleSelection = val;
      this.uid = val.map(item => item.uid).join(',')
      console.log(this.uid, 'this.uid')
    },

    handleSizeChange (val) {
      this.formParams.limit = val
      this.getList(this.formParams)
    },
    handleCurrentChange (val) {
      this.formParams.page = val
      this.getList(this.formParams)
    },
    selectQuery (key, item) {
      if (key == 'login_data') {
        this.formParams['reg_date'] = 0
        this.formParams['status'] = 0
      } else if (key == 'reg_date') {
        this.formParams['status'] = 0
        this.formParams['login_data'] = 0
      } else {
        this.formParams['reg_date'] = 0
        this.formParams['login_data'] = 0
      }
      this.keyword = '';
      this.formParams[this.value] = '';
      this.formParams[key] = item.value
      this.getList(this.formParams)
    },
    handleTeam (type, val) {
      console.log(val, 'val')
      if (!this.uid && !val) {
        return this.$message.warning('请选择团队')
      }
      else {
        if (!type) {
          this.handleDel(this.uid)
        }
        else {
          this.uid = val.uid || val
          this.dialogType = type
          this.dialogTableVisible = true
          if (this.dialogType == 'check') {
            // this.objRow = val
            this.objRow.status = Number(val.status) + 1
            console.log(this.objRow.status)
          } else {
            this.objRow = val
          }

        }
      }
    },
    submit (val) {
      this.checkData = {}
      this.lockData = {}
      console.log(val, 'val')
      // this.lockStatusNum = val.status
      // console.log(this.checkData,'this.checkData')
      this.dialogTableVisible = false
      if (this.dialogType == 'check') {
        if (val.status == 3) {
          if (!val.reason) {
            return this.$message.warning('请输入不通过原因')
          }
        }
        let params = Object.assign(val, { uid: this.uid })
        this.checkData = {
          uids: this.uid,
          status: val.status - 1,
          reason: val.reason
        }
        this.submitCheck(this.checkData)
      } else {
        this.lockData = {
          uids: this.uid,
          status: val.status,
          reason: val.reason
        }
        this.submitLock(this.lockData)
      }
    },
    getList (params) {
      getCompanyList(params).then(res => {
        const { data } = res
        this.tableData = data.data
        this.total = data.count
      }).catch(error => {
        if (error.status) {
          this.$message.error(error.status.remind)
        }
      })
    },
    handleEdit (val) {
      this.$router.push({ path: 'companyInfo', query: { uid: val.uid } })
    },
    submitCheck (val) {
      companyCheck(val).then(res => {
        this.dialogType = ''
        this.objRow = {}
        this.getList(this.formParams)
        // this.uid = ''
      }).catch((error) => {
        this.$message.error(error.status.remind)
      })
    },
    submitLock (val) {
      companyLock(val).then(res => {
        this.dialogType = ''
        this.objRow = {}
        this.getList(this.formParams)
        this.uid = ''
        // this.lockStatus = res.data.status
      }).catch((error) => {
        this.$message.error(error.status.remind)
      })
    },
    handleLock (val) {
      this.visibleLock = true
      this.uid = val.uid
    },
    handleDel (uids) {
      this.delData.uids = this.uid
      console.log(77777)
      this.$confirm('你确定要删除吗?', '', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        companyDel({ uids }).then(res => {
          this.getList(this.formParams)
        })
      }).catch(() => {
        console.log(2)
      })
      this.delData = {}
    },
    handlePassword (val) {
      this.$alert('密码将设置为123456<br>确定重置吗?', '密码重置', {
        dangerouslyUseHTMLString: true,
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        let uid = val.uid
        resetPassword({ uid }).then(res => {
          this.$message.success('重置成功')
          this.getList(this.formParams)
        })
      }).catch(() => {
        console.log(2)
      })
    },
    handleLog (row) {
      this.uid = row.uid
      this.visible = true
    }
  }
}
</script>
<style lang="scss">
@import "../assets/css/table-list";
.tables-box .table-button-box {
  .el-button--small {
    padding: 3px 0 !important;
  }
}
.grayyuan {
  display: inline-block;
  width: 5px;
  height: 5px;
  border-radius: 50%;
  background-color: grey;
}
.greenyuan {
  display: inline-block;
  width: 5px;
  height: 5px;
  border-radius: 50%;
  background-color: green;
}
.redyuan {
  display: inline-block;
  width: 5px;
  height: 5px;
  border-radius: 50%;
  background-color: red;
}
img {
  margin: 0 5px;
}
.x-flex-between {
  display: flex;
  flex-wrap: wrap;
  justify-content: space-between;
  justify-items: space-between;
}
</style>