<template>
  <div class="app-container">
    <el-table
      :data="tableData2"
      style="width: 100%"
      height="550"
    >
      <el-table-column
        fixed
        prop="date"
        label="日期"
        width="150"
      />
      <el-table-column
        prop="text"
        label="文本"
        width="990"
      />
    </el-table>
    <div class="tabListPage">
    <el-pagination
        background
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
        layout="prev, pager, next,total"
        :total="total"
        :page-size="pagesize"
        :page-count="pageCount"
        :current-page="currentPage"
    >
    </el-pagination>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  data() {
    return {
      total: 100,
      pagesize: 10,
      pageCount: this.total /this.pagesize,
      currentPage: 1,
      // tableData2:[],
      tableData2: [{
        date: '2020-05-03',
        text: '去过好多次了，老板们人很好，还有双胞胎，特地看了一下，的确很像。游戏满多的，最喜欢古邦国，因为每次都我赢哈哈。环境还蛮干净明亮的。也没想到正好一个同学的同学住在他们楼上，真巧啊\n' +
          '比较喜欢声音很明亮的那个老板，每次上来都说这是很欢乐的游戏，其实我们想要不欢乐的游戏，哈哈',
      }, {
        date: '2020-05-02',
        text: '环境挺好的，听说夏天的时候会有新人选择在这里办户外婚礼，个人认为还是很适合的。菜色一般，整餐吃下来没有特别想推荐的。大厅的空调根本不管事儿，冻死了。服务员也比较一般。',
      },
        {
        date: '2020-05-07',
        text: '很小一间小吃店，4-5张桌子。买了一碗二两的素粉。\n' +
          '很快就下好了，要下粉的师傅给了一点辣，没有香菜，自己往里面放了点他家泡萝卜，一点青辣椒丁，调个味。泡萝卜不是很酸，带着萝卜的本味。粉还是圆粉，不过比很久以前的桂林圆米粉细些。分量也少了些，几筷子就吃完了。底汤味道还可以。整体一般，和其他味道好的桂林米粉店比较而言。\n' +
          '除去米粉热干面3，门口还有炸面窝卖，是迷你型的那种，喝的就是原味豆浆。',
      }]
    }
  },
  mounted() {

    this.$nextTick(() => {
      // this.initChart()
      this.getData(10,1)
    })
  },
  methods: {

    getData(n1,n2){
      this.axios.post("/get_all_text",{
        // 每页显示的条数
        pageSize:n1,
        // 显示第几页
        page:n2,
      },{emulateJSON: true},).then(
          res => {
            console.log(res.data.status)
            var status = res.data.status
            if (status == 0) {
              // console.log("status is 0,  = ",res.data.pos_list)
              this.tableData2 = res.data.sents_all
              this.total = res.data.total
            }
          }
      ).catch(res => {
        console.log(res.data.status)
        console.log(res.data.msg)
      })
    },
    // 每页显示的条数
    handleSizeChange(val) {
      // 改变每页显示的条数
      this.pagesize=val
      // 点击每页显示的条数时，显示第一页
      this.getData(val,1)

      // 注意：在改变每页显示的条数时，要将页码显示到第一页
      this.currentPage=1
    },
    // 显示第几页
    handleCurrentChange(val) {
      // 改变默认的页数
      console.log("current change, val = ",val)
      this.currentPage=val
      // console.log("current change, val = ",val)
      // 切换页码时，要获取每页显示的条数
      this.getData(this.pagesize,(this.currentPage)*(this.pagesize))
      // console.log("current change, val = ",val,"pagesize")
    },

  }
}
</script>

<style scoped>

</style>
