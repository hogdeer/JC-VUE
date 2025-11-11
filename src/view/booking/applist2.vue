
<template>
  <div>
    <!-- 搜索表单 -->
    <el-card class="search-card">
      <el-form :inline="true" class="demo-form-inline" label-width="120px">
        <el-row :gutter="24">
          
          <el-col :span="6">
            <el-form-item label="买方名称">
              <el-input v-model="searchForm.riskCompName" placeholder="请选择买方名称"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="买方代码">
              <el-input v-model="searchForm.buyerNo" placeholder="请输入买方代码"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="买方PICCCODE">
              <el-input v-model="searchForm.picccode" placeholder="请输入买方PICCCODE"></el-input>
            </el-form-item>
          </el-col>
        <!-- </el-row>
        <el-row :gutter="20"> -->
          <el-col :span="6">
            <el-form-item label="有效/失效">
              <el-select v-model="searchForm.quotaState" placeholder="请选择有效/失效">
                <el-option label="有效" value="有效"></el-option>
                <el-option label="失效" value="失效"></el-option>
              </el-select>
            </el-form-item>
          </el-col>
          <!-- <el-col :span="8">
            <el-form-item label="开证行名称">
              <el-input v-model="searchForm.bankName" placeholder="请选择开证行名称"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item label="开证行SWIFT">
              <el-input v-model="searchForm.bankSwift" placeholder="请选择开证行SWIFT"></el-input>
            </el-form-item>
          </el-col> -->
        </el-row>
        <el-row :gutter="20">
          <el-col :span="24" style="text-align: center;">
            <el-button type="primary" @click="handleSearch">查询</el-button>
            <el-button @click="resetForm">重置</el-button>
          </el-col>
        </el-row>
      </el-form>
    </el-card>

    <!-- 数据表格 -->
    <el-card class="table-card">

      <el-table 
        :data="tableData" 
        border 
        style="width: 100%; ">
        <el-table-column prop="corpSerialNo" label="限额流水号" width="120" fixed></el-table-column>
             <el-table-column prop="riskCompName" label="买方名称" min-width="180">
          <template slot-scope="scope">
            <el-button type="text" @click="handleBuyerNameClick">{{ scope.row.riskCompName }}</el-button>
          </template>
        </el-table-column>
          <el-table-column prop="buyerNo" label="买方代码" width="80"></el-table-column>
        <el-table-column prop="appliAmount" label="申请额度" min-width="100"></el-table-column>
        <el-table-column prop="appliPaidTerm" label="申请信用期限(天)" width="140"></el-table-column>
        <el-table-column prop="quotaSum" label="批复额度" width="80"></el-table-column>
        <el-table-column prop="currency" label="币种" width="80"></el-table-column>
        <el-table-column prop="payMode" label="批复支付方式" min-width="140"></el-table-column>
        <el-table-column prop="paidTerm" label="批复信用期限(天)" width="140"></el-table-column>
        <!-- <el-table-column prop="bankName" label="开证行名称" min-width="140"></el-table-column>
        <el-table-column prop="bankSwift" label="SWIFT代码" min-width="140"></el-table-column> -->
        <el-table-column prop="auditDate" label="批复日期" width="120"></el-table-column>
        <el-table-column prop="effectDate" label="限额生效日"  width="120"></el-table-column>
        <el-table-column prop="lapseDate" label="限额失效日"  width="120"></el-table-column>
        <!-- <el-table-column prop="usedQuota" label="已用限额"></el-table-column>
        <el-table-column prop="remainingQuota" label="剩余限额"></el-table-column> -->
        <el-table-column prop="adCondition" label="特别条件" min-width="100"></el-table-column>
        <el-table-column prop="quotaState" label="限额状态" width="80" fixed="right">
          <template slot-scope="scope" >
            <el-tag 
              :type="getStatusTagType(scope.row.quotaState)"
              disable-transitions>
              {{ scope.row.quotaState }}
            </el-tag>
          </template>
        </el-table-column>
        <el-table-column label="操作" fixed="right" width="120">
          <template slot-scope="scope">
            <el-button 
              type="text" 
              size="small"
              @click="handleDetail(scope.row)">
              批复详情
            </el-button>
            <!-- <el-button 
              type="text" 
              size="small"
              @click="handleTest2Detail(scope.row)">
              详情
            </el-button> -->
          </template>
        </el-table-column>

      </el-table>

      <!-- 分页 -->
      <div class="pagination-container">
        <el-pagination
          background
          layout="total, prev, pager, next"
          :total="total"
          :page-size="pageSize"
          @current-change="handleCurrentChange"
        ></el-pagination>
      </div>
    </el-card>

    <!-- 添加drawer组件 -->
    <el-drawer
      title="限额批复详细信息"
      :visible.sync="drawerVisible"
      direction="rtl"
      size="60%"
      :wrapperClosable="false">
      <test3 v-if="drawerVisible" :corpSerialNo="currentCorpSerialNo"></test3>
    </el-drawer>
    <el-drawer
      title="出运信息详情"
      :visible.sync="test2DrawerVisible"
      direction="rtl"
      size="60%"
      :wrapperClosable="false">
      <test2 v-if="test2DrawerVisible" :formData="currentTest2Data"></test2>
    </el-drawer>
    <el-drawer
      title="买方信息"
      :visible.sync="testDrawerVisible"
      direction="rtl"
      size="60%"
      :wrapperClosable="false">
      <test v-if="testDrawerVisible" />
    </el-drawer>
  </div>
</template>

<script>
import Test3 from './test3.vue'
import Test2 from './test2.vue'
import Test from './test.vue'
export default {
  components: {
    Test3,
    Test2,
    Test
  },
  data() {
    return {
      searchForm: {
        buyerNo: '',
        riskCompName: '',
        picccode: '',
        quotaState: '',
        bankName: '',
        bankSwift: ''
      },
      tableData: [
        // 示例数据
        {
          corpSerialNo: 'Q001',
          riskCompName: '湖南省优禾皮具有限公司',
          buyerNo: 'DSSDFSD',
          appliAmount: '2,000,000$',
          appliPaidTerm: '90天',
          quotaSum: '95000$',
          currency: 'USD',
          payMode: 'OA',
          paidTerm: '90天',
          bankName: '中国银行',
          bankSwift: 'ICBKCNBJXXX',
          auditDate: '2023-07-01',
          effectDate: '2023-07-02',
          lapseDate: '2024-07-01',
          // usedQuota: '50000$',
          // remainingQuota: '45000$',
          adCondition: '无',
          quotaState: '有效'
        }
      ],
      total: 1,
      pageSize: 10,
      currentPage: 1,
      drawerVisible: false,
      currentCorpSerialNo: '',
      test2DrawerVisible: false,
      currentTest2Data: null,
      testDrawerVisible: false
    }
  },
  methods: {
    handleSearch() {
      // 处理查询逻辑
      console.log('搜索条件:', this.searchForm);
    },
    resetForm() {
      // 重置表单
      this.$refs.searchForm.resetFields();
    },
    handleCurrentChange(val) {
      this.currentPage = val;
      // 处理分页逻辑
      console.log(`当前页: ${val}`);
    },
    getStatusTagType(status) {
      switch (status) {
        case '有效':
          return 'success';  // 修改为绿色
        case '失效':
          return 'danger';
        default:
          return 'info';
      }
    },
    // 修改操作列按钮的点击事件
    handleDetail(row) {
      this.currentCorpSerialNo = row.corpSerialNo
      this.drawerVisible = true
    },
    handleTest2Detail(row) {
      this.currentTest2Data = row
      this.test2DrawerVisible = true
    },
    handleBuyerNameClick() {
      this.testDrawerVisible = true
    }
  }
};
</script>

<style scoped>
.search-card {
  margin-bottom: 20px;
}

.table-card {
  margin-top: 20px;
}

.demo-form-inline {
  margin-bottom: 20px;
}

.pagination-container {
  text-align: center;
  margin-top: 20px;
}

/* 新增样式确保表单元素统一宽度 */
.el-form-item__content {
  width: 100%;
}
.el-input,
.el-select {
  width: 100%;
}
.el-date-editor {
  width: 100%;
}
</style>