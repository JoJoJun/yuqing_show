<template>
  <el-tabs v-model="activeName" type="border-card" @tab-click="handleClick">
    <el-tab-pane :key="all" label="ALL" name="all">
      <el-row style="background:#fff;padding:16px 16px 0;margin-bottom:32px;">
        <el-col :span="24">
          <div ref="wrap" class="eee">
            <div ref="echart-right" style="height:100%;width:100%" />
          </div>
        </el-col>
      </el-row>
      <el-divider />
      <el-row>
        <el-col :span="2" />
        <el-col :span="14" style="text-align: center;">
          <!--          <el-image-->
          <!--            style="width: 700px; height: 400px"-->
          <!--            :src="url"-->
          <!--            :fit="fit"-->
          <!--          />-->
          <el-image
            style="width: 700px; height: 400px"
            :src="'data:image/png;base64,'+pic"
            :fit="fit"
          />
        </el-col>
        <el-col :span="8">
          <el-card class="box-card">
            <div slot="header" class="clearfix">
              <span style="font-size: large; color: #e81414">热词词频</span>
              <!--              <el-tag type="danger">热词词频</el-tag>-->
              <!--              <el-button style="float: right; padding: 3px 0" type="text">操作按钮</el-button>-->
            </div>
            <div v-for="o in hotwords" :key="o" class="text item">
              <i class="el-icon-caret-right" />
              {{ o.word +'      '+ o.freq }}
              <el-divider />
            </div>
          </el-card>
        </el-col>
      </el-row>
    </el-tab-pane>
    <el-tab-pane
      v-for="(item, index) in aspectTabs"
      :key="item.name"
      :label="item.title"
      :name="item.name"
    >

      <el-row v-if="key=item.name" :gutter="6">
        <el-col v-if="key=item.name" :span="12">
          <div ref="wrap">
            <el-image style="width: 400px; height: 300px" :src="'data:image/png;base64,'+asp_pic" />
          </div>
        </el-col>
        <el-col :span="12">
          <div>
            <bar-chart :aspect="item.title" style="width: 400px; height: 300px" />
          </div>
        </el-col>
      </el-row>
      <el-divider />
      <el-row>
<!--        <div>-->
<!--        <span>7天被提及次数</span>-->
<!--        <LineChart :aspect="item.title" height="100%" width="100%" :line-data-x="lineDataX" :line-data-y="lineDataY"  />-->
<!--&lt;!&ndash;          <div class="chart-container">&ndash;&gt;-->
<!--&lt;!&ndash;          <LineMarker height="100%" width="100%"/>&ndash;&gt;-->
<!--&lt;!&ndash;            </div>&ndash;&gt;-->
<!--        </div>-->
      </el-row>
    </el-tab-pane>
    <!--    <el-tab-pane label="口味" name="taste">口味</el-tab-pane>-->
    <!--    <el-tab-pane label="环境" name="env">环境</el-tab-pane>-->
    <!--    <el-tab-pane label="服务" :key='activeName'  name="service">-->
    <!--      <el-row :gutter="6" v-if="key='activeName'">-->
    <!--        <el-col :span="12" v-if="key='activeName'">-->
    <!--          <div ref="wrap" >-->
    <!--                <el-image style="width: 400px; height: 300px" :src="url" />-->
    <!--            </div>-->
    <!--        </el-col>-->
    <!--        <el-col :span="12">-->
    <!--            <div >-->
    <!--              <bar-chart style="width: 400px; height: 300px"/>-->
    <!--            </div>-->
    <!--        </el-col>-->
    <!--    </el-row>-->
    <!--      <el-divider></el-divider>-->
    <!--      <el-row>-->
    <!--        <span>7天被提及次数</span>-->
    <!--        <LineChart height="100%" width="100%"></LineChart>-->
    <!--      </el-row>-->
    <!--    </el-tab-pane>-->
  </el-tabs>
</template>

<script>
import echarts from 'echarts'
require('echarts/theme/macarons')
import BarChart from './components/BarChart'
import LineChart from './components/line'
import LineMarker from './components/LineMarker'
export default {
  name: 'Hotwords',
  components: { BarChart, LineChart, LineMarker },
  data() {
    return {
      // echarts图表
      myChart: null,
      activeName: 'all',
      // 获取网页可见区域宽
      screenWidth: document.body.clientWidth,
      category: [
        '口味',
        '环境',
        '服务'
      ],
      aspectTabs: [
        {
          title: '口味',
          name: 'taste',
          content: '口味'
        },
        {
          title: '环境',
          name: 'env',
          content: '环境'
        },
        {
          title: '服务',
          name: 'service',
          content: '服务'
        }
      ],
      barData: [0.70, 0.50, 0.30],
      hotwords: [],
      // hotwords: [
      //   {word : '吃', freq: 15081},
      //   {word : '不错', freq: 10388},
      //   {word : '味道', freq: 8084},
      //   {word : '好吃', freq: 5550},
      //   {word : '喜欢', freq: 5362},
      //   {word : '服务', freq: 5222},
      //   {word : '感觉', freq: 4867},
      //   {word : '环境', freq: 3930},{word : '价格', freq: 3121},{word : '贵', freq: 2025},
      // ],
      pic: '',
      asp_pic: '',
      lineDataX: '',
      lineDataY: '',
      url: 'https://img-blog.csdnimg.cn/a73d8f490ddb48f1bc5d2388dd78ad68.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAQmVmb3JlRWFzeQ==,size_15,color_FFFFFF,t_70,g_se,x_16'
    }
  },
  mounted() {
    this.$nextTick(() => {
      this.requestWordCloud()
      this.requestLineChartData()
      this.requestWordTop()
    })
  },
  methods: {
    requestaspNums(aspect) {
      this.axios.post('/aspect_day_nums', {
        aspect: aspect
      }, { emulateJSON: true }).then(
          res => {
            console.log('in aspect_day_nums,status = ',res.data.status)
            var status = res.data.status
            if (status == 0) {
              this.lineDataX = res.data.week_list
              this.lineDataY = res.data.freq_list
              console.log('finish asp day num, dataY = ',this.dataY)
              this.initChart()
            }
          }
      ).catch(res => {
        console.log(res.data.status)
        console.log(res.data.msg)
      })
    },
    requestAspects() {
      this.axios.get('/dash_asp_rate').then(
        res => {
          console.log(res.data.status)
          var status = res.data.status
          if (status == 0) {
            this.category = res.data.asp_list
            this.barData = res.data.num_list
            this.barGraph()
          }
        }
      ).catch(res => {
        console.log(res.data.status)
        console.log(res.data.msg)
      })
    },
    handleClick(tab, event) {
      console.log(tab.label)
      this.requestaspCloud(tab.label)
    },
    requestLineChartData() {
      this.axios.get('/dash_asp_rate').then(
        res => {
          console.log(res.data.status)
          var status = res.data.status
          if (status == 0) {
            this.category = res.data.asp_list
            this.barData = res.data.num_list
            this.barGraph()
          }
        }
      ).catch(res => {
        console.log(res.data.status)
        console.log(res.data.msg)
      })
    },
    requestWordCloud() {
      this.axios.get('/wordcloud').then(
        res => {
          // console.log(res.data.status)
          // var status = res.data.status
          // if (status == 0) {
          this.pic = res.data
          console.log('finish pic')
          // }
        }
      ).catch(res => {
        console.log(res.data.status)
        console.log(res.data.msg)
      })
    },
    requestWordTop() {
      this.axios.get('/wordTop').then(
        res => {
          console.log(res.data.status)
          var status = res.data.status
          if (status == 0) {
            this.hotwords = res.data.wordTop
          }
        }
      ).catch(res => {
        console.log(res.data.status)
        console.log(res.data.msg)
      })
    },
    requestaspCloud(aspect) {
      this.axios.post('/aspect_cloud', {
        aspect: aspect
      }, { emulateJSON: true }).then(
        res => {
          // console.log(res.data.status)
          var status = res.data.status
          if (status == 0) {
            this.asp_pic = res.data.pic
            console.log('finish asp pic')
          }
        }
      ).catch(res => {
        console.log(res.data.status)
        console.log(res.data.msg)
      })
    },
    barGraph() {
      // var myChart = this.$echarts.init(this.$refs['echart-right'])
      var myChart = echarts.init(this.$refs['echart-right'])
      // var category = this.category
      // var barData = [0.82, 0.70, 0.50, 0.30]
      var option = {
        title: {
          text: '包含aspect的文本数占比'
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
          data: this.category,
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
            name: '(%)',
            type: 'bar',
            data: this.barData,
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
.eee{height: calc(100vh - 390px);width: 100%;}
  .chart-wrapper {
    background: #fff;
    padding: 16px 16px 0;
    margin-bottom: 32px;
  }
.chart-container{
     position: relative;
     width: 100%;
     height: calc(100vh - 84px);
   }
</style>
