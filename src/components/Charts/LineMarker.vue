<template>
  <div :id="id" :class="className" :style="{height:height,width:width}" />
</template>

<script>
import echarts from 'echarts'
import resize from './mixins/resize'

export default {
  mixins: [resize],
  props: {
    className: {
      type: String,
      default: 'chart'
    },
    id: {
      type: String,
      default: 'chart'
    },
    width: {
      type: String,
      default: '200px'
    },
    height: {
      type: String,
      default: '200px'
    },
    aspect: {
      type: String,
      default: '口味'
    },
    LineDataX: {
      type: Object,
      required: true
    },
    LineDataY: {
      type: Object,
      required: true
    }
  },
  data() {
    return {
      chart: null,
      dataX: ['Mon', 'Tue', 'Wed', 'Thu', 'Fri','Sat', 'Sun'],
      dataY: [22, 18, 19, 13.98, 15.04, 1.20, 10, 12, 14, 12.22, 16.5, 12.2]
    }
  },
  mounted() {
    this.$nextTick(() => {
      console.log("in lineMarker,this aspect =",this.aspect)
      console.log("in line marker datay=",this.LineDataY)
      this.initChart(LineDataX, LineDataY)
      // console.log('finish initchart')
      // this.requestaspNums(this.aspect)
      // console.log("finish request")
    })
  },
  beforeDestroy() {
    if (!this.chart) {
      return
    }
    this.chart.dispose()
    this.chart = null
  },
  // watch: {
  //   chartData: {
  //     deep: true,
  //     handler(val) {
  //       this.setOptions(val)
  //     }
  //   }
  // },
  methods: {
    requestaspNums(aspect) {
      this.axios.post('/aspect_day_nums', {
        aspect: aspect
      }, { emulateJSON: true }).then(
          res => {
            console.log('in aspect_day_nums,status = ',res.data.status)
            var status = res.data.status
            if (status == 0) {
              this.dataX = res.data.week_list
              this.dataY = res.data.freq_list
              console.log('finish asp day num, dataY = ',this.dataY)
              this.initChart()
            }
          }
      ).catch(res => {
        console.log(res.data.status)
        console.log(res.data.msg)
      })
    },
    initChart(LineDataX, LineDataY) {
      this.chart = echarts.init(document.getElementById(this.id))

      this.chart.setOption({
        backgroundColor: '#394056',
        title: {
          top: 20,
          text: this.aspect + ' 出现次数变化',
          textStyle: {
            fontWeight: 'normal',
            fontSize: 16,
            color: '#F1F1F3'
          },
          left: '1%'
        },
        tooltip: {
          trigger: 'axis',
          axisPointer: {
            lineStyle: {
              color: '#57617B'
            }
          }
        },
        legend: {
          top: 20,
          icon: 'rect',
          itemWidth: 14,
          itemHeight: 5,
          itemGap: 13,
          // data: ['CMCC', 'CTCC', 'CUCC'],
          data: ['出现频率'],
          right: '4%',
          textStyle: {
            fontSize: 12,
            color: '#F1F1F3'
          }
        },
        grid: {
          top: 100,
          left: '2%',
          right: '2%',
          bottom: '2%',
          containLabel: true
        },
        xAxis: [{
          type: 'category',
          boundaryGap: false,
          axisLine: {
            lineStyle: {
              color: '#57617B'
            }
          },
          // data: ['13:00', '13:05', '13:10', '13:15', '13:20', '13:25', '13:30', '13:35', '13:40', '13:45', '13:50', '13:55']
          // data: this.dataX
          data: LineDataX
        }],
        yAxis: [{
          type: 'value',
          name: '(%)',
          axisTick: {
            show: false
          },
          axisLine: {
            lineStyle: {
              color: '#57617B'
            }
          },
          axisLabel: {
            margin: 10,
            textStyle: {
              fontSize: 14
            }
          },
          splitLine: {
            lineStyle: {
              color: '#57617B'
            }
          }
        }],
        series: [{
          name: '出现频率',
          type: 'line',
          smooth: true,
          symbol: 'circle',
          symbolSize: 5,
          showSymbol: false,
          lineStyle: {
            normal: {
              width: 1
            }
          },
          areaStyle: {
            normal: {
              color: new echarts.graphic.LinearGradient(0, 0, 0, 1, [{
                offset: 0,
                color: 'rgba(137, 189, 27, 0.3)'
              }, {
                offset: 0.8,
                color: 'rgba(137, 189, 27, 0)'
              }], false),
              shadowColor: 'rgba(0, 0, 0, 0.1)',
              shadowBlur: 10
            }
          },
          itemStyle: {
            normal: {
              color: 'rgb(137,189,27)',
              borderColor: 'rgba(137,189,2,0.27)',
              borderWidth: 12

            }
          },
          // data: this.dataY
          data: LineDataY
        },
        //   {
        //   name: 'CTCC',
        //   type: 'line',
        //   smooth: true,
        //   symbol: 'circle',
        //   symbolSize: 5,
        //   showSymbol: false,
        //   lineStyle: {
        //     normal: {
        //       width: 1
        //     }
        //   },
        //   areaStyle: {
        //     normal: {
        //       color: new echarts.graphic.LinearGradient(0, 0, 0, 1, [{
        //         offset: 0,
        //         color: 'rgba(0, 136, 212, 0.3)'
        //       }, {
        //         offset: 0.8,
        //         color: 'rgba(0, 136, 212, 0)'
        //       }], false),
        //       shadowColor: 'rgba(0, 0, 0, 0.1)',
        //       shadowBlur: 10
        //     }
        //   },
        //   itemStyle: {
        //     normal: {
        //       color: 'rgb(0,136,212)',
        //       borderColor: 'rgba(0,136,212,0.2)',
        //       borderWidth: 12
        //
        //     }
        //   },
        //   data: [120, 110, 125, 145, 122, 165, 122, 220, 182, 191, 134, 150]
        // },
        //   {
        //   name: 'CUCC',
        //   type: 'line',
        //   smooth: true,
        //   symbol: 'circle',
        //   symbolSize: 5,
        //   showSymbol: false,
        //   lineStyle: {
        //     normal: {
        //       width: 1
        //     }
        //   },
        //   areaStyle: {
        //     normal: {
        //       color: new echarts.graphic.LinearGradient(0, 0, 0, 1, [{
        //         offset: 0,
        //         color: 'rgba(219, 50, 51, 0.3)'
        //       }, {
        //         offset: 0.8,
        //         color: 'rgba(219, 50, 51, 0)'
        //       }], false),
        //       shadowColor: 'rgba(0, 0, 0, 0.1)',
        //       shadowBlur: 10
        //     }
        //   },
        //   itemStyle: {
        //     normal: {
        //       color: 'rgb(219,50,51)',
        //       borderColor: 'rgba(219,50,51,0.2)',
        //       borderWidth: 12
        //     }
        //   },
        //   data: [220, 182, 125, 145, 122, 191, 134, 150, 120, 110, 165, 122]
        // }
        ]
      })
    }
  }
}
</script>
