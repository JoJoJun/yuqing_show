<template>
  <div class="app-container">
    <el-row :gutter="8" class="el-row">
      <el-col :span="8" align="right">
        <el-tag v-for="tag in tags" :key="tag.name" :type="tag.type" style="margin-left: 10px;">{{ tag.name }}</el-tag>
      </el-col>
      <el-col :span="4"><el-button type="primary" icon="el-icon-refresh" @click="reFresh">刷新</el-button></el-col>
    </el-row>
    <el-divider />
    <el-row :gutter="12" class="el-row">
      <el-col :span="8" align="right">
        <el-tag
          v-for="tag in dynamicTags"
          :key="tag"
          closable
          :disable-transitions="false"
          @close="handleClose(tag)"
        >
          {{ tag }}
        </el-tag>
        <el-input
          v-if="inputVisible"
          ref="saveTagInput"
          v-model="inputValue"
          class="input-new-tag"
          size="small"
          @keyup.enter.native="handleInputConfirm"
          @blur="handleInputConfirm"
        />
        <el-button v-else class="button-new-tag" size="small" @click="showInput">+ New Tag</el-button>
      </el-col>
      <el-col :span="4"><el-button type="primary" icon="el-icon-circle-plus-outline" @click="add_sentiments">提交</el-button></el-col>
    </el-row>
    <el-divider />
    <el-table :data="tableData" style="width: 100%" height="250">
      <el-table-column fixed prop="date" label="日期" />
      <el-table-column prop="sent" label="文本" />
<!--      <el-table-column prop="state" label="状态" />-->
      <el-table-column label="操作">
        <template slot-scope="scope">
        <el-button type="success" plain size="small" @click="handleClick(scope.$index, scope.row)">标注</el-button>
        </template>
      </el-table-column>
    </el-table>
    <el-dialog title="标注" :visible.sync="dialogFormVisible">
      <el-form :model="form" :rules="rules" ref="form">
<!--        <el-form-item label="sent" :label-width="formLabelWidth">-->
<!--          <el-input v-model="form.sent" :disabled="true" />-->
<!--        </el-form-item>-->
        <el-row :gutter="12">
          <el-col :span="24">
            <el-form-item label="句子">
              <el-input type="textarea" autosize v-model="form.sent" :disabled="true" size="medium" />
            </el-form-item>
          </el-col>

        </el-row>
        <el-form-item label="aspect" :label-width="formLabelWidth">
          <el-input v-model="form.asp" autocomplete="off" placeholder="如有多个请用空格分割" />
        </el-form-item>
        <el-form-item label="情感倾向" :label-width="formLabelWidth"  prop="senti">
          <el-select v-model="form.senti" placeholder="请标注情感倾向"  >
            <el-option label="正面" value="正面" />
            <el-option label="负面" value="负面" />
            <el-option label="中性" value="中性" />
          </el-select>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormVisible = false">取 消</el-button>
        <el-button type="primary" @click="add_sent_button('form')">确 定</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
export default {
  name: 'Edit',
  data() {
    return {
      dialogFormVisible: false,
      cnt: 0,
      tags: [
        { name: '给力', type: '' },
        { name: '奥里给', type: 'success' },
        { name: '飘红', type: 'info' },
        { name: '点赞', type: 'warning' },
        { name: '万里挑一', type: 'danger' }
      ],
      dynamicTags: ['给力', '奥里给'],
      inputVisible: false,
      inputValue: '',
      rules: {
        senti: [
          { required: true, message: '请选择情感倾向', trigger: 'change' }
          // {min: 3, max: 5, message: '长度在 3 到 5 个字符', trigger: 'blur'}
        ],
      },
      tableData: [{
        date: '2020-05-02',
        sent: '吃过4次了！非常喜欢！很入味，够辣\n' +
            '推荐在平底锅里加清水面',
        state: '已标注'
      }, {
        date: '2020-05-04',
        sent: '很好吃 鲈鱼真的很嫩 2个人都吃不完 最好多点人去吃 可以吃些别的菜\n' +
            '煲仔饭也不错 \n' +
            '难的在深圳吃到这么辣的东西 哈哈',
        state: '未标注'
      }
      ],
      form: {
        sent: '',
        asp: '',
        senti: '',

        type: [],
        resource: '',
        desc: ''
      },
      formLabelWidth: '120px'
    }
  },
  mounted() {
    console.log('mounted')
    this.get_Candidates()
    this.requestNewSents()
  },
  methods: {
    get_Candidates() {
      this.axios.post('/senti/get_candi_words', {
        cnt: this.cnt
      }, { emulateJSON: true }).then(
        res => {
          var status = res.data.status
          if (status == 0) {
            this.tags = res.data.candi_list
          }
        }
      ).catch(res => {
        console.log(res.data.status)
        console.log(res.data.msg)
      })
      this.cnt += 1
    },
    reFresh() {
      this.get_Candidates()
    },
    add_sentiments() {
      if(this.dynamicTags.length == 0){
        this.$message({
          message: '不能为空!',
          type: 'error'
        })
      }
      else {
        this.axios.post('/senti/add_senti_words', {
          senti_word: this.dynamicTags
        }, {emulateJSON: true}).then(
            res => {
              var status = res.data.status
              if (status == 0) {
                this.$message({
                  message: '添加成功!',
                  type: 'success'
                })
              }
            }
        ).catch(res => {
          console.log(res.data.status)
          console.log(res.data.msg)
        })
        this.cnt += 1
      }
    },
    requestNewSents() {
      this.axios.get('/senti/get_unlabled').then(
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
    add_sent_button(formName) {
      this.$refs[formName].validate((valid) => {
        if (valid) {
          // alert('submit!');

          this.dialogFormVisible = false
          console.log("sent = ",this.form.sent," aspect= ",this.form.asp, " label = ",this.form.senti)
          this.add_new_sent()
        } else {
          console.log('error submit!!');
          return false;
        }
      });


    },
    add_new_sent() {
      this.axios.post('/senti/add_labeled', {
        sent: this.form.sent,
        aspect: this.form.asp,
        label: this.form.senti
      }, { emulateJSON: true }).then(
        res => {
          var status = res.data.status
          if (status == 0) {
            this.$message({
              message: '标注成功!',
              type: 'success'
            })
            this.requestNewSents()
          }
        }
      ).catch(res => {
        console.log(res.data.status)
        console.log(res.data.msg)
      })
    },
    handleClose(tag) {
      this.dynamicTags.splice(this.dynamicTags.indexOf(tag), 1)
    },

    showInput() {
      this.inputVisible = true
      this.$nextTick(_ => {
        this.$refs.saveTagInput.$refs.input.focus()
      })
    },

    handleInputConfirm() {
      const inputValue = this.inputValue
      if (inputValue) {
        this.dynamicTags.push(inputValue)
      }
      this.inputVisible = false
      this.inputValue = ''
    },
    handleClick(index, row) {
      // console.log("row=",row)
      // console.log(row['sent'])
      this.form.sent = row['sent']
      this.dialogFormVisible = true
      // this.tableData.splice(index, 1);
    }

  }
}
</script>

<style scoped>
.el-row {
    border-radius: 4px;
  display: block;
  }
  .el-tag + .el-tag {
    margin-left: 10px;
  }
  .button-new-tag {
    margin-left: 10px;
    height: 32px;
    line-height: 30px;
    padding-top: 0;
    padding-bottom: 0;
  }
  .input-new-tag {
    width: 90px;
    margin-left: 10px;
    vertical-align: bottom;
  }
</style>
