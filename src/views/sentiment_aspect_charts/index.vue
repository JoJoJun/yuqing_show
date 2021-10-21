<template>
  <div class="dashboard-container">
  <el-row :gutter="20" class="button_row" align="middle">
<!--    <el-button type="success" plain>口味</el-button>-->
<!--    <el-button type="warning" plain>服务</el-button>-->
      <el-col :xs="24" :sm="24" :lg="8" align="right"><el-button type="primary" plain>环境</el-button> </el-col>
      <el-col :xs="24" :sm="24" :lg="8" align="middle"><el-button type="success" plain>口味</el-button></el-col>
      <el-col :xs="24" :sm="24" :lg="8"><el-button type="warning" plain>服务</el-button></el-col>
  </el-row>
    <el-divider/>
  <el-row :gutter="32">
    <el-col :xs="24" :sm="24" :lg="8">
      <div class="chart-wrapper">
        <div id="piechart" style="width:100%;height:300px" />
      </div>
    </el-col>
    <el-col :xs="24" :sm="24" :lg="8">
      <div class="chart-wrapper">
        <pie-chart />
      </div>
    </el-col>
    <el-col :xs="24" :sm="24" :lg="8">
      <div class="chart-wrapper">
        <bar-chart />
      </div>
    </el-col>
  </el-row>
    </div>
</template>

<script>
import PieChart from './components/PieChart'
import BarChart from './components/BarChart'
import echarts from 'echarts'
require('echarts/theme/macarons')
export default {
  name: 'SentimentAspect',
  components: {
    PieChart,
    BarChart,
  },
  mounted() {
    this.$nextTick(() => {
      this.drawPieChart()
    })
  },
  methods: {
    drawPieChart() {
      var myEchart = echarts.init(document.getElementById('piechart'));
      const option = {
        title: {
          text: '服务项情感整体占比',
          top: '10',
        },

        series: [{
          name: '文本数量',
          type: 'pie',    // 设置图表类型为饼图
            radius: '85%',  // 饼图的半径，外半径为可视区尺寸（容器高宽中较小一项）的 55% 长度。
            data:[          // 数据数组，name 为数据项名称，value 为数据项值
                {value:235, name:'正'},
                {value:294, name:'负'},
            ]},
          ]
      };
      myEchart.setOption(option);
    },
  }
}
</script>

<style scoped>
.button_row {
  padding: 32px;
  /*position: relative;*/
}
</style>
