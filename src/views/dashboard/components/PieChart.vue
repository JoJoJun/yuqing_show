<template>
  <div :class="className" :style="{height:height,width:width}" />
</template>

<script>
import echarts from 'echarts'
require('echarts/theme/macarons') // echarts theme
import resize from './mixins/resize'

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
      chartX: [ '口味', '环境', '服务', '装修'],
      chartY: [
        // { value: 320, name: '全部' },
        { value: 240, name: '口味' },
        { value: 149, name: '环境' },
        { value: 100, name: '服务' },
        { value: 59, name: '装修' }
      ]
    }
  },
  mounted() {
    this.requestLineChartData()
    this.$nextTick(() => {
      this.initChart()
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
      this.axios.get("/dash_asp_nums").then(
          res => {
            console.log(res.data.status)
            var status = res.data.status
            if (status == 0) {
              // console.log("status is 0, pos_list = ",res.data.pos_list)
              this.chartX = res.data.asp_list
              this.chartY = res.data.asp_dic
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
        tooltip: {
          trigger: 'item',
          formatter: '{a} <br/>{b} : {c} ({d}%)'
        },
        legend: {
          left: 'center',
          bottom: '10',
          data: this.chartX
        },
        series: [
          {
            name: 'WEEKLY WRITE ARTICLES',
            type: 'pie',
            roseType: 'radius',
            radius: [15, 95],
            center: ['50%', '38%'],
            data: this.chartY ,
            animationEasing: 'cubicInOut',
            animationDuration: 2600
          }
        ]
      })
    }
  }
}
</script>
