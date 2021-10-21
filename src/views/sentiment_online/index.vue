<template>
   <div class="app-container">
    <el-form ref="form" :model="form" label-width="80px">
      <el-form-item label="输入框">
        <el-input v-model="form.name" type="textarea" autosize/>
      </el-form-item>
      <el-row :gutter="12">
        <el-col :span="12" align="left">
          <el-form-item label="Aspect" align="left">
        <el-select v-model="form.aspect" placeholder="选择方面">
          <el-option label="口味" value="food" />
          <el-option label="服务" value="service" />
          <el-option label="环境" value="env" />
        </el-select>
      </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item>
        <el-button type="primary" @click="onSubmit">Predict</el-button>
      </el-form-item>
        </el-col>
      </el-row>
    </el-form>
     <el-divider/>
     <el-form ref="form_res" :model="form_res" label-width="80px"  >
       <el-row :gutter="12" >
         <el-col :span="8">
      <el-form-item label="结果">
        <el-input v-model="form_res.res"  :disabled="true" size="medium"/>
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
     <el-divider/>
     <el-row  type="flex"  justify="center" >
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
        prop="start_date"
        label="开始训练日期"
        width="200"
      />
       <el-table-column
        prop="end_date"
        label="结束训练日期"
        width="200"
      />
       <el-table-column
        prop="pretraining_size"
        label="预训练数据规模"
        width="200"
      />
       <el-table-column
        prop="state"
        label="状态"
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
        aspect: 'env',
      },
      form_res: {
        res: '正向',
      },
      tableData: [{
        version: 'st_1634813888',
        start_date: '20211018',
        end_date: '20211021',
        pretraining_size: '1000000',
        state: '完成'
      },
        {version: 'st_1634812801',
        start_date: '20211015',
        end_date: '20211017',
        pretraining_size: '260000',
        state: '完成'},
        {version: 'st_1634811000',
        start_date: '20211010',
        end_date: '20211013',
        pretraining_size: '980000',
        state: '完成'},]
    }
  },
  methods: {
    onSubmit() {
      this.$message({
        message: '反馈成功!',
        type: 'success'
      })
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
