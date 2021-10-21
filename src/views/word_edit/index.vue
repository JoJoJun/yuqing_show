<template>
    <div class="app-container">
      <el-row :gutter="8" class="el-row">
        <el-col :span="8" align="right">
      <el-tag v-for="tag in tags" :key="tag.name"  :type="tag.type" style="margin-left: 10px;">{{tag.name}}</el-tag>
      </el-col>
        <el-col :span="4"><el-button type="primary" icon="el-icon-refresh" >刷新</el-button></el-col>
      </el-row>
      <el-divider/>
      <el-row :gutter="12" class="el-row">
        <el-col :span="8" align="right">
        <el-tag
  :key="tag"
  v-for="tag in dynamicTags"
  closable
  :disable-transitions="false"
  @close="handleClose(tag)">
  {{tag}}
</el-tag>
        <el-input
  class="input-new-tag"
  v-if="inputVisible"
  v-model="inputValue"
  ref="saveTagInput"
  size="small"
  @keyup.enter.native="handleInputConfirm"
  @blur="handleInputConfirm"
>
</el-input>
<el-button v-else class="button-new-tag" size="small" @click="showInput">+ New Tag</el-button>
      </el-col>
        <el-col :span="4"><el-button type="primary" icon="el-icon-circle-plus-outline" >提交</el-button></el-col>
      </el-row>
      <el-divider/>
      <el-table :data="tableData">
        <el-table-column prop="date" label="日期"></el-table-column>
        <el-table-column prop="name" label="文本"></el-table-column>
        <el-table-column prop="state" label="状态"></el-table-column>
        <el-table-column label="操作">
          <el-button type="text" @click="handleClick(row)" size="small">标注</el-button>
        </el-table-column>
      </el-table>
      <el-dialog title="标注" :visible.sync="dialogFormVisible">
  <el-form :model="form">
    <el-form-item label="aspect" :label-width="formLabelWidth" >
      <el-input v-model="form.name" autocomplete="off" placeholder="如有多个请用空格分割" ></el-input>
    </el-form-item>
    <el-form-item label="情感倾向" :label-width="formLabelWidth">
      <el-select v-model="form.region" placeholder="请标注情感倾向">
        <el-option label="正向" value="shanghai"></el-option>
        <el-option label="负向" value="beijing"></el-option>
      </el-select>
    </el-form-item>
  </el-form>
  <div slot="footer" class="dialog-footer">
    <el-button @click="dialogFormVisible = false">取 消</el-button>
    <el-button type="primary" @click="dialogFormVisible = false">确 定</el-button>
  </div>
</el-dialog>
    </div>
</template>

<script>
    export default {
        name: "Edit",
      data() {
      return {
        dialogFormVisible: false,
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
        tableData: [{
          date: '2020-05-02',
          name: '吃过4次了！非常喜欢！很入味，够辣\n' +
            '推荐在平底锅里加清水面',
          state: '已标注'
        }, {
          date: '2020-05-04',
          name: '很好吃 鲈鱼真的很嫩 2个人都吃不完 最好多点人去吃 可以吃些别的菜\n' +
            '煲仔饭也不错 \n' +
            '难的在深圳吃到这么辣的东西 哈哈',
          state: '未标注'
        },
          {
          date: '2020-05-04',
          name: '很好吃 鲈鱼真的很嫩 2个人都吃不完 最好多点人去吃 可以吃些别的菜\n' +
            '煲仔饭也不错 \n' +
            '难的在深圳吃到这么辣的东西 哈哈',
          state: '未标注'
        },
        {
          date: '2020-05-04',
          name: '很好吃 鲈鱼真的很嫩 2个人都吃不完 最好多点人去吃 可以吃些别的菜\n' +
            '煲仔饭也不错 \n' +
            '难的在深圳吃到这么辣的东西 哈哈',
          state: '未标注'
        }
        ],
        form: {
          name: '',
          region: '',
          date1: '',
          date2: '',
          delivery: false,
          type: [],
          resource: '',
          desc: ''
        },
        formLabelWidth: '120px'
      };
    },
        methods: {
      handleClose(tag) {
        this.dynamicTags.splice(this.dynamicTags.indexOf(tag), 1);
      },

      showInput() {
        this.inputVisible = true;
        this.$nextTick(_ => {
          this.$refs.saveTagInput.$refs.input.focus();
        });
      },

      handleInputConfirm() {
        let inputValue = this.inputValue;
        if (inputValue) {
          this.dynamicTags.push(inputValue);
        }
        this.inputVisible = false;
        this.inputValue = '';
      },
          handleClick(row) {
        console.log(row);
        this.dialogFormVisible = true;
      },

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
