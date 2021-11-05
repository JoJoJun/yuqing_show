<template>
  <div class="dashboard-container">
<!--    <div class="dashboard-text">name: {{ name }}</div>-->
<!--    <el-button type="primary">主要按钮</el-button>-->
<panel-group  />

    <el-row style="background:#fff;padding:16px 16px 0;margin-bottom:32px;">
      <line-chart :chart-data="lineChartData" />
    </el-row>

    <el-row :gutter="32">
      <el-col :xs="24" :sm="24" :lg="8">
        <div class="chart-wrapper">
<!--          <raddar-chart />-->
          <div id='all_text_num_chart' style="width:100%;height:300px"></div>
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
import { mapGetters } from 'vuex'
import PanelGroup from './components/PanelGroup'
import LineChart from './components/LineChart'
import RaddarChart from './components/RaddarChart'
import PieChart from './components/PieChart'
import BarChart from './components/BarChart'
import echarts from 'echarts'
require('echarts/theme/macarons')
import axios from 'axios'
const lineChartData = {
  newVisitis: {
    PosData: [100, 120, 161, 134, 105, 160, 165],
    NegData: [120, 82, 91, 154, 162, 140, 145]
  },
}
export default {
  name: 'Dashboard',
  computed: {
    ...mapGetters([
      'name'
    ])
  },
  components: {
    PanelGroup,
    LineChart,
    RaddarChart,
    PieChart,
    BarChart,
  },
  data() {
    return {
      lineChartData: lineChartData.newVisitis,
      chart1XData:['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun'],
      chart1YData:[500, 600, 450, 300, 200, 300, 500]
    }
  },
  mounted() {
    this.requestLineChartData()
    this.requestBar1ChartData()
    this.drawChart()
  },
  methods: {
    handleSetLineChartData() {
      this.lineChartData = lineChartData['newVisitis']
      console.log("in dashboard")
    },
    drawChart() {
      // const myEchart = this.$echarts.init(document.getElementById('all_text_num_chart'));
      console.log("in draw chart data:",this.chart1YData);
      var myEchart = echarts.init(document.getElementById('all_text_num_chart'));
      const option = {
        title: {
          text: '新增舆情文本'
        },
        tooltip: {},
        legend: {
          data: ['文本数量']
        },
        xAxis: {
          data: this.chart1XData
        },
        yAxis: {},
        series: [{
          name: '文本数量',
          type: 'bar',
          data: this.chart1YData
        }]
      };
      myEchart.setOption(option);
    },
    requestLineChartData() {
      this.axios.get("/dash_line").then(
          res => {
            console.log(res.data.status)
            var status = res.data.status
            if (status == 0) {
              console.log("status is 0, pos_list = ",res.data.pos_list)
              this.lineChartData = res.data.data_dict
            }
          }
      ).catch(res => {
        console.log(res.data.status)
        console.log(res.data.msg)
      })
    },
    requestBar1ChartData() {
      this.axios.get("/dash_new_origin").then(
          res => {
            // console.log(res.data.status)
            var status = res.data.status
            if (status == 0) {
              console.log(" week_list = ",res.data.week_list)
              this.chart1XData = res.data.week_list
              this.chart1YData = res.data.dates_list
              // console.log("data:",this.chart1YData)
              this.drawChart()
            }
          }
      ).catch(res => {
        console.log(res.data.status)
        console.log(res.data.msg)
      })
    }
  },
}
</script>

<!--<style lang="scss" scoped>-->
<!--.dashboard {-->
<!--  &-container {-->
<!--    margin: 30px;-->
<!--  }-->
<!--  &-text {-->
<!--    font-size: 30px;-->
<!--    line-height: 46px;-->
<!--  }-->
<!--}-->
<!--</style>-->
<style lang="scss" scoped>
.dashboard-editor-container {
  padding: 32px;
  background-color: rgb(240, 242, 245);
  position: relative;

  .github-corner {
    position: absolute;
    top: 0px;
    border: 0;
    right: 0;
  }

  .chart-wrapper {
    background: #fff;
    padding: 16px 16px 0;
    margin-bottom: 32px;
  }
}

@media (max-width:1024px) {
  .chart-wrapper {
    padding: 8px;
  }
}
</style>
