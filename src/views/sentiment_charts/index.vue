<template>

  <div class="chart-container">
<!--    <h1>charts!</h1>-->
    <el-row align="middle">
      <el-col :span="12" align="middle">
        <div id="piechart" style="width:90%;height:240px; background-color: #edf3fa"/>
      </el-col>
      <el-col :span="12" align="middle">
        <div ref="echart-right" style="height:240px;width:100%"></div>
      </el-col>
    </el-row>
    <el-divider />
    <el-row style="width: 100%; height: calc(100vh - 84px);">
      <chart height="70%" width="100%" />
    </el-row>
  </div>
</template>

<script>
import Chart from '@/components/Charts/MixChart'
import echarts from 'echarts'
require('echarts/theme/macarons')
export default {
  name: 'SentimentChart',
  components: { Chart },
  mounted() {
    this.drawPieChart(),
      this.barGraph()
  },
  methods: {
    drawPieChart() {
      var myEchart = echarts.init(document.getElementById('piechart'));
      const option = {
        title: {
          text: '情感整体占比',
          top: '20',
        },

        series: [{
          name: '文本数量',
          type: 'pie',    // 设置图表类型为饼图
            radius: '85%',  // 饼图的半径，外半径为可视区尺寸（容器高宽中较小一项）的 55% 长度。
            data:[          // 数据数组，name 为数据项名称，value 为数据项值
                {value:235, name:'正'},
                {value:274, name:'负'},
            ]},
          ]
      };
      myEchart.setOption(option);
    },
    barGraph() {
      // var myChart = this.$echarts.init(this.$refs['echart-right'])
      var myChart = echarts.init(this.$refs['echart-right'])
      var category = [
        '口味',
        '味道',
        '环境',
        '服务'
      ]
      var barData = [0.82, 0.70, 0.50, 0.30]
      var option = {
        title: {
          text: 'aspect各自正向情感占比'
        },
        tooltip: {
          trigger: 'axis',
          axisPointer: {
            type: 'shadow'
          }
        },
        grid: {
          left: '3%',
          right: '4%',
          bottom: '3%',
          containLabel: true
        },
        xAxis: {
          type: 'value',
          axisLine: {
            show: false
          },
          axisTick: {
            show: false
          },
          splitLine: { show: false },
          axisLabel: { show: false }
        },
        yAxis: {
          type: 'category',
          data: category,
          splitLine: { show: false },
          axisLine: {
            show: false
          },
          axisTick: {
            show: false
          },
          offset: 10,
          nameTextStyle: {
            fontSize: 10
          }
        },
        series: [
          {
            name: '占比',
            type: 'bar',
            data: barData,
            barWidth: 14,
            barGap: 10,
            smooth: true,
            label: {
              normal: {
                show: true,
                position: 'right',
                offset: [5, -2],
                textStyle: {
                  color: '#333',
                  fontSize: 13
                }
              }
            },
            itemStyle: {
              emphasis: {
                barBorderRadius: 7
              },
              normal: {
                barBorderRadius: 7,
                color: new echarts.graphic.LinearGradient(0, 0, 1, 0, [
                  { offset: 0, color: '#3977E6' },
                  { offset: 1, color: '#37BBF8' }
                ])
              }
            }
          }
        ]
      }
      myChart.setOption(option)
    }
  }
}
</script>

<style scoped>
.chart-container{
  position: relative;
  width: 100%;
  /*height: calc(100vh - 84px);*/
}
</style>
