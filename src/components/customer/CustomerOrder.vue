<template>

  <!-- <el-container> -->
  <!-- 顶栏容器 -->
  <!-- 主要区域容器 -->
  <!-- 底栏容器 -->
  <div>
    <div>
      &nbsp;&nbsp;&nbsp;
      <div class="ss">
        <el-input v-model="input" placeholder="请输入菜品名称"></el-input>
      </div>
      &nbsp;&nbsp;&nbsp;
      <div class="ss">
        <el-select v-model="value" placeholder="请选择菜品状态">
          <el-option v-for="item in options" :key="item.value" :label="item.label" :value="item.value">
          </el-option>
        </el-select>
      </div>
      &nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;
      <el-button type="primary" @click="searchDish">查询菜品</el-button>
      ￥{{this.cost}}

      <span id="right"><el-button type="danger" @click="submitDish">提交订单</el-button>
      </span>


    </div>

    <el-table :data="records" style="width: 100%">
      <el-table-column label="图片" width="150">
        <template slot-scope="scope">
          <img :src="scope.row.image" alt="菜品图片" style="width: 100%; height: auto;">
        </template>
      </el-table-column>
      <el-table-column prop="id" label="id" width="180" v-if="false"></el-table-column>
      <el-table-column prop="name" label="菜品名称" width="180"></el-table-column>
      <el-table-column prop="price" label="价格" width="120"></el-table-column>
      <el-table-column label="描述" prop="description"></el-table-column>

      <el-table-column label="状态" prop="status">
        <template slot-scope="scope">
          <span v-if="scope.row.status == 0">停止售卖</span>
          <span v-else-if="scope.row.status == 1">正在售卖</span>
        </template>
      </el-table-column>
      <el-table-column label="销售次数" prop="sum" width="120"></el-table-column>
      <el-table-column label="功能" width="150">
        <template slot-scope="scope">
          <el-input-number size="mini" v-model="scope.row.num" :min="0" :max="100" label="描述文字"
            :disabled="scope.row.status == 0" @change="handleChange"></el-input-number>
        </template>
      </el-table-column>
    </el-table>

    <div style="margin-top: 20px;">
      <el-pagination :current-page="currentPage" :page-size="pageSize" @current-change="changePage" layout="total"
        :total="total"></el-pagination>
    </div>


  </div>
  <!-- 
    <el-footer>Footer</el-footer>
  </el-container> -->
</template>
<script>
  import axios from 'axios';

  export default {
    data () {
      return {
        cost: 0,
        options: [{
          value: 2,
          label: '全部'
        }, {
          value: 1,
          label: '正在售卖'
        }, {
          value: 0,
          label: '停止售卖'
        },
        ],
        value: '',
        input: '',
        //////
        total: null,
        records: [],
        orders: [],
        currentPage: 1, // 当前页码  
        pageSize: 10, // 每页显示的条目数  
        dialogFormVisible: false,
        dialogFormVisibleAdd: false,
      };
    },
    methods: {
      submitDish () {

      },
      accAdd (arg1, arg2) {
        var r1, r2, m, c;
        try {
          r1 = arg1.toString().split(".")[1].length;
        }
        catch (e) {
          r1 = 0;
        }
        try {
          r2 = arg2.toString().split(".")[1].length;
        }
        catch (e) {
          r2 = 0;
        }
        c = Math.abs(r1 - r2);
        m = Math.pow(10, Math.max(r1, r2));
        if (c > 0) {
          var cm = Math.pow(10, c);
          if (r1 > r2) {
            arg1 = Number(arg1.toString().replace(".", ""));
            arg2 = Number(arg2.toString().replace(".", "")) * cm;
          } else {
            arg1 = Number(arg1.toString().replace(".", "")) * cm;
            arg2 = Number(arg2.toString().replace(".", ""));
          }
        } else {
          arg1 = Number(arg1.toString().replace(".", ""));
          arg2 = Number(arg2.toString().replace(".", ""));
        }
        return (arg1 + arg2) / m;
      },
      handleChange () {
        var sum = 0;
        for (var i = 0; i < this.records.length; ++i) {
          var sprice = this.records[i].price.slice(1)
          sum = this.accAdd(sprice * this.records[i].num, sum)
        }
        console.log(sum)
        this.cost = sum
      },
      add (row) {
        this.orders.push({
          id: row.id,

        })
      },
      ////////////////////////////
      searchDish () {
        axios({//菜品分页查询
          url: '/api/business/dish/page',
          method: 'get',
          headers: {
            "token": localStorage.getItem('token')
          },
          params: {
            name: this.input,//就是输入框得到的要查询菜品的名字，默认没有
            status: this.value
          }
        }).then(res => {
          console.log(res.data)
          if (res.data.msg == "NOT_LOGIN") {
            this.$router.push('/business/login')
            return;
          }
          console.log(res.data.data)
          this.total = res.data.data.total
          this.records = res.data.data.records
        }).catch(err => {
          console.log(err.data.msg)
        })
      },

      changePage () {
        axios({//菜品分页查询
          url: '/api/business/dish/page',
          method: 'get',
          headers: {
            "token": localStorage.getItem('token')
          },
          params: {
            name: null,//就是输入框得到的要查询菜品的名字，默认没有
            page: this.currentPage,
            status: 2
          }
        }).then(res => {
          console.log(res.data)
          if (res.data.msg == "NOT_LOGIN") {
            this.$router.push('/business/login')
            return;
          }
          console.log(res.data.data)
          this.total = res.data.data.total
          for (var i = 0; i < this.total; ++i) {
            var form = res.data.data.records[i]
            form.num = 0
            this.records.push(form)
          }
        }).catch(err => {
          console.log(err.data.msg)
        })
      }
    },
    mounted () {
      this.changePage()
    },

  }
</script>

<style>
  /* .el-container {
    min-height: 100vh;
  } */

  .el-row {
    margin-bottom: 20px;

    &:last-child {
      margin-bottom: 20;
    }
  }

  .bg-purple-dark {
    background: #99a9bf;
  }

  .ss {
    display: inline-block
  }

  #right {
    display: inline-block;
    float: right;
  }
</style>