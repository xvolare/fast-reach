<template>
  <div class="employee-management">
    <h1>商家端管理员工</h1>
    <el-form :inline="true" class="demo-form-inline">
      <el-form-item label="姓名">
        <el-input v-model="searchForm.name" placeholder="请输入姓名"></el-input>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="searchEmployees">查询</el-button>
        <el-button @click="clearSearch">清空</el-button>
      </el-form-item>
    </el-form>
    <el-button type="danger" @click="deleteSelectedEmployees">批量删除</el-button>
    <el-button type="primary" @click="openDialogAdd">新增员工</el-button>

    <el-dialog title="添加员工信息" :visible.sync="dialogFormVisibleAdd">
          <el-form :model="form">
            <el-form-item label="员工ID" :label-width="'120px'">
              <el-input v-model="form.id" autocomplete="off"></el-input>
            </el-form-item>
            <el-form-item label="员工姓名" :label-width="'120px'">
              <el-input v-model="form.name" autocomplete="off"></el-input>
            </el-form-item>
            <el-form-item label="员工账号" :label-width="'120px'">
              <el-input v-model="form.account" autocomplete="off"></el-input>
            </el-form-item>
            <el-form-item label="员工电话" :label-width="'120px'">
              <el-input v-model="form.phone" autocomplete="off"></el-input>
            </el-form-item>

            <el-form-item label="员工性别" :label-width="'120px'">
              <el-select v-model="form.sex" placeholder="请选择菜品状态">
                <el-option label="女" value=1></el-option>
                <el-option label="男" value=0></el-option>
              </el-select>
            </el-form-item>
            <el-form-item label="更新时间" :label-width="'120px'">
              <el-input v-model="form.updateTime" autocomplete="off"></el-input>
            </el-form-item>
            <el-form-item label="创建时间" :label-width="'120px'">
              <el-input v-model="form.createTime" autocomplete="off"></el-input>
            </el-form-item>
            <el-form-item label="员工密码" :label-width="'120px'">
              <el-input v-model="form.password" autocomplete="off"></el-input>
            </el-form-item>
          </el-form>
          <div slot="footer" class="dialog-footer">
            <el-button @click="dialogFormVisibleAdd = false">取 消</el-button>
            <el-button type="primary" @click="submitFormAdd">确 定</el-button>
          </div>

        </el-dialog>
    <!-- 表格 -->
    <template>
    <el-table :data="records" style="width: 100%; margin-top: 20px;" border @selection-change="handleSelectionChange">
      <el-table-column type="selection" width="55"  align="center"></el-table-column>
      <el-table-column prop="id" label="ID" width="180"></el-table-column>
      <el-table-column prop="name" label="姓名" width="180"></el-table-column>
    <el-table-column prop="account" label="账号"></el-table-column>
      <el-table-column prop="phone" label="电话"></el-table-column>
      <el-table-column label="性别" prop="sex">
        <template slot-scope="scope">
          <span v-if="scope.row.sex == 0">男</span>
          <span v-else-if="scope.row.sex == 1">女</span>
        </template>
    </el-table-column>
      <el-table-colum prop="permission" label="权限"></el-table-colum>
      <el-table-column prop="updateTime" label="更新时间"></el-table-column>
      <el-table-column prop="createTime" label="创建时间"></el-table-column>
      <el-table-column label="操作">
        <template slot-scope="scope">
          <el-button size="mini" @click="editEmployee(scope.row)">编辑</el-button>
          <el-button size="mini" type="danger" @click="open(scope.row)">删除</el-button>
        </template>
      </el-table-column>
    </el-table>
</template>
    <el-pagination @current-change="handlePageChange" :current-page="currentPage" :page-size="pageSize"
      layout="prev, pager, next, jumper" :total="total">
    </el-pagination>
  </div>
</template>

<script>
  import axios from 'axios';
  import { mapActions } from 'vuex';

  export default {
    data () {
      return {
        
        searchForm: {
          name: '', // 用于存储搜索框的值
        },
        total: 0,
        records: [],
        currentPage: 1,
        pageSize: 10,
        form: {
          id: null,
          name: '',
          account: '',
          phone: '',
          sex: '',
          permission: '',
          updateTime: '',
          createTime: '',
          password: '',
        },
        dialogFormVisible: false,
        dialogFormVisibleAdd: false,
        fileList: []
      };
    },
    methods: {
      ...mapActions(['deleteEmployeeAction']),
      searchEmployees () {
        // 这里实现搜索员工的逻辑
        this.fetchEmployees({
          name: this.searchForm.name,
          page: this.currentPage,
          pageSize: this.pageSize,
        });
      },
          // 复选框选中后执行的方法
    handleSelectionChange(val) {
      this.multipleSelection = val;
    },
      clearSearch () {
        this.searchForm.name = '';
        this.fetchEmployees({
          page: this.currentPage,
          pageSize: this.pageSize,
        });
      },
      addEmployee () {
        // 跳转到新增员工界面
        this.$router.push('api/business/employee/add'); // 替换为实际的路由路径
      },
      editEmployee (employee) {
        // 跳转到编辑员工界面，并携带员工信息
        this.$router.push({
          path: '/path/to/EditEmployeeView', // 替换为实际的路由路径
          query: { id: employee.id },
        });
      },
      open (row) {
        this.$confirm('此操作将删除该员工, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.deleteEmployee(row.id),
            this.$message({
              type: 'success',
              message: '删除成功!'
            });
        }).catch(() => {
          this.$message({
            type: 'info',
            message: '已取消删除'
          });
        });
      },
      deleteEmployee (id) {
        axios({//菜品删除
          url: '/api/business/employee/delete',
          method: 'delete',
          headers: {
            "token": localStorage.getItem('token')
          },
          params: {
            id: id
          }
        }).then(res => {
          if (res.data.msg == "NOT_LOGIN") {
            this.$router.push('/business/login')
            return;
          }
          location.reload();
          console.log(res.data.data)

        }).catch(err => {
          console.log(err.data.msg)
        })
      },
      deleteSelectedEmployees() {
    const ids = this.multipleSelection.map((employee) => employee.id);
    if (ids.length === 0) {
      this.$message({
        type: 'warning',
        message: '请至少选择一个员工进行删除'
      });
      return;
    }

    this.$confirm('此操作将永久删除选中的员工, 是否继续?', '提示', {
      confirmButtonText: '确定',
      cancelButtonText: '取消',
      type: 'warning'
    }).then(() => {
      this.deleteEmployees(ids) // 调用后端批量删除接口
        .then(() => {
          this.$message({
            type: 'success',
            message: '删除成功!'
          });
          this.fetchEmployees({
            page: this.currentPage,
            pageSize: this.pageSize,
          });
        })
        .catch(error => {
          this.$message({
            type: 'error',
            message: '删除失败'
          });
          console.error('批量删除失败:', error);
        });
    }).catch(() => {
      this.$message({
        type: 'info',
        message: '已取消删除'
      });
    });
  },
      handlePageChange (newPage) {
        this.currentPage = newPage;
        this.fetchEmployees({
          page: this.currentPage,
          pageSize: this.pageSize,
        });
      },
      fetchEmployees (params) {
        axios({
          url: '/api/business/employee/page',
          method: 'get',
          headers: {
            "token": localStorage.getItem('token')
          },
          params, // 使用params代替data，因为GET请求使用URL查询字符串传递参数
        }).then(res => {
          console.log(res)
          this.total = res.data.data.total;
          this.records = res.data.data.records;
        }).catch(err => {
          console.log(err.response.data.msg);
        });
      },

/*       提交表单 */
submitForm () {
        this.dialogFormVisible = false
        console.log(this.form.id)
        axios.post('/api/business/employee/edit', {
          id: this.form.id,
          name: this.form.name,
          account:this.form.account,
          phone:this.form.phone,
          sex: this.form.sex,
          permission: this.form.permission,
          updateTime: this.form.updateTime,
          createTime: this.form.createTime,
          password: this.form.password,
        },
          {
            headers: {
              "token": localStorage.getItem("token")
            }
          }).then(res => {
            if (res.data.msg == "NOT_LOGIN") {
              this.$router.push('/business/login')
              return;
            }
            console.log(res.data);
            location.reload();
        })
      },
      async submitFormAdd () {
        this.dialogFormVisibleAdd = false
        var res = await axios.post('/api/business/employee/add',
          {
            description: this.form.description,
            name: this.form.name,
            price: this.form.price,
            status: this.form.status
          },
          {
            headers: {
              "token": localStorage.getItem("token")
            }
          })
        if (res.data.msg == "NOT_LOGIN") {
          this.$router.push('/business/login')
          return;
        }
        this.form.id = res.data.data;
        console.log(this.form.id)
        console.log(res.data);

        this.update(this.newImg);
        location.reload();
      },
    },
    mounted () {
      this.fetchEmployees({
        page: this.currentPage,
        pageSize: this.pageSize,
      });
    },
  };
</script>

<style>
  .employee-management {
    margin: 30px;
  }
</style>