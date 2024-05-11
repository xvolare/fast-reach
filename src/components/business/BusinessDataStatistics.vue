TODO 把data里面的dateList,turnoverList的数据渲染出来就行，一定要好看一点，axios我已经写好了
<template>
  <h1>展示报表数据</h1>
</template>

<script>
  import axios from 'axios';

  export default {
    data () {
      return {
        dateList: [],//日期列表
        turnoverList: [],//营业额，与日期列表一一对应
        userList: [],//每日付费顾客数量，与日期列表一一对应
        nameList: [],//TOP10菜品名称
        numberList: []//Top10卖了多少菜品
      };
    },
    methods: {
    },
    mounted () {
      axios({//分天营业额统计
        url: '/api/business/report/turnover',
        method: 'get',
        headers: {
          "token": localStorage.getItem('token'),
        }
      }).then(res => {
        this.dateList = res.data.data.dateList,
          this.turnoverList = res.data.data.turnoverList
      }).catch(err => {
        console.log(err.data.msg)
      })
      axios({//付费顾客数量统计
        url: '/api/business/report/user',
        method: 'get',
        headers: {
          "token": localStorage.getItem('token')
        }
      }).then(res => {
        this.userList = res.data.data.userList
      }).catch(err => {
        console.log(err.data.msg)
      })

      axios({//Top10统计
        url: '/api/business/report/top',
        method: 'get',
        headers: {
          "token": localStorage.getItem('token')
        }
      }).then(res => {
        this.nameList = res.data.data.nameList,
          this.numberList = res.data.data.numberList
      }).catch(err => {
        console.log(err.data.msg)
      })
    }
  }
</script>

<style>

</style>