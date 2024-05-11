TODO 写一个修改用户信息的按钮,跳转到修改信息界面，跳转到这里：src\views\customer\editinfofunction\EditAccountView.vue
<template>
  <div id="manage">
    <el-container>
      <el-header>Header</el-header>
      <el-container>
        <el-aside width="200px">
          <el-menu default-active="2" class="el-menu-vertical-demo" background-color="#545c64" text-color="#fff"
            active-text-color="#ffd04b" :router=flag>
            <el-menu-item index="/customer/manage/order">
              <i class="el-icon-menu"></i>
              <span slot="title">点餐</span>
            </el-menu-item>
            <el-menu-item index="/customer/manage/history">
              <i class="el-icon-menu"></i>
              <span slot="title">历史订单查询</span>
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
      };
    },
    methods: {
      created () {
        this.activeIndex2 = this.$route.path
      }
    },
    mounted () {

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