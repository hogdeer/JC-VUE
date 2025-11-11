<template>
  <div class="bill-list">
    <!-- 筛选区域 -->
    <el-card shadow="never" class="filter-card">
      <el-form :inline="true" class="filter-container">
        <el-form-item label="状态：">
          <el-select v-model="statusFilter" placeholder="请选择">
            <el-option label="全部" value=""></el-option>
            <el-option label="待确认" value="pending"></el-option>
            <el-option label="已到账" value="arrived"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="账单月：">
          <el-date-picker v-model="monthFilter" type="month" placeholder="选择月份"></el-date-picker>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="handleSearch">查询</el-button>
        </el-form-item>
      </el-form>
    </el-card>

    <!-- 表格区域 -->
    <el-card shadow="never" class="table-card">
      <el-table :data="filteredBills" style="width: 100%">
        <el-table-column prop="customerName" label="客户名称" ></el-table-column>
        <el-table-column prop="billContent" label="账单内容" ></el-table-column>
        <el-table-column prop="totalAmount" label="账单总金额"  align="right"></el-table-column>
        <el-table-column prop="billTime" label="账单时间" ></el-table-column>
        <el-table-column prop="status" label="状态" width="100">
          <template slot-scope="scope">
            <el-tag :type="scope.row.status === 'pending' ? 'warning' : 'success'">{{ scope.row.statusText }}</el-tag>
          </template>
        </el-table-column>
        <el-table-column label="操作" width="120">
          <template slot-scope="scope">
            <el-button size="mini" type="primary" @click="handleDetail(scope.$index, scope.row)">详情</el-button>
          </template>
        </el-table-column>
      </el-table>

    </el-card>

    <!-- 分页区域 -->
    <div class="pagination-container">
      <el-pagination
        background
        layout="prev, pager, next"
        :current-page="currentPage"
        :page-size="pageSize"
        :total="filteredBills.length"
        @current-change="handleCurrentChange"
      ></el-pagination>
    </div>

    <!-- 账单详情抽屉 -->
    <el-drawer
    :append-to-body="true"
      title="账单详情"
      :visible.sync="detailDialogVisible"
      direction="rtl"
      size="70%"
      :wrapperClosable="false">
      <bill-info v-if="detailDialogVisible" :bill-data="currentBill"></bill-info>
    </el-drawer>


    
  </div>
</template>

<script>
import BillInfo from './billInfo.vue'

export default {
  components: {
    BillInfo
  },
  data() {
    return {
      statusFilter: '',
      monthFilter: '',
      bills: [
        {
          customerName: '客户A',
          billContent: '2023年1月份账单',
          totalAmount: 143.47,
          billTime: '2024-07-04 17:50:40',
          status: 'pending',
          statusText: '待确认'
        },
        {
          customerName: '客户B',
          billContent: '2023年2月份账单',
          totalAmount: 256.89,
          billTime: '2024-07-05 10:30:15',
          status: 'arrived',
          statusText: '已到账'
        },
        // 其他测试数据...
      ],
      currentPage: 1, // 当前页码
      pageSize: 10, // 每页显示条数
      detailDialogVisible: false,  // 弹窗显示状态
      currentBill: null           // 当前查看的账单数据
    };
  },
  computed: {
    filteredBills() {
      const filtered = this.bills.filter(bill => {
        const matchesStatus = !this.statusFilter || bill.status === this.statusFilter;
        const matchesMonth = !this.monthFilter || new Date(bill.billTime).getMonth() + 1 === new Date(this.monthFilter).getMonth() + 1;
        return matchesStatus && matchesMonth;
      });
      const start = (this.currentPage - 1) * this.pageSize;
      const end = start + this.pageSize;
      return filtered.slice(start, end);
    },
    totalBills() {
      return this.bills.length;
    }
  },
  methods: {
    handleSearch() {
      // 可以在这里添加搜索逻辑
    },
    handleDetail(index, row) {
      this.currentBill = row
      this.detailDialogVisible = true
    }
  }
};
</script>

<style scoped>
.bill-list {
  padding: 15px;        /* 修改：统一内边距 */
}

.filter-card {
  margin-bottom: 20px;
  padding: 15px 20px;   /* 修改：调整内边距 */
}

.table-card {
  padding: 15px 20px;   /* 修改：调整内边距 */
}

.el-table >>> .el-table__cell {
  padding: 12px 0;
  font-size: 13px;      /* 新增：统一表格字体大小 */
}

.el-form-item {
  margin-right: 20px;   /* 新增：调整表单项目间距 */
  margin-bottom: 10px;
}

.el-pagination {
  padding: 20px 0;      /* 新增：分页区域上下留白 */
}
</style>
