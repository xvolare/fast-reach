TODO 负责这一部分的组员，要在顶栏展示欢迎xxx(员工姓名),然后前后端联调成功之后一定一定要把v-if改成isAdmin!!!!
<template>
  <div id="manage">
    <el-container>
      <el-header>Header</el-header>
      <el-container>
        <el-aside width="200px">
          <el-menu default-active="2" class="el-menu-vertical-demo" background-color="#545c64" text-color="#fff"
            active-text-color="#ffd04b" :router=flag>
            <el-menu-item index="/business/manage/datastatistics">
              <i class="el-icon-menu"></i>
              <span slot="title">数据统计</span>
            </el-menu-item>
            <el-menu-item index="/business/manage/dishmanage">
              <i class="el-icon-menu"></i>
              <span slot="title">菜品管理</span>
            </el-menu-item>
            <el-menu-item index="/business/manage/ordermanage">
              <i class="el-icon-document"></i>
              <span slot="title">订单管理</span>
            </el-menu-item>
            <el-menu-item index="/business/manage/employeemanage" v-if=true>
              <i class="el-icon-setting"></i>
              <span slot="title">员工管理</span>
            </el-menu-item>
          </el-menu>
        </el-aside>
        <el-main>
          <router-view></router-view>
        </el-main>
      </el-container>
    </el-container>
  </div>
</template>

<script>
  export default {
    data () {
      return {
        activeIndex2: '',
        flag: true,//router配置，默认不要动
        isAdmin: false,//判断是否是超级管理员，false是普通员工，true是超级管理员,默认是false
        name: ''//目前登录员工的姓名
      };
    },
    methods: {
      created () {
        this.activeIndex2 = this.$route.path
      }
    },
    mounted () {
      if (localStorage.getItem('permission') == 0) this.isAdmin = true;
      else this.isAdmin = false;//根据响应结果调整isAdmin
      this.name = localStorage.getItem('name')
    }
  }
</script>

<style>
  #manage {
    position: absolute;
    top: 0;
    bottom: 0;
    left: 0;
    right: 0;
  }

  .el-header,
  .el-footer {
    background-color: #B3C0D1;
    color: #333;
    text-align: center;
    line-height: 60px;
  }

  .el-aside {
    background-color: #D3DCE6;
    color: #333;
    text-align: center;
    line-height: 100%;
  }

  .el-container {
    height: 100%;
  }

  .el-menu {
    height: 100%;
  }
</style>