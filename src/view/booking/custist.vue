<template>
  <div class="app-container">
    <!-- 搜索筛选区域（表单形式） -->
    <el-card class="filter-container" shadow="never">
      <div slot="header" class="clearfix">
        <span class="card-title">筛选条件</span>
        <el-button style="float: right; padding: 3px 0" type="text" @click="handleReset">重置</el-button>
      </div>
      <el-form :inline="true" :model="listQuery" class="demo-form-inline" label-width="80px">
        <el-form-item label="客户名称">
          <el-input v-model="listQuery.name" placeholder="请输入客户名称" clearable />
        </el-form-item>
        <el-form-item label="手机号">
          <el-input v-model="listQuery.phone" placeholder="请输入手机号" clearable />
        </el-form-item>
        <el-form-item label="状态">
          <el-select v-model="listQuery.status" placeholder="请选择状态" clearable>
            <el-option label="启用" value="1" />
            <el-option label="禁用" value="0" />
          </el-select>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" icon="el-icon-search" @click="handleFilter">查询</el-button>
        </el-form-item>
      </el-form>
    </el-card>

    <!-- 批量操作工具栏 -->
    <div class="toolbar">
     <div class="toolbar-right">
         <el-button type="primary" icon="el-icon-plus" @click="handleCreate">新增客户</el-button>
      <el-button type="danger" icon="el-icon-delete" @click="handleBatchDelete" :disabled="multipleSelection.length === 0">批量删除</el-button>
      
        <!-- <el-button type="success" icon="el-icon-upload2" @click="handleImport">导入</el-button>
        <el-button type="warning" icon="el-icon-download" @click="handleExport">导出</el-button> -->
      </div>
    </div>

    <!-- 数据表格主体 -->
    <el-table
      :data="list"
      border
      fit
      highlight-current-row
      style="width: 100%;"
      @selection-change="handleSelectionChange"
      v-loading="listLoading"
    >
      <el-table-column type="selection" width="55" align="center" />
      <el-table-column label="ID" prop="id" width="80" align="center" />
      <el-table-column label="客户名称" prop="name" align="center" />
      <el-table-column label="手机号" prop="phone" align="center" />
      <el-table-column label="邮箱" prop="email" align="center" />
      <el-table-column label="状态" prop="status" align="center" width="100">
        <template slot-scope="{row}">
          <el-tag :type="row.status | statusFilter">{{ row.status === '1' ? '启用' : '禁用' }}</el-tag>
        </template>
      </el-table-column>
      <el-table-column label="创建时间" prop="createTime" align="center" width="180" />
      
      <!-- 右侧操作按钮 -->
      <el-table-column label="操作" align="center" width="230" class-name="small-padding fixed-width">
        <template slot-scope="{row}">
          <!-- <el-button type="primary" size="mini" @click="handleUpdate(row)">编辑</el-button>
          <el-button v-if="row.status==='1'" size="mini" type="success" @click="handleModifyStatus(row,'0')">禁用</el-button>
          <el-button v-else size="mini" @click="handleModifyStatus(row,'1')">启用</el-button> -->
          <el-button size="mini" type="warning" @click="handleRateSetting(row)">费率设置</el-button>
          <!-- <el-dropdown trigger="click" @command="(command) => handleCommand(command, row)">
            <el-button size="mini" type="info">
              更多<i class="el-icon-arrow-down el-icon--right"></i>
            </el-button>
            <el-dropdown-menu slot="dropdown">
              <el-dropdown-item command="detail">详情</el-dropdown-item>
              <el-dropdown-item command="delete" divided>删除</el-dropdown-item>
            </el-dropdown-menu>
          </el-dropdown> -->
        </template>
      </el-table-column>
    </el-table>

    <!-- 分页组件 -->
    <div class="pagination-container">
      <el-pagination
        background
        :current-page="listQuery.page"
        :page-sizes="[10, 20, 30, 50]"
        :page-size="listQuery.limit"
        layout="total, sizes, prev, pager, next, jumper"
        :total="total"
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
      />
    </div>

    <!-- 费率设置侧滑面板 -->
    <el-drawer
      :title="'费率设置 - ' + (currentCustomer ? currentCustomer.name : '')"
      :visible.sync="rateDialogVisible"
      direction="rtl"
      size="60%"
      :before-close="handleRateDialogClose"
    >
      <div class="drawer-content">
        <rate-info ref="rateInfo" />
        <!-- <div class="drawer-footer">
          <el-button @click="rateDialogVisible = false">取 消</el-button>
          <el-button type="primary" @click="handleRateSubmit">确 定</el-button>
        </div> -->
      </div>
    </el-drawer>
  </div>
</template>

<script>
export default {
  name: 'CustList',
  filters: {
    statusFilter(status) {
      const statusMap = {
        '1': 'success',
        '0': 'danger'
      }
      return statusMap[status]
    }
  },
  components: {
    RateInfo: () => import('./rateInfo.vue')
  },
  data() {
    return {
      list: [],
      total: 0,
      listLoading: true,
      multipleSelection: [],
      listQuery: {
        page: 1,
        limit: 20,
        name: undefined,
        phone: undefined,
        status: undefined
      },
      // 添加费率设置弹窗控制字段
      rateDialogVisible: false,
      currentCustomer: null // 添加当前客户数据存储
    }
  },
  created() {
    this.getList()
  },
  methods: {
    getList() {
      this.listLoading = true
      // 模拟获取数据
      setTimeout(() => {
        this.list = [
          {
            id: 1,
            name: '张三',
            phone: '13800138000',
            email: 'zhangsan@example.com',
            status: '1',
            createTime: '2023-01-01 12:00:00'
          },
          {
            id: 2,
            name: '李四',
            phone: '13900139000',
            email: 'lisi@example.com',
            status: '0',
            createTime: '2023-01-02 12:00:00'
          }
        ]
        this.total = 2
        this.listLoading = false
      }, 500)
    },
    handleFilter() {
      this.listQuery.page = 1
      this.getList()
    },
    handleReset() {
      this.listQuery = {
        page: 1,
        limit: 20,
        name: undefined,
        phone: undefined,
        status: undefined
      }
      this.getList()
    },
    handleCreate() {
      this.$message.success('点击了新增')
    },
    handleUpdate(row) {
      this.$message.success(`点击了编辑 ${row.name}`)
    },
    handleBatchDelete() {
      this.$confirm('确定删除选中的客户吗？', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.$message.success('删除成功')
      })
    },
    handleImport() {
      this.$message.info('点击了导入')
    },
    handleExport() {
      this.$message.info('点击了导出')
    },
    handleSelectionChange(val) {
      this.multipleSelection = val
    },
    handleSizeChange(val) {
      this.listQuery.limit = val
      this.listQuery.page = 1
      this.getList()
    },
    handleCurrentChange(val) {
      this.listQuery.page = val
      this.getList()
    },
    handleModifyStatus(row, status) {
      this.$message.success(`设置${row.name}状态为${status === '1' ? '启用' : '禁用'}`)
    },
    handleCommand(command, row) {
      switch (command) {
        case 'detail':
          this.$message.info(`查看${row.name}详情`)
          break
        case 'record':
          this.$message.info(`查看${row.name}预约记录`)
          break
        case 'delete':
          this.$confirm(`确定删除${row.name}吗？`, '提示', {
            confirmButtonText: '确定',
            cancelButtonText: '取消',
            type: 'warning'
          }).then(() => {
            this.$message.success('删除成功')
          })
          break
      }
    },
    // 添加处理费率设置的方法
    handleRateSetting(row) {
      // 打开费率设置侧滑面板
      this.currentCustomer = row
      this.rateDialogVisible = true
      this.$nextTick(() => {
        if (this.$refs.rateInfo && typeof this.$refs.rateInfo.initForm === 'function') {
          this.$refs.rateInfo.initForm(row)
        }
      })
    },
    // 添加处理费率设置侧滑面板关闭的方法
    handleRateDialogClose(done) {
      this.$confirm('确认关闭费率设置窗口？')
        .then(_ => {
          done();
        })
        .catch(_ => {});
    },
    // 添加处理费率提交的方法
    handleRateSubmit() {
      this.$message.success('费率设置已保存');
      this.rateDialogVisible = false;
    }
  }
}
</script>

<style scoped>
.app-container {
  padding: 20px;
  background-color: #f5f5f5;
}

.filter-container {
  margin-bottom: 20px;
  border-radius: 8px;
  box-shadow: 0 2px 12px 0 rgba(0, 0, 0, 0.1);
}

.filter-container ::v-deep .el-card__header {
  background-color: #f9f9f9;
  border-bottom: 1px solid #ebeef5;
  padding: 15px 20px;
}

.card-title {
  font-size: 16px;
  font-weight: 600;
  color: #333;
}

.filter-container ::v-deep .el-form-item {
  margin-bottom: 10px;
}

.filter-container ::v-deep .el-form-item__label {
  font-weight: 500;
}

.toolbar {
  margin-bottom: 20px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  background-color: #fff;
  padding: 15px 20px;
  border-radius: 4px;
  box-shadow: 0 2px 12px 0 rgba(0, 0, 0, 0.1);
}

.toolbar-right {
  display: flex;
  gap: 10px;
}

.toolbar ::v-deep .el-button {
  border-radius: 4px;
}

.pagination-container {
  margin-top: 20px;
  text-align: center;
  background-color: #fff;
  padding: 15px 20px;
  border-radius: 4px;
  box-shadow: 0 2px 12px 0 rgba(0, 0, 0, 0.1);
}

::v-deep .el-table {
  box-shadow: 0 2px 12px 0 rgba(0, 0, 0, 0.1);
  border-radius: 4px;
}

::v-deep .el-table th {
  background-color: #fafafa;
  font-weight: 600;
}

::v-deep .el-table .el-table__row:hover {
  background-color: #f5f7fa;
}

.drawer-content {
  padding: 20px;
  height: 100%;
  display: flex;
  flex-direction: column;
}

.drawer-footer {
  margin-top: auto;
  padding: 20px 0;
  display: flex;
  justify-content: flex-end;
}

.drawer-footer .el-button {
  margin-left: 10px;
}
</style>