<template>
  <div :class="className" :style="{height:height,width:width}" />
</template>

<script>
import echarts from 'echarts'
require('echarts/theme/macarons') // echarts theme
import resize from './mixins/resize'

const animationDuration = 6000

export default {
  mixins: [resize],
  props: {
    className: {
      type: String,
      default: 'chart'
    },
    width: {
      type: String,
      default: '100%'
    },
    height: {
      type: String,
      default: '300px'
    }
  },
  data() {
    return {
      chart: null,
      chartX: ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun'],
      chartY: [79, 52, 200, 334, 390, 330, 220]
    }
  },
  mounted() {

    this.$nextTick(() => {
      // this.initChart()
      this.requestLineChartData()
    })
  },
  beforeDestroy() {
    if (!this.chart) {
      return
    }
    this.chart.dispose()
    this.chart = null
  },
  methods: {
    requestLineChartData() {
      this.axios.get("/dash_new_labeled").then(
          res => {
            console.log(res.data.status)
            var status = res.data.status
            if (status == 0) {
              // console.log("status is 0,  = ",res.data.pos_list)
              this.chartX = res.data.week_list
              this.chartY = res.data.dates_list
              this.initChart()
            }
          }
      ).catch(res => {
        console.log(res.data.status)
        console.log(res.data.msg)
      })
    },
    initChart() {
      this.chart = echarts.init(this.$el, 'macarons')

      this.chart.setOption({
        title: {
          text: '新增标记文本数'
        },
        tooltip: {
          trigger: 'axis',
          axisPointer: { // 坐标轴指示器，坐标轴触发有效
            type: 'shadow' // 默认为直线，可选为：'line' | 'shadow'
          }
        },
        grid: {
          top: 30,
          left: '2%',
          right: '2%',
          bottom: '3%',
          containLabel: true
        },
        xAxis: [{
          type: 'category',
          data: this.chartX,
          axisTick: {
            alignWithLabel: true
          }
        }],
        yAxis: [{
          type: 'value',
          axisTick: {
            show: false
          }
        }],
        series: [{
          name: '新增标记文本数',
          type: 'bar',
          stack: 'vistors',
          barWidth: '60%',
          data: this.chartY,
          animationDuration
           },
          // {
        //   name: 'pageB',
        //   type: 'bar',
        //   stack: 'vistors',
        //   barWidth: '60%',
        //   data: [80, 52, 200, 334, 390, 330, 220],
        //   animationDuration
        // }, {
        //   name: 'pageC',
        //   type: 'bar',
        //   stack: 'vistors',
        //   barWidth: '60%',
        //   data: [30, 52, 200, 334, 390, 330, 220],
        //   animationDuration
        // }
        ]
      })
    }
  }
}
</script>
