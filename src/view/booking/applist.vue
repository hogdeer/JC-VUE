<template>
  <div>
    <!-- 搜索表单 -->
    <el-card class="search-card">
      <el-form :inline="true" label-width="100px">
        <el-row :gutter="24">
          <el-col :span="6">
            <el-form-item label="买方名称">
              <el-input v-model="searchForm.riskCompName" placeholder="请输入买方名称"></el-input>
            </el-form-item>
          </el-col>
          <!-- <el-col :span="8">
            <el-form-item label="国家和地区">
              <el-select v-model="searchForm.countryOrArea" placeholder="请选择国家和地区" style="width: 100%">
                <el-option label="中国" value="CN"></el-option>
                <el-option label="美国" value="US"></el-option>
              </el-select>
            </el-form-item>
          </el-col> -->
          <el-col :span="6">
            <el-form-item label="状态">
              <el-select v-model="searchForm.dataStatus" placeholder="请选择状态" style="width: 100%">
                <el-option label="全部" value=""></el-option>
                <el-option label="待受理" value="待受理"></el-option>
                <el-option label="申请中" value="申请中"></el-option>
                <el-option label="通过" value="通过"></el-option>
                <el-option label="拒绝" value="拒绝"></el-option>
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item label="录入时间">
              <el-date-picker
                v-model="searchForm.entryDateRange"
                type="daterange"
                range-separator="至"
                start-placeholder="开始日期"
                end-placeholder="结束日期"
                style="width: 100%"
              ></el-date-picker>
            </el-form-item>
          </el-col>
           <el-col :span="4" style="text-align: center;">
            <el-form-item>
              <el-button type="primary" @click="handleSearch">查询</el-button>
              <el-button @click="resetForm">重置</el-button>
            </el-form-item>
          </el-col>
        </el-row>
      <!--  <el-row :gutter="20">
           <el-col :span="8">
            <el-form-item label="开证行名称">
              <el-input v-model="searchForm.bankName" placeholder="请输入开证行名称"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item label="开证行SWIFT">
              <el-input v-model="searchForm.bankSwift" placeholder="请输入开证行SWIFT"></el-input>
            </el-form-item>
          </el-col> 
          <el-col :span="8">
            <el-form-item label="支付方式">
              <el-select v-model="searchForm.payWay" placeholder="请选择支付方式" style="width: 100%">
                <el-option label="OA" value="OA"></el-option>
                <el-option label="LC" value="LC"></el-option>
              </el-select>
            </el-form-item>
          </el-col>
        </el-row>-->
        <!-- <el-row :gutter="20">
          <el-col :span="8">
            <el-form-item label="录入时间">
              <el-date-picker
                v-model="searchForm.entryDateRange"
                type="daterange"
                range-separator="至"
                start-placeholder="开始日期"
                end-placeholder="结束日期"
                style="width: 100%"
              ></el-date-picker>
            </el-form-item>
          </el-col> -->
          <!-- <el-col :span="8" style="text-align: center;">
            <el-form-item>
              <el-button type="primary" @click="handleSearch">查询</el-button>
              <el-button @click="resetForm">重置</el-button>
            </el-form-item>
          </el-col> 
        </el-row>-->
      </el-form>
    </el-card>

    <!-- 数据表格 -->
    <el-card class="table-card">
      <div style="margin-bottom: 20px;">
        <el-button type="primary" @click="showDrawer = true">新增</el-button>
      </div>
      <el-drawer
        title="新增限额申请"
        :visible.sync="showDrawer"
        size="60%"
        direction="rtl"
        :wrapperClosable="false"
        :show-header="true"
        >
        <div style="height: calc(100vh - 50px); overflow-y: auto;">
          <test @close="showDrawer = false"/>
        </div>
      </el-drawer>
      <div class="table-container">
        <el-table 
          :data="tableData" 
          border 
          style="width: 100%; min-width: 1500px"
          :header-cell-style="{background: '#f5f7fa', color: '#606266'}">
          <el-table-column prop="corpSerialNo" label="申请编号" width="150"></el-table-column>
          <el-table-column prop="riskCompName" label="买方名称" min-width="180">
            <template slot-scope="scope">
              <el-button type="text" @click="showDrawer = true">{{ scope.row.riskCompName }}</el-button>
            </template>
          </el-table-column>
          <el-table-column prop="countryOrArea" label="国家" width="150"></el-table-column>
          <el-table-column prop="historyBusiness" label="是否历史交易" width="120"></el-table-column>
          <el-table-column prop="appliAmount" label="申请限额" width="120"></el-table-column>
          <!-- <el-table-column prop="bankName" label="开证行名称" width="150"></el-table-column>
          <el-table-column prop="bankSwift" label="开证行SWIFT" width="150"></el-table-column> -->
          <el-table-column prop="payWay" label="支付方式" width="100"></el-table-column>
          <el-table-column prop="paidTerm" label="申请期限" width="100"></el-table-column>
          <el-table-column prop="createTime" label="申请时间" width="120"></el-table-column>
          <el-table-column prop="dataStatus" label="状态" width="100">
            <template slot-scope="scope">
              <el-tag 
                :type="getStatusTagType(scope.row.dataStatus)"
                disable-transitions>
                {{ scope.row.dataStatus }}
              </el-tag>
            </template>
          </el-table-column>
        </el-table>
      </div>

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
  </div>
</template>

<script>
import Test from './test'

export default {
  components: {
    Test
  },
  data() {
    return {
      showDrawer: false,
      searchForm: {
        riskCompName: '',
        dataStatus: '',
        bankName: '',
        bankSwift: '',
        payWay: '',
        entryDateRange: []
      },
      tableData: [
        // 示例数据
        {
          corpSerialNo: 'CI2389934593543',
          riskCompName: '湖南省优禾皮具有限公司',
          appliAmount: '2,000,000$',
          countryOrArea: '中国',
          historyBusiness: '是',
          payWay: 'OA',
          paidTerm: '90天',
          createTime: '2025-07-07',
          dataStatus: '已受理'
        },{
          corpSerialNo: 'CI2389934593544',
          riskCompName: '北京华联科技有限公司',
          appliAmount: '1,500,000$',
          countryOrArea: '中国',
          historyBusiness: '是',
          payWay: 'LC',
          paidTerm: '60天',
          createTime: '2025-07-06',
          dataStatus: '资信调查'
        },{
          corpSerialNo: 'CI2389934593545',
          riskCompName: '上海国际贸易有限公司',
          appliAmount: '3,200,000$',
          countryOrArea: '中国',
          historyBusiness: '是',
          payWay: 'OA',
          paidTerm: '120天',
          createTime: '2025-07-05',
          dataStatus: '已拒绝'
        },{
          corpSerialNo: 'CI2389934593546',
          riskCompName: '广州进出口贸易公司',
          appliAmount: '1,800,000$',
          countryOrArea: '中国',
          historyBusiness: '是',
          payWay: 'LC',
          paidTerm: '90天',
          createTime: '2025-07-04',
          dataStatus: '已批复'
        },
        // 更多示例数据...
      ],
      total: 6,
      pageSize: 10,
      currentPage: 1
    };
  },
  methods: {
    handleSearch() {
      // 处理查询逻辑
      console.log('搜索条件:', this.searchForm);
    },
    resetForm() {
      // 重置表单
      this.searchForm = {
        riskCompName: '',
        dataStatus: '',
        bankName: '',
        bankSwift: '',
        payWay: '',
        entryDateRange: []
      };
    },
    handleCurrentChange(val) {
      this.currentPage = val;
      // 处理分页逻辑
      console.log(`当前页: ${val}`);
    },
    getStatusTagType(status) {
      switch (status) {
        case '已受理':
          return 'warning';  // 修改为黄色
        case '资信调查':
          return 'primary';
        case '已拒绝':
          return 'danger';
        case '已批复':
          return 'success';  // 修改为绿色
        default:
          return 'info';
      }
    }
  }
};
</script>

<style scoped>
.search-card {
  margin-bottom: 20px;
  box-shadow: 0 2px 12px 0 rgba(0, 0, 0, 0.1);
}

.table-card {
  margin-top: 20px;
  box-shadow: 0 2px 12px 0 rgba(0, 0, 0, 0.1);
}

.table-container {
  margin-bottom: 20px;
}

.pagination-container {
  text-align: center;
  margin-top: 20px;
  padding: 20px 0;
}

.el-form--inline .el-form-item {
  display: inline-block;
  margin-right: 10px;
  vertical-align: top;
  width: 100%;
}

.el-form-item--medium .el-form-item__content, 
.el-form-item--medium .el-form-item__label {
  line-height: 36px;
  width: 100%;
}

.el-button + .el-button {
  margin-left: 10px;
}
</style>