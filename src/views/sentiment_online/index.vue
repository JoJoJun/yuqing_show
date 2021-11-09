<template>
  <div class="app-container">
    <el-form ref="form" :model="form" label-width="80px">
      <el-form-item label="输入框">
        <el-input v-model="form.name" type="textarea" autosize />
      </el-form-item>
      <el-row :gutter="12">
        <el-col :span="12" align="left">
          <el-form-item label="Aspect" align="left">
            <el-select v-model="form.aspect" placeholder="选择方面">
              <el-option label="口味" value="口味" />
              <el-option label="服务" value="服务" />
              <el-option label="环境" value="环境" />
            </el-select>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item>
            <el-button type="primary" @click="onPredict">Predict</el-button>
          </el-form-item>
        </el-col>
      </el-row>
    </el-form>
    <el-divider />
    <el-form ref="form_res" :model="form_res" label-width="80px">
      <el-row :gutter="12">
        <el-col :span="8">
          <el-form-item label="结果">
            <el-input v-model="form_res.res" :disabled="true" size="medium" />
          </el-form-item>
        </el-col>
        <el-col :span="4">
          <el-select v-model="form_res.feedback" placeholder="分析是否正确">
            <el-option label="正确" value="yes" />
            <el-option label="错误" value="no" />
          </el-select>
        </el-col>
        <el-col :span="12" align="left" style="float: right;">
          <el-form-item>
            <el-button type="primary" @click="onSubmit">反馈</el-button>
          </el-form-item>
        </el-col>
      </el-row>
    </el-form>
    <el-divider />
    <el-row type="flex" justify="center">
      <el-col :span="20">
        <el-table
          :data="tableData"
          style="width: 80%"
          height="550"
          :row-class-name="tableRowClassName"
        >
          <el-table-column
            fixed
            prop="version"
            label="模型版本"
            width="150"
          />
          <el-table-column
            prop="end_date"
            label="结束训练日期"
            width="200"
          />
          <el-table-column
            prop="state"
            label="状态"
            width="200"
          />
          <el-table-column
            prop="pretraining_size"
            label="预训练规模"
            width="200"
          />
        </el-table>
      </el-col>
    </el-row>
  </div>
</template>

<script>
export default {
  name: 'OnlineAna',
  data() {
    return {
      form: {
        name: '我喜欢这家店的环境，下次还会来！',
        aspect: ''
      },
      form_res: {
        res: '正向',
        feedback: ''
      },
      tableData: [{
        version: 'st_1634813888',
        start_date: '20211018',
        end_date: '20211021',
        pretraining_size: '1000000',
        state: '完成'
      },
      { version: 'st_1634812801',
        start_date: '20211015',
        end_date: '20211017',
        pretraining_size: '260000',
        state: '完成' },
      { version: 'st_1634811000',
        start_date: '20211010',
        end_date: '20211013',
        pretraining_size: '980000',
        state: '完成' }]
    }
  },
  mounted() {
    this.requestTable()
  },
  methods: {
    get_Predict() {
      console.log('input = ', this.form.name, ' aspect=', this.form.aspect)
      this.axios.post('/model/get_prediction', {
        input: this.form.name,
        aspect: this.form.aspect
      }, { emulateJSON: true }).then(
        res => {
          console.log('in aspect_day_nums,status = ', res.data.status)
          var status = res.data.status
          if (status == 0) {
            this.form_res.res = res.data.ans
            this.$message({
              message: '调用成功!',
              type: 'success'
            })
          }
        }
      ).catch(res => {
        console.log(res.data.status)
        console.log(res.data.msg)
      })
    },
    onPredict() {

      this.get_Predict()

    },
    put_feedBack() {
      console.log('input = ', this.form.name, ' aspect=', this.form.aspect)
      this.axios.post('/model/model_feedback', {
        sent: this.form.name,
        aspect: this.form.aspect,
        label: this.form_res.res,
        feed_back: this.form_res.feedback
      }, { emulateJSON: true }).then(
        res => {
          // console.log('in aspect_day_nums,status = ',res.data.status)
          var status = res.data.status
          if (status == 0) {
            // this.form_res.res = res.data.ans
            this.$message({
              message: '反馈成功!',
              type: 'success'
            })
          }
        }
      ).catch(res => {
        console.log(res.data.status)
        console.log(res.data.msg)
      })
    },
    requestTable() {
      this.axios.get('/model/model_info').then(
        res => {
          console.log(res.data.status)
          var status = res.data.status
          if (status == 0) {
            this.tableData = res.data.table_data
          }
        }
      ).catch(res => {
        console.log(res.data.status)
        console.log(res.data.msg)
      })
    },
    onSubmit() {
      this.put_feedBack()
      // this.$message({
      //   message: '反馈成功!',
      //   type: 'success'
      // })
    },
    onCancel() {
      this.$message({
        message: 'cancel!',
        type: 'warning'
      })
    }
  }
}
</script>

<style scoped>
.line{
  text-align: center;
}
</style>
