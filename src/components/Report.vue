<template>
  <div class="tables-box">
    <div class="table-list team-forum">
      <el-form :inline="true" :model="formParams" ref="formParams" class="demo-form-inline" label-width="100px" :label-position="labelPosition">
        <el-form-item label="反馈账号：">
          <el-input
            class="query-inputs width350"
            v-model="formParams.teamName"
            @change="changeInput"
            placeholder="请输入反馈账号"
          ></el-input>
        </el-form-item>
        <el-form-item label="状态：">
          <el-radio-group v-model="radio1">
            <el-radio-button label="">全部</el-radio-button>
            <el-radio-button label="1">待审核</el-radio-button>
            <el-radio-button label="2">已处理</el-radio-button>
          </el-radio-group>
        </el-form-item>
        <div></div>
        <el-form-item label="处理人：">
          <el-input
            class="query-inputs width350"
            v-model="formParams.name"
            @change="changeInput"
            placeholder="请输入处理人"
          ></el-input>
        </el-form-item>
          <el-form-item label="选择日期：">
            <el-date-picker
              v-model="DatePicker"
              type="daterange"
              range-separator="至"
              start-placeholder="开始日期"
              end-placeholder="结束日期"
              @change="dataChanged"
              >
            </el-date-picker>
          </el-form-item>
          <div></div>
        <el-button type="primary" @click="searCh" class="select-btn">查询</el-button>
        <el-button type="primary" @click="reSet(formParams)">重置</el-button>
        <div class="slot_row"></div>
      </el-form>
        <el-table
        :data="tableData"
        tooltip-effect="dark"
        style="width: 100%"
        @selection-change="handleSelectionChange">
        <el-table-column
          type="selection"
          width="55">
        </el-table-column>
        <el-table-column
          prop="name"
          label="反馈账号"
          width="130"
          align="center"
          >
        </el-table-column>
        <el-table-column
          prop="mobile"
          label="手机号码"
          width="130"
          align="center"
          >
        </el-table-column>
        <el-table-column
          prop="desired_position"
          label="反馈说明"
          width="180"
          align="center"
          >
        </el-table-column>
        <el-table-column
          prop="money"
          label="反馈时间"
          width="180"
          align="center"
          >
        </el-table-column>
        <el-table-column
          prop="city"
          label="处理人"
          width="120"
          align="center"
          >
        </el-table-column>
        <el-table-column
          label="状态"
          width="130"
          align="center"
          >
          <template slot-scope="scope">
            <span :class="deal ? 'green' : 'grey'">{{deal ? '已处理' : '未处理'}}</span>
          </template> 
        </el-table-column>
        <el-table-column
          label="操作"
          show-overflow-tooltip>
          <template slot-scope="scope">
            <el-button @click="dialogVisible = true" type="text" size="small">查看</el-button>
            <el-button @click="handleStatus(scope.row,scope.$index)" type="text" size="small" :disabled="disabled && activeIndex==scope.$index">处理</el-button>
          </template>
        </el-table-column>
      </el-table>
      <el-dialog
        title="反馈详情"
        :visible.sync="dialogVisible"
        width="40%"
        >
        <ul>
          <li class="marginb"><span>举报账号：</span><span>{{reportlist.user}}</span></li>
          <li class="marginb"><span>手机号码：</span><span>{{reportlist.phone}}</span></li>
          <li class="marginb"><span>举报原因：</span><span>{{reportlist.reason}}</span></li>
          <li class="marginb"><span>举报日期：</span><span>{{$moment.unix(reportlist.date).format('YYYY-MM-DD HH:mm')}}</span></li>
          <li class="marginb"><span>处理人：</span><span>{{reportlist.deal_per}}</span></li>
          <li class="marginb">
            <span>处理结果：</span>
            <!-- <span>{{reportlist.deal_result}}</span> -->
            <el-input type="textarea" v-model="reportlist.deal_result"></el-input>
          </li>
        </ul>
        <span slot="footer" class="dialog-footer">
          <el-button @click="dialogVisible = false">取 消</el-button>
          <el-button type="primary" @click="dialogVisible = false">确 定</el-button>
        </span>
      </el-dialog>
      <el-dialog
        title="处理结果"
        :visible.sync="dialogVisibleDeal"
        width="40%"
        >
          <el-input type="textarea" v-model="reportlist.deal_result"></el-input>
        </el-form>
         <span slot="footer" class="dialog-footer">
          <el-button @click="dialogVisibleDeal = false">取 消</el-button>
          <el-button type="primary" @click="dealSure">确 定</el-button>
        </span>
      </el-dialog>
      <el-pagination
        class="team-pagination"
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
        :current-page="formParams.page"
        :page-sizes="[10, 30, 50, 100]"
        :page-size="formParams.limit"
        layout="total, sizes, prev, pager, next, jumper"
        :total="total"
      ></el-pagination>
    </div>
    <!-- <followUpRecord :dialogTableVisible="dialogTableVisible" :trackList="trackLists" @handleClose="handleClose" @submitRecord="submitRecord"></followUpRecord> -->
  </div>
</template>
<script>
  import { feedbackList, gainFeedbackInfo, doFeedbackInfo } from '../api/report'
  import cvLook  from './cvLook'
  import followUpRecord from './followUpRecord'
  export default {
    // components: {
    //   cvLook,
    //   followUpRecord
    // },
    data () {
      return {
        dialogVisible: false,
        dialogVisibleDeal: false,
        deal: false,
        tableData: [{
          id: 0,
          date: '2016-05-02',
          name: '王小虎',
          address: '上海市普陀区金沙江路 1518 弄',

        }, {
          id: 1,
          date: '2016-05-04',
          name: 'da虎',
          address: '上海市普陀区金沙江路 1518 弄',

        }, {
          id: 2,
          date: '2016-05-01',
          name: '王3虎',
          address: '上海市普陀区金沙江路 1518 弄',

        }],
        dialogTableVisible: false,
        dialogFormVisible: false,
        reason: '',
        reportlist:{
          user: 'alfie',
          phone: 15765692066,
          date: 1576569206,
          reason: "违规",
          deal_per: "ren",
          deal_result: 'wqefgergberiogh7r8bgvr',
        },
        radio1: '',
        show: false,
        resumeId: '',
        formLabelWidth: '120px',
        names: '',
        phones: '',
        namess: '',
        datess: '',
        labelPosition: 'right',
        show: false,
        visible: false,
        userType: 1,
        keyword: '',
        team_name: '',
        name: '',
        mobile: '',
        type: '',
        id: '',
        formParams: {
          uid: localStorage.getItem('sys_uid'),
          // teamName: '',
          // name: '',
          // beginTime: '',
          // endTime: '',
          // phone: '',
          limit: 10,
          page: 1,
        },
        value1:'',
        DatePicker:[],
        total: 0,
        formMember: {},
        commentInfo: {},
        id: '',
        exportC: {
          uid: localStorage.getItem('sys_uid'),
        },
        
        trackLists: [],
        multipleSelection: [],
        activeIndex:-1,
        disabled:false
      }
    },
    created () {
      this.getList(this.formParams);
      // console.log(asdf,'asdf')
      // console.log(teamAdministrationResume,'teamAdministrationResume')
    },
    methods: {
      dealSure () {
        this.disabled = true;
        this.dialogVisibleDeal = false;
      },
      handleStatus(row,index){
        console.log(index)
        this.dialogVisibleDeal = true
        this.activeIndex = index
      },
      submitRecord (w) {
        console.log(w,'用户input')
        this.dialogTableVisible = false
      },
      openWindow (q) {
        this.dialogTableVisible = true
        console.log(q,2222222222)
        this.trackLists = q
      },
      changeInput (e) {
        this.formParams[this.type] = e
      },
      searCh () {
        // let d = new Date().valueOf()
        // console.log(d,222)
        // console.log(this.DatePicker[0].valueOf(),3333)
        this.getList(this.formParams)
      },
      handleSizeChange (val) {
        console.log(val,'val')
        this.formParams.limit = val
        this.getList(this.formParams)
      },
      handleCurrentChange (val) {
        this.formParams.page = val
        this.getList(this.formParams)
      },
      selectSort (val) {
        this.formParams.type = val.value
      },
      refurbish () {
        this.getList(this.formParams)
      },
      handleClick () {
        console.log(1)
        // this.$router.push({path:'./cvLook'})
      },
      //关闭弹窗
      handleClose () {
        this.dialogTableVisible = false
      },
      getList (formParams) {
        feedbackList(formParams).then(res => {
          if (res.data.data) {
            this.tableData = res.data.data || [];
            this.total = res.data.count
          }
          else {
            this.commentInfo = null
          }
        })
        .catch(error => {
          this.$message.error(error.status.remind)
        })
      },
      getSortType (val) {
        let obj = this.commentSort.find(item => {
          return val == item.value
        })
        if (obj) {
          return obj.label
        }
      },
      handleDetail (val) {
        let arr = JSON.parse(sessionStorage.getItem('menus'))
        arr.push('论坛详情')
        sessionStorage.setItem('menus', JSON.stringify(arr))
        this.$router.push({ path: 'detailCard', query: { id: val.id } })
      },
      handleDel (val) {
        this.$confirm('确定要删除吗?', '', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          let uid = val.id
          deleteUser({ uid }).then(res => {
            this.getList(this.formParams)
          })
        }).catch(() => {
          console.log(2)
        })
      },
      toggleSelection (rows) {
        if (rows) {
          rows.forEach(row => {
            this.$refs.multipleTable.toggleRowSelection(row);
          });
        } else {
          this.$refs.multipleTable.clearSelection();
        }
      },
      handleSelectionChange(val) {
        this.multipleSelection = val;
      },
      dataChanged () {
        if(this.DatePicker.length > 0){
          this.formParams.beginTime = this.DatePicker[0].valueOf();
          this.formParams.endTime = this.DatePicker[1].valueOf();
        }
      },
      reSet (formParams) {
        this.DatePicker = [];
        this.formParams.teamName = '';
        this.formParams.phone = '';
        this.formParams.name = '';
      },
      // getSortType (val) {
      //   // let obj = this.trackLists.find(item => {
      //   //   return val == item.
      //   // })
      //   // if (obj) {
      //     // return val.remark
      //   }
      // },
    },
  }
</script>
<!-- 
<style lang="scss">
</style> -->
<style lang="scss">
  @import '../assets/css/table-list';
  .slot_row{width: 100%;height: 20px;}
  .tables-box .query-inputs {
    margin: 0;
    width: 350px;
  }
  .green{color: green;}
  .grey{color: grey;}
  .marginb{
    margin-bottom: 30px;
    
    }

</style>
  