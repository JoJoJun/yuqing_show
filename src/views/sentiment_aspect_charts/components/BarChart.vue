<template>
  <div :class="className" :style="{height:height,width:width}" />
</template>

<script>
import echarts from 'echarts'
require('echarts/theme/infographic') // echarts theme
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
    },
    // aspect: {
    //   type: String,
    //   default: '口味'
    // },
    chartData: {
      type: Object,
      required: true
    }
  },
  data() {
    return {
      chart: null,
      // dataX:['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun'],
      // dataPos:[20, 48, 48, 77, 72, 60, 30],
      // dataNeg:[80, 52, 52, 23, 28, 40, 70]
    }
  },
  watch: {
    chartData: {
      deep: true,
      handler(val) {
        this.setOptions(val)
      }
    }
  },
  mounted() {
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
    setOptions({aspect, dataX, dataPos, dataNeg } = {}) {
      this.chart.setOption({
        title: {
          text: '围绕'+aspect+'的情感变化'
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
          data: dataX,
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
          name: '正向',
          type: 'bar',
          stack: 'vistors',
          barWidth: '60%',
          data: dataPos,
          animationDuration
        },
          //   {
          //   name: 'pageC',
          //   type: 'bar',
          //   stack: 'vistors',
          //   barWidth: '60%',
          //   data: [30, 52, 200, 334, 390, 330, 220],
          //   animationDuration
          // },
          {
            name: '负向',
            type: 'bar',
            stack: 'vistors',
            barWidth: '60%',
            data: dataNeg,
            animationDuration
          }
          // {
          //   name: 'pageC',
          //   type: 'bar',
          //   stack: 'vistors',
          //   barWidth: '60%',
          //   data: [30, 52, 200, 334, 390, 330, 220],
          //   animationDuration
          // }
        ]
      })
    },
    initChart() {
      this.chart = echarts.init(this.$el, 'infographic')
      this.setOptions(this.chartData)
      // this.chart.setOption({
      //   title: {
      //     text: '围绕'+this.aspect+'的情感变化'
      //   },
      //   tooltip: {
      //     trigger: 'axis',
      //     axisPointer: { // 坐标轴指示器，坐标轴触发有效
      //       type: 'shadow' // 默认为直线，可选为：'line' | 'shadow'
      //     }
      //   },
      //   grid: {
      //     top: 30,
      //     left: '2%',
      //     right: '2%',
      //     bottom: '3%',
      //     containLabel: true
      //   },
      //   xAxis: [{
      //     type: 'category',
      //     data: this.dataX,
      //     axisTick: {
      //       alignWithLabel: true
      //     }
      //   }],
      //   yAxis: [{
      //     type: 'value',
      //     axisTick: {
      //       show: false
      //     }
      //   }],
      //   series: [{
      //     name: '正向',
      //     type: 'bar',
      //     stack: 'vistors',
      //     barWidth: '60%',
      //     data: this.dataPos,
      //     animationDuration
      //      },
      //   //   {
      //   //   name: 'pageC',
      //   //   type: 'bar',
      //   //   stack: 'vistors',
      //   //   barWidth: '60%',
      //   //   data: [30, 52, 200, 334, 390, 330, 220],
      //   //   animationDuration
      //   // },
      //     {
      //     name: '负向',
      //     type: 'bar',
      //     stack: 'vistors',
      //     barWidth: '60%',
      //     data: this.dataNeg,
      //     animationDuration
      //   }
      //     // {
      //   //   name: 'pageC',
      //   //   type: 'bar',
      //   //   stack: 'vistors',
      //   //   barWidth: '60%',
      //   //   data: [30, 52, 200, 334, 390, 330, 220],
      //   //   animationDuration
      //   // }
      //   ]
      // })
    }
  }
}
</script>
