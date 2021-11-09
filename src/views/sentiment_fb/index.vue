<template>
  <div class="app-container">
  <el-table
      :data="tableData"
      height="450"
      border
      style="width: 100%">
    <el-table-column
        prop="date"
        label="日期"
        width="180">
    </el-table-column>
    <el-table-column
        prop="sent"
        label="句子"
        >
    </el-table-column>
    <el-table-column
        prop="aspect"
        label="aspect">
    </el-table-column>
    <el-table-column
        prop="label"
        label="分类结果">
    </el-table-column>
    <el-table-column
        label="操作"
        >
      <template slot-scope="scope">
        <el-button @click="handleClick(scope.row)" type="text" size="small">反馈</el-button>
      </template>
    </el-table-column>
  </el-table>

  <el-dialog title="标注" :visible.sync="dialogFormVisible">
  <el-form ref="form_res" :model="form_res" :rules="rules" label-width="80px">
    <el-row :gutter="12">
      <el-col :span="24">
      <el-form-item label="句子">
        <el-input type="textarea" autosize v-model="form_res.sent" :disabled="true" size="medium" />
      </el-form-item>
      </el-col>

    </el-row>
    <el-row :gutter="12">
      <el-col :span="8">
        <el-form-item label="aspect">
          <el-input v-model="form_res.asp" :disabled="true" size="medium" />
        </el-form-item>
      </el-col>
      <el-col :span="8">
        <el-form-item label="结果">
          <el-input v-model="form_res.res" :disabled="true" size="medium" />
        </el-form-item>
      </el-col>
      <el-col :span="8">
        <el-form-item label="结果" prop="feedback" >
        <el-select v-model="form_res.feedback" label="反馈" placeholder="请选择">
          <el-option label="正确" value="正确" />
          <el-option label="错误" value="错误" />

        </el-select>
        </el-form-item>
      </el-col>
<!--      <el-col :span="12" align="left" style="float: right;">-->
<!--        <el-form-item>-->
<!--          <el-button type="primary" @click="onSubmit">反馈</el-button>-->
<!--        </el-form-item>-->
<!--      </el-col>-->
    </el-row>

  </el-form>
    <div slot="footer" class="dialog-footer">
      <el-button @click="dialogFormVisible = false">取 消</el-button>
      <el-button type="primary" @click="onSubmit('form_res')">确 定</el-button>
    </div>
  </el-dialog>
  <el-divider></el-divider>
    <el-table
        :data="modelData"
        style="width: 100%"
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
<!--  <el-row>-->
<!--    <el-upload-->
<!--        class="upload-demo"-->
<!--        action="https://jsonplaceholder.typicode.com/posts/"-->
<!--        :on-preview="handlePreview"-->
<!--        :on-remove="handleRemove"-->
<!--        :before-remove="beforeRemove"-->
<!--        multiple-->
<!--        :limit="1"-->
<!--        :on-exceed="handleExceed"-->
<!--        :file-list="fileList">-->
<!--      <el-button size="small" type="primary">点击上传</el-button>-->
<!--      <div slot="tip" class="el-upload__tip">只能上传csv文件</div>-->
<!--    </el-upload>-->
<!--  </el-row>-->
  </div>
</template>

<script>
export default {
  name: "sentiment_fb",
  data() {
    return {
      form_res: {
        id: '',
        sent: '',
        asp: '',
        res: '',
        feedback: ''
      },
      rules: {
        feedback: [
          { required: true, message: '请选择反馈结果', trigger: 'change' }
          // {min: 3, max: 5, message: '长度在 3 到 5 个字符', trigger: 'blur'}
        ],
      },
      fileList: [{name:'file1.csv',url:''}],
      dialogFormVisible: false,
      tableData: [{
        date: '2016-05-03',
        aspect: '王小虎',
        sent: '上海市普陀区金沙江路 1518 弄',
        label: '正面'
      }, {
        date: '2016-05-03',
        aspect: '王小虎',
        sent: '上海市普陀区金沙江路 1518 弄',
        label: '正面'
      } ],
      modelData: [{
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
    this.requestSentall()
    this.requestModelTable()
  },
  methods: {
    tableRowClassName({row, rowIndex}) {
      if (rowIndex%2 === 0) {
        return 'warning-row';
      } else  {
        return 'success-row';
      }
      return '';
    },
    requestSentall() {
      this.axios.get('/senti/get_asp_result_all').then(
          res => {
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
    requestModelTable() {
      this.axios.get('/model/model_info').then(
          res => {
            console.log(res.data.status)
            var status = res.data.status
            if (status == 0) {
              this.modelData = res.data.table_data
            }
          }
      ).catch(res => {
        console.log(res.data.status)
        console.log(res.data.msg)
      })
    },
    handleClick(row) {
      // console.log("row=",row)
      // console.log(row['sent'])
      // this.form.sent = row['sent']
      // console.log('sent id=',row['id'])
      this.form_res.sent = row['sent']
      this.form_res.asp = row['aspect']
      this.form_res.res = row['label']
      this.form_res.id = row['id']
      this.dialogFormVisible = true
      // this.tableData.splice(index, 1);
    },
    put_feedBack() {
      console.log('input = ', this.form_res.sent, ' aspect=', this.form_res.asp,' res=',this.form_res.res,
      'feedback = ', this.form_res.feedback)
      this.axios.post('/model/model_feedback', {
        sent: this.form_res.sent,
        aspect: this.form_res.asp,
        label: this.form_res.res,
        feed_back: this.form_res.feedback,
        id: this.form_res.id
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
    onSubmit(formName) {
      this.$refs[formName].validate((valid) => {
        if (valid) {
          // alert('submit!');
          this.put_feedBack()
          this.dialogFormVisible = false
          console.log('check valid')
          // console.log("sent = ",this.form.sent," aspect= ",this.form.asp, " label = ",this.form.senti)
          // this.add_new_sent()
        } else {
          console.log('error submit!!');
          return false;
        }
      });
    }
  },
}
</script>

<style scoped>

</style>