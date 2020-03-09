<template>
  <div class="tables-box team-list-box">
    <div class="team-list">
      <el-tabs v-model="activeName" @tab-click="handleClick">
        <el-tab-pane label="全部团队" name="all">
          <teamTable :tableType="activeName" v-if="activeName=='all'"></teamTable>
        </el-tab-pane>
        <el-tab-pane label="成员账户" name="personal">
          <teamTable :tableType="activeName" v-if="activeName=='personal'"></teamTable>
        </el-tab-pane>
      </el-tabs>
    </div>
  </div>
</template>
<script>
import teamTable from './teamMessage/teamTable'
export default {
  components: {
    teamTable
  },
  data() {
    return {
      activeName: 'all',
    }
  },
  created() {

  },
  methods: {
    handleClick(tab, event) {
      console.log(tab, event);
    },
    // submitLock (val) {
    //   companyLock(val).then(res => {
    //     this.getList(this.formParams)
    //     this.uid = ''
    //     // this.lockStatus = res.data.status
    //   }).catch((error) => {
    //     this.$message.error(error.status.remind)
    //   })
    // },
    // handleDel () {
    //   this.getList(this.formParams)

    // }
  },
  // watch: {
  //   tableType (val) {
  //     if (val == 'all') {
  //       this.getList(this.formParams)
  //     }
  //     else {
  //       this.getUserList(this.formParams)
  //     }
  //   }
  // },
}

</script>

<style lang="scss">
@import "../assets/css/table-list";
</style>
