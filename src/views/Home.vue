<template>
  <div class="home">
    <header>
      <div class="top">
          <span style="margin-left: 5px">按月份筛选：</span>
          <el-select @change="selectList" v-model="month" size="small" clearable placeholder="请选择月份">
            <el-option
              v-for="item in options"
              :key="item.value"
              :label="item.label"
              :value="item.value">
            </el-option>
          </el-select>
          <span style="margin-left: 5px">按内容筛选：</span>
          <el-select @change="selectList" v-model="cate" size="small" clearable placeholder="请选择内容">
            <el-option
              v-for="item in category"
              :key="item.type"
              :label="item.name"
              :value="item.type">
            </el-option>
          </el-select>

        <el-button type="primary" size="small" plain @click="addBill">添加账单</el-button>
      </div>
    </header>

    <article>
      <BillTable :billTable = curPageList></BillTable>
    </article>

    <footer>
      <el-pagination
        :current-page="currentPage"
        @current-change="getCurrentPageData"
        background
        layout="prev, pager, next"
        :total="totalPage">
      </el-pagination>
      <section>
        <div>总支出金额：{{paySum}}</div>
        <div>总收入金额：{{incomeSum}}</div>
        <div>总金额：{{allSum}}</div>
      </section>
    </footer>

  </div>
</template>

<style scoped>
  @import "../assets/home.css";
</style>

<script>
// @ is an alias to /src
import BillTable from '@/components/BillTable.vue'
import data from 'vuex'

export default {
  name: 'Home',
  components: {
    BillTable
  },

  data() {
    return {
      data: [],
      list: [],
      list2: [],
      showList: [],
      curPageList: [],
      category: [],
      options: [],
      month: '',
      cate: '',
      pageSize: 7,
      currentPage: 1,
    }
  },

  mounted() {
    this.getDataAndCate()
    this.changeDateF()
    this.selectList()
    this.getOptions()
  },

  methods: {
    getDataAndCate() {
      this.data = this.dataFromX
      this.category = this.cateFromX
    },

    changeDateF() {
      this.list = [...this.data]
      this.list.sort( (a, b) => {
        let x = a.time
        let y = b.time
        return y - x
      } )
      this.list.forEach(item => {
        let time = new Date(+item.time)
        time.setHours(time.getHours(), time.getMinutes() - time.getTimezoneOffset())
        item.time = time
      })
    },

    selectList() {
      this.list2 = this.list.filter(item => {
        let yy = item.time.getFullYear()
        let mm = (item.time.getMonth() + 1 >= 10 ? item.time.getMonth() + 1 : 0 + '' + (item.time.getMonth() + 1))
        let month = yy + '' + mm
        if(this.month === '' && this.cate === '') {
          return item
        }else if(+this.month == month && this.cate === '') {
          return item
        }else if(this.month ==='' && this.cate === item.category) {
          return item
        }else if(+this.month == month && this.cate === item.category) {
          return item
        }else{
          return null
        }
      })

      this.changeShowF()
    },

    changeShowF() {
      this.showList = JSON.parse( JSON.stringify(this.list2) )
      this.showList.forEach(item => {
        let date = new Date(item.time)
        let time = date.getFullYear() + '-' + this.checkTimeF(date.getMonth() + 1) + '-' + this.checkTimeF(date.getDate())
                   + ' ' + this.checkTimeF(date.getHours()) + ':' + this.checkTimeF(date.getMinutes()) + ':' + this.checkTimeF(date.getSeconds())

        item.time = time

        if(+item.type === 0){
          item.type = '支出'
        }else if(+item.type === 1){
          item.type = '收入'
        }else{
          return item.type
        }

        this.category.filter(o => {
          if(item.category === o.type) {
            item.category = o.name
          }
        })
      })
      this.currentPage = 1
      this.getCurrentPageData(this.currentPage)
    },

    checkTimeF(date) {
      if(date < 10) {
        date = '0' + date
      }
      return date
    },

    getOptions() {
      let arr = []
      this.list.forEach(item => {
        let dateOpt = {
          value: undefined,
          label: undefined
        }
        let date = new Date(+item.time)
        let yOpt = date.getFullYear()
        let mOpt = date.getMonth() + 1 >= 10? date.getMonth() + 1 : 0 + '' + (date.getMonth() + 1)
        dateOpt.value = yOpt + '' + mOpt
        dateOpt.label = yOpt + '年' + '-' + mOpt + '月'
        arr.push(dateOpt)
      })
      let hash = {};
      arr = arr.reduce(function(item, next) {
          hash[next.value] ? '' : hash[next.value] = true && item.push(next);
          return item
      }, [])
      this.options = arr
    },

    addBill() {
      this.$router.push('addBill')
    },

    getCurrentPageData(currentPage) {
      this.currentPage = currentPage
      let start = (this.currentPage - 1) * this.pageSize
      this.curPageList = this.showList.slice( start, start + this.pageSize )
    },


  },

  computed: {
    dataFromX() {
      return this.$store.state.data
    },

    cateFromX() {
      return this.$store.state.category
    },

    totalPage: function() {
      return Math.ceil(this.showList.length / this.pageSize) * 10
    },

    paySum: function() {
      let sum = 0
      this.list2.filter(item => {
        if(+item.type === 0) {
          sum += +item.amount
        }
      })
      return sum
    },
    
    incomeSum: function() {
      let sum = 0
      this.list2.filter(item => {
        if(+item.type === 1) {
          sum += +item.amount
        }
      })
      return sum
    },

    allSum: function() {
      let sum = 0
      sum = this.incomeSum - this.paySum
      return sum
    }
  }
}
</script>
