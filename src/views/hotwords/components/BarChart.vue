<template>
  <div :class="className" :style="{height:height,width:width}" />
</template>

<script>
import echarts from 'echarts'
require('echarts/theme/roma') // echarts theme
import resize from './mixins/resize'
import axios from 'axios'
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
    },
    aspect: {
      type: String,
      default: '口味'
    },
  },
  data() {
    return {
      chart: null,
      dataX: ['不错', '服务', '服务员', '味道', '环境', '感觉', '喜欢'],
      dataY: [5002, 4512, 3474, 3025, 2469, 1953, 1656]
    }
  },
  watch: {
    aspect: {
      deep: true,
      handler(val) {
        console.log('in aspect bar val = ', val)
        this.aspect=val
        // this.requestData(val)
      }
    }
  },
  mounted() {
    this.$nextTick(() => {
      this.requestData(this.aspect)
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
    requestData(asp) {
      this.axios.post('/aspWordTop', {
        aspect: asp
      }, { emulateJSON: true }).then(
        res => {
          // console.log(res.data.status)
          var status = res.data.status
          if (status == 0) {
            this.dataX = res.data.words
            this.dataY = res.data.nums
            console.log('finish asp pic')
            this.initChart()
          }
        }
      ).catch(res => {
        console.log(res.data.status)
        console.log(res.data.msg)
      })
    },
    initChart() {
      this.chart = echarts.init(this.$el, 'roma')

      this.chart.setOption({
        title: {
          text: 'Top热词'
        },
        color: ['#de6e6b', '#2f4554', '#61a0a8'],
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
          data: this.dataX,
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
          name: '词频',
          type: 'bar',
          stack: 'vistors',
          barWidth: '60%',
          data: this.dataY,

          animationDuration
        }
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
