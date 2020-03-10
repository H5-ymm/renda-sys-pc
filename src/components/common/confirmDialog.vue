<template>
  <el-dialog :title="title" width="400px" :visible.sync="dialogTableVisible" :before-close="handleClose">
    <el-form :model="form" class="lock-form">
      <el-form-item :label="`${title}结果`">
        <el-radio-group v-model="form.status">
          <el-radio :label="item.value" v-for="(item, index) in labelList" :key="index" @change="choose_change">{{item.label}}</el-radio>
        </el-radio-group>
      </el-form-item>
      <el-form-item :label="`${title}说明`">
        <el-input type="textarea" :autosize="{ minRows: 3, maxRows: 2}" v-model="form.reason"></el-input>
      </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="handleClose">取消</el-button>
      <el-button type="primary" @click="submit">确定</el-button>
    </span>
  </el-dialog>
</template>

<script>
export default {
  props: ['dialogTableVisible', 'dialogType', 'objRow', 'infoObj'],
  data () {
    return {
      form: {
        status: 0,
        reason: ''
      }
    }
  },
  created () {
    console.log(this.dialogType === "check")
    console.log(this.dialogType)
    if (this.dialogType === "check") {
      this.form.status = this.objRow.status ? this.objRow.status : 0
    } else {
      //  this.form.status === null ? this.form.status = this.objRow.is_lock : this.form.status;
    }
  },
  watch: {
    dialogType (val) {
      console.log(val)
      if (this.dialogType === "check") {
        this.form.status = this.objRow.status ? this.objRow.status : 0
      } else {
        this.form.status = this.objRow.is_lock ? this.objRow.is_lock : 0
      }
    }
  },
  computed: {
    title () {
      return this.dialogType == 'check' ? '审核' : '锁定'
    },
    labelList () {
      let arr = []
      if (this.dialogType == 'check') {
        arr = [{
          label: '通过', value: 2
        }, {
          label: '不通过', value: 3
        }]
      }
      else {
        arr = [{
          label: '解锁', value: 0
        }, {
          label: '锁定', value: 1
        }]
      }
      console.log(arr)
      return arr
    },

  },

  methods: {
    handleClose () {
      this.form.status = this.objRow.status
      this.$emit('handleClose')
    },
    submit () {
      console.log(this.form, 'this.form')
      this.$emit('submit', this.form)
      this.form = {
        status: 0,
        reason: ''
      }
    },
    choose_change (v) {
      console.log(v, 222)
      this.form.status = v
    }
  }
}
</script>

<style lang="scss">
.lock-form {
  .el-radio-group {
    width: 100%;
  }
}
</style>

