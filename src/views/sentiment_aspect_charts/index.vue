<template>
  <div class="dashboard-container">
  <el-row :gutter="20" class="button_row" align="middle">
<!--    <el-button type="success" plain>口味</el-button>-->
<!--    <el-button type="warning" plain>服务</el-button>-->
      <el-col :xs="24" :sm="24" :lg="8" align="right"><el-button type="primary" plain @click="onClickEnv">环境</el-button> </el-col>
      <el-col :xs="24" :sm="24" :lg="8" align="middle"><el-button type="success" plain @click="onClickTaste">口味</el-button></el-col>
      <el-col :xs="24" :sm="24" :lg="8"><el-button type="warning" plain @click="onClickService">服务</el-button></el-col>
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
        <pie-chart :chart-data="pieChartData"/>
      </div>
    </el-col>
    <el-col :xs="24" :sm="24" :lg="8">
      <div class="chart-wrapper">
        <bar-chart :chart-data="barChartData" />
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
  data() {
    return {
      barDataX: [
        '口味',
        '味道',
        '环境',
        '服务'
      ],
      barDataY: [0.82, 0.70, 0.50, 0.30],
      pieData: [          // 数据数组，name 为数据项名称，value 为数据项值
        {value:235, name:'正'},
        {value:294, name:'负'},
      ],
      //用于中间的pie图
      pieChartData:{
        dataX:[ '不错', '喜欢', '好吃', '差', '贵', '便宜', '干净', '新鲜'],
        dataY:[
          // { value: 320, name: '全部' },
          { value: 5002, name: '不错' },
          { value: 1656, name: '喜欢' },
          { value: 1615, name: '好吃' },
          { value: 553, name: '差' },
          { value: 543, name: '贵' },
          { value: 512, name: '便宜' },
          { value: 421, name: '干净' },
          { value: 383, name: '新鲜' },
        ]
      },
      barChartData: {
        aspect: '口味',
        dataX:['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun'],
        dataPos:[20, 48, 48, 77, 72, 60, 30],
        dataNeg:[80, 52, 52, 23, 28, 40, 70]
      }
    }
  },
  mounted() {
    this.$nextTick(() => {
      // this.drawPieChart()
      this.onClickEnv()
    })
  },
  methods: {
    onClickEnv() {
      console.log('click env')
      var asp = '环境'
      this.requesSentiPi(asp)
      this.requestPieChartData(asp)
      this.requestBarChartData(asp)
    },
    onClickTaste() {
      console.log('click taste')
      var asp = '口味'
      this.requesSentiPi(asp)
      this.requestPieChartData(asp)
      this.requestBarChartData(asp)
    },
    onClickService() {
      console.log('click service')
      var asp = '服务'
      this.requesSentiPi(asp)
      this.requestPieChartData(asp)
      this.requestBarChartData(asp)
    },
    requesSentiPi(aspect) {
      this.axios.post('/senti/top_senti_words', {
        aspect: aspect
      }, { emulateJSON: true }).then(
          res => {
            console.log('in senti_word pie,status = ',res.data.status)
            var status = res.data.status
            if (status == 0) {
              this.pieChartData = res.data.data_dict
              // console.log('finish asp day num, dataY = ',this.dataY)
              this.drawPieChart(aspect)
            }
          }
      ).catch(res => {
        console.log(res.data.status)
        console.log(res.data.msg)
      })
    },
    requestPieChartData(aspect) {
      this.axios.post('/senti/pos_neg_pie', {
        aspect: aspect
      }, { emulateJSON: true }).then(
          res => {
            console.log('in aspect_pie,status = ',res.data.status)
            var status = res.data.status
            if (status == 0) {
              this.pieData = res.data.data
              // console.log('finish asp day num, dataY = ',this.dataY)
              // this.drawPieChart(aspect)
            }
          }
      ).catch(res => {
        console.log(res.data.status)
        console.log(res.data.msg)
      })
    },
    requestBarChartData(aspect) {
      this.axios.post('/senti/aspect_senti_bar', {
        aspect: aspect
      }, { emulateJSON: true }).then(
          res => {
            console.log('in aspect_pie,status = ',res.data.status)
            var status = res.data.status
            if (status == 0) {
              this.barChartData = res.data.data_dict
              // console.log('finish asp day num, dataY = ',this.dataY)
              // this.drawPieChart(aspect)
            }
          }
      ).catch(res => {
        console.log(res.data.status)
        console.log(res.data.msg)
      })
    },
    drawPieChart(aspect) {
      var myEchart = echarts.init(document.getElementById('piechart'));
      const option = {
        title: {
          text: aspect+':情感整体占比',
          top: '10',
        },

        series: [{
          name: '文本数量',
          type: 'pie',    // 设置图表类型为饼图
            radius: '85%',  // 饼图的半径，外半径为可视区尺寸（容器高宽中较小一项）的 55% 长度。
            data:this.pieData
        },
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
