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
    },
    chartData:{
      type: Object,
      required: true
//       default: {
//         dataX:[ '不错', '喜欢', '好吃', '差', '贵', '便宜', '干净', '新鲜'],
//         dataY:[
//           // { value: 320, name: '全部' },
//           { value: 5002, name: '不错' },
//           { value: 1656, name: '喜欢' },
//           { value: 1615, name: '好吃' },
//           { value: 553, name: '差' },
//           { value: 543, name: '贵' },
//           { value: 512, name: '便宜' },
//           { value: 421, name: '干净' },
//           { value: 383, name: '新鲜' },
//         ]
// }
    },
  },
  data() {
    return {
      chart: null,
      // dataX:[ '不错', '喜欢', '好吃', '差', '贵', '便宜', '干净', '新鲜'],
      // dataY:[
      //   // { value: 320, name: '全部' },
      //   { value: 5002, name: '不错' },
      //   { value: 1656, name: '喜欢' },
      //   { value: 1615, name: '好吃' },
      //   { value: 553, name: '差' },
      //   { value: 543, name: '贵' },
      //   { value: 512, name: '便宜' },
      //   { value: 421, name: '干净' },
      //   { value: 383, name: '新鲜' },
      // ]
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
    setOptions({ dataX, dataY } = {}) {
        this.chart.setOption({
          title: {
            text: 'Top情感词'
          },
          tooltip: {
            trigger: 'item',
            formatter: '{a} <br/>{b} : {c} ({d}%)'
          },
          legend: {
            left: 'center',
            bottom: '10',
            data: dataX
          },
          series: [
            {
              name: 'WEEKLY WRITE ARTICLES',
              type: 'pie',
              roseType: 'radius',
              radius: [15, 90],
              center: ['50%', '38%'],
              data: dataY,
              animationEasing: 'cubicInOut',
              animationDuration: 2600
            }
          ]
        })

    },
    initChart() {
      this.chart = echarts.init(this.$el, 'macarons')
      this.setOptions(this.chartData)
      // this.chart.setOption({
      //   title: {
      //     text: 'Top情感词'
      //   },
      //   tooltip: {
      //     trigger: 'item',
      //     formatter: '{a} <br/>{b} : {c} ({d}%)'
      //   },
      //   legend: {
      //     left: 'center',
      //     bottom: '10',
      //     data: this.dataX
      //   },
      //   series: [
      //     {
      //       name: 'WEEKLY WRITE ARTICLES',
      //       type: 'pie',
      //       roseType: 'radius',
      //       radius: [15, 90],
      //       center: ['50%', '38%'],
      //       data: this.dataY,
      //       animationEasing: 'cubicInOut',
      //       animationDuration: 2600
      //     }
      //   ]
      // })
    }
  }
}
</script>
