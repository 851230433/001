<template>
  <div>
    <div class="backArea">
      <el-page-header @back="goBack" content="添加账单页面" style="margin: 20px"></el-page-header>
    </div>
    <div class="formArea">
      <el-form ref="form" :model="form" label-width="90px">
        <el-form-item label="账单时间：">
          <el-col :span="10">
            <el-date-picker type="date" placeholder="选择日期" v-model="form.date1" style="width: 100%;"></el-date-picker>
          </el-col>
          <el-col class="line" :span="2">-</el-col>
          <el-col :span="10">
            <el-time-picker placeholder="选择时间" v-model="form.date2" style="width: 100%;"></el-time-picker>
          </el-col>
        </el-form-item>
        <el-form-item label="账单内容：">
          <el-col :span='10'>
            <el-select v-model="form.category" placeholder="请选择账单内容">
              <el-option label="车贷" value="1bcddudhmh"></el-option>
              <el-option label="车辆保养" value="hc5g66kviq"></el-option>
              <el-option label="房贷" value="8s0p77c323"></el-option>
              <el-option label="房屋租赁" value="0fnhbcle6hg"></el-option>
              <el-option label="家庭用品" value="odrjk823mj8"></el-option>
              <el-option label="交通" value="bsn20th0k2o"></el-option>
              <el-option label="旅游" value="j1h1nohhmmo"></el-option>
              <el-option label="日常饮食" value="3tqndrjqgrg"></el-option>
              <el-option label="工资" value="s73ijpispio"></el-option>
              <el-option label="股票投资" value="1vjj47vpd28"></el-option>
              <el-option label="基金投资" value="5il79e11628"></el-option>
            </el-select>
          </el-col>
          <el-col :span='12'>
            <el-form-item label="账单金额：">
              <el-input type="number" v-model="form.amount"></el-input>
            </el-form-item>
          </el-col>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="onSubmit" size="small" plain>立即添加</el-button>
          <el-button size="small" @click="goBack">取消</el-button>
        </el-form-item>
      </el-form>
    </div>    
  </div>
</template>

<script>
  export default {
    data() {
      return {
        form: {
          category: '',
          date1: '',
          date2: '',
          amount: ''
        },

        addBill: {
          type: undefined,
          time: undefined,
          category: undefined,
          amount: undefined
        }
      }
    },
    methods: {
      onSubmit() {
        if(this.form.category && this.form.date1 && this.form.date2 && this.form.amount) {
          let d1 = +this.form.date1
          let d2 = this.form.date2.getHours() * 3600 + this.form.date2.getMinutes() * 60 + this.form.date2.getSeconds() * 1
          let time = d1 + d2
          if(time > +new Date()) {
            this.$alert('请确认账单时间是否正确！')
            return null
          }else{
            this.addBill.time = time
          }

          this.addBill.category = this.form.category

          if(this.form.category === 's73ijpispio' || this.form.category === '1vjj47vpd28' || this.form.category === '5il79e11628') {
            this.addBill.type = 1
          }else{
            this.addBill.type = 0
          }

          this.addBill.amount = this.form.amount

          this.$store.commit('addBill', this.addBill)
          this.$alert('添加成功！')
          
          this.$router.push('/')
        }else{
          this.$alert('账单信息不完整!')
        }

      },
      goBack() {
        this.$router.push('/')
      },
    }
  }
</script>

<style scoped>
  .backArea {
    width: 100%;
    height: 100px;
    display: flex;
    align-items: center;
    justify-content: left;
    box-shadow: 0 0 3px 1px gray;
    margin-bottom: 50px;
  }

  .formArea {
    width: 70%;
    margin: 0 auto;
    padding: 20px;
    border-radius: 20px;
    background: #fff;
    box-shadow:  20px 20px 60px #d0d5d9, 
                -20px -20px 60px #ffffff;
  }
</style>