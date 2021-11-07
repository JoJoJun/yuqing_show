<template>
  <div class="chart-container">
    <chart :aspect="aspect" height="100%" width="100%" :line-data-x="LineDataX" :line-data-y="LineDataY" />
  </div>
</template>

<script>
import Chart from '@/components/Charts/LineMarker'
// import Chart from './LineMarker'

export default {
  name: 'LineChart',
  components: { Chart },
  props: {
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
  }
}
</script>

<style scoped>
.chart-container{
  position: relative;
  width: 100%;
  height: calc(100vh - 84px);
}
</style>

