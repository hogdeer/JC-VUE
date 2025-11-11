<template>
  <div class="draft-container">
    <!-- 筛选区域 -->
    <el-card shadow="never" class="filter-card">
      <el-form :inline="true" class="filter-container" label-width="120px">
        <el-row :gutter="20">
          
          <el-col :span="8">
            <el-form-item label="申报单号">
              <el-input v-model="searchForm.declarationNo" placeholder="请输入申报单号"></el-input>
            </el-form-item>
          </el-col>

          <el-col :span="8">
            <el-form-item label="买方名称">
              <el-input v-model="searchForm.buyerName" placeholder="请输入买方名称"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item label="开证行名称">
              <el-input v-model="searchForm.bankName" placeholder="请输入开证行名称"></el-input>
            </el-form-item>
          </el-col>
          <!-- <el-col :span="8">
            <el-form-item label="回款状态">
              <el-select v-model="searchForm.paymentStatus" placeholder="请选择回款状态">
                <el-option label="全部" value=""></el-option>
              </el-select>
            </el-form-item>
          </el-col> -->
        </el-row>
        <el-row :gutter="20">
           <el-col :span="8">
            <el-form-item label="买方国家和地区">
              <el-select v-model="searchForm.buyerCountry" placeholder="请选择买方国家和地区">
                <el-option label="请选择" value=""></el-option>
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item label="处理状态">
              <el-select v-model="searchForm.processStatus" placeholder="请选择处理状态">
                <el-option label="全部" value=""></el-option>
              </el-select>
            </el-form-item>
          </el-col>
          <!-- <el-col :span="8">
            <el-form-item label="申报人">
              <el-select v-model="searchForm.declarant" placeholder="请选择申报人">
                <el-option label="全部" value=""></el-option>
              </el-select>
            </el-form-item>
          </el-col> -->
          <el-col :span="8">
            <el-form-item label="发票号">
              <el-input v-model="searchForm.invoiceNo" placeholder="请输入发票号"></el-input>
            </el-form-item>
          </el-col>
        </el-row>
        <el-row :gutter="20">
          <el-col :span="8">
            <el-form-item label="赊销申报日期">
              <el-date-picker
                v-model="searchForm.declarationDateRange"
                type="daterange"
                range-separator="至"
                start-placeholder="开始日期"
                end-placeholder="结束日期"
              ></el-date-picker>
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item label="出货日">
              <el-date-picker
                v-model="searchForm.shipmentDateRange"
                type="daterange"
                range-separator="至"
                start-placeholder="开始日期"
                end-placeholder="结束日期"
              ></el-date-picker>
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item label="信用期限起始日">
              <el-date-picker
                v-model="searchForm.creditStartDateRange"
                type="daterange"
                range-separator="至"
                start-placeholder="开始日期"
                end-placeholder="结束日期"
              ></el-date-picker>
            </el-form-item>
          </el-col>
        </el-row>
        
        <el-row :gutter="20" style="text-align: center;">
          <el-button type="primary" @click="handleSearch">查询</el-button>
          <el-button @click="resetForm">重置</el-button>
        </el-row>
      </el-form>
    </el-card>

    <!-- 表格区域 -->
    <el-card shadow="never" class="table-card">
       <div style="margin-bottom: 10px;">
        <el-button type="success" @click="showImportDialog = true">批量导入</el-button>
        <!-- 新增批量导出按钮 -->
        <el-button type="primary" @click="handleBatchExport" style="margin-left: 10px;">批量导出</el-button>
      </div>
      <el-table :data="tableData" style="width: 100%">
    
        <el-table-column prop="invoiceNo" label="发票号" width="120"></el-table-column>
        <el-table-column prop="buyerName" label="买方名称" width="180"></el-table-column>
        <el-table-column prop="currency" label="币种" width="80"></el-table-column>
        <el-table-column prop="country" label="国家和地区" width="120"></el-table-column>

        <el-table-column prop="quotaAmount" label="申报金额" width="120"></el-table-column>
        <el-table-column prop="rate" label="费率%" width="80"></el-table-column>
        <el-table-column prop="premium" label="保费" width="120"></el-table-column>
       
        <el-table-column prop="creditStartDate" label="信用期限起始日" width="120"></el-table-column>
        <el-table-column prop="shipmentDate" label="出货日" width="120"></el-table-column>
        <el-table-column prop="duePaymentDate" label="应收款日" width="120"></el-table-column>

        <el-table-column prop="processStatus" label="数据状态" width="120"></el-table-column>
        <el-table-column prop="declarationDate" label="申请日期" width="120"></el-table-column>
        <el-table-column prop="remarkInfo" label="备注信息" width="120"></el-table-column>
        <el-table-column label="操作" width="120" fixed="right">
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
        :total="tableData.length"
        @current-change="handleCurrentChange"
      ></el-pagination>
    </div>

    <!-- 添加抽屉组件 -->
    <el-drawer
      title="出运信息详情"
      :visible.sync="test2DrawerVisible"
      direction="rtl"
      size="60%"
      :wrapperClosable="false">
      <test2 v-if="test2DrawerVisible" :formData="currentTest2Data"></test2>
    </el-drawer>

    <!-- 新增抽屉组件 -->
    <el-drawer
      title="新增出运信息"
      :visible.sync="showAddDrawer"
      direction="rtl"
      size="60%"
      :wrapperClosable="false">
      <test2 v-if="showAddDrawer" :formData="currentTest2Data"></test2>
    </el-drawer>

    <!-- 批量导入对话框 -->
    <el-dialog title="批量导入" :visible.sync="showImportDialog" width="500px">
      <el-form>
        <el-form-item label="选择文件">
          <el-upload
            ref="upload"
            :action="uploadAction"
            :auto-upload="false"
            :on-change="handleFileChange"
            :on-remove="handleFileRemove"
            :file-list="fileList"
            accept=".xlsx,.xls,.csv"
            drag>
            <i class="el-icon-upload"></i>
            <div class="el-upload__text">将文件拖到此处，或<em>点击上传</em></div>
            <div class="el-upload__tip" slot="tip">
              只能上传xlsx、xls、csv文件，且不超过10MB
            </div>
          </el-upload>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="cancelUpload">取 消</el-button>
        <el-button type="primary" @click="submitUpload" :loading="uploadLoading">确 定</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
import Test2 from './test2.vue'

export default {
  components: {
    Test2
  },
  data() {
    return {
      searchForm: {
        policyNo: '',
        declarationNo: '',
        insuranceType: '',
        buyerName: '',
        bankName: '',
        paymentStatus: '',
        processStatus: '',
        declarant: '',
        invoiceNo: '',
        declarationDateRange: [],
        shipmentDateRange: [],
        creditStartDateRange: [],
        buyerCountry: '',
        quotaType: ''
      },
      tableData: [
        // 示例数据
        {
          bankName: '中国银行',
          invoiceNo: 'INV001',
          buyerName: '客户A',
          accountNo: 'ACC001',
          currency: 'USD',
          country: '中国',
          quotaCalculationStandard: 'OA',
          quotaAmount: '10000$',
          rate: '1%',
          premium: '100$',
          recoveryAmount: '9000$',
          creditStartDate: '2023-07-01',
          shipmentDate: '2023-07-02',
          duePaymentDate: '2023-07-23',
          duePayment: '已收款',
          processStatus: '通过',
          declarationDate: '2023-07-01',
          remarkInfo: '无'
        },
        {
          bankName: '建设银行',
          invoiceNo: 'INV002',
          buyerName: '客户B',
          accountNo: 'ACC002',
          currency: 'CNY',
          country: '美国',
          quotaCalculationStandard: 'DA',
          quotaAmount: '20000$',
          rate: '2%',
          premium: '200$',
          recoveryAmount: '18000$',
          creditStartDate: '2023-08-01',
          shipmentDate: '2023-08-02',
          duePaymentDate: '2023-08-23',
          duePayment: '未收款',
          processStatus: '申请中',
          declarationDate: '2023-08-01',
          remarkInfo: '需进一步确认'
        }
        // 其他测试数据...
      ],
      currentPage: 1,
      pageSize: 10,
      test2DrawerVisible: false,
      currentTest2Data: null,
      showAddDrawer: false,
      // 新增导入相关数据
      showImportDialog: false,
      fileList: [],
      uploadLoading: false,
      uploadAction: process.env.VUE_APP_BASE_API + '/quota/import' // 根据实际接口调整
    };
  },
  methods: {
    handleSearch() {
      console.log('搜索条件:', this.searchForm);
    },
    resetForm() {
      this.$refs.searchForm.resetFields();
    },
    handleCurrentChange(val) {
      this.currentPage = val;
      console.log(`当前页: ${val}`);
    },
    handleDetail(index, row) {
      this.currentTest2Data = row
      this.test2DrawerVisible = true
    },
    handleAdd() {
      this.currentTest2Data = null
      this.showAddDrawer = true
    },
    
    // 新增导入相关方法
    handleFileChange(file, fileList) {
      this.fileList = fileList;
      // 文件大小限制
      const isLt10M = file.size / 1024 / 1024 < 10;
      if (!isLt10M) {
        this.$message.error('上传文件大小不能超过 10MB!');
        this.fileList = [];
        return false;
      }
      return true;
    },
    
    handleFileRemove(file, fileList) {
      this.fileList = fileList;
    },
    
    cancelUpload() {
      this.showImportDialog = false;
      this.fileList = [];
      this.uploadLoading = false;
    },
    
    submitUpload() {
      if (this.fileList.length === 0) {
        this.$message.warning('请选择要上传的文件');
        return;
      }
      
      this.uploadLoading = true;
      
      // 模拟上传过程
      setTimeout(() => {
        this.uploadLoading = false;
        this.$message.success('导入成功');
        this.cancelUpload();
        // 刷新表格数据
        this.refreshTableData();
      }, 2000);
    },
    
    refreshTableData() {
      // 模拟刷新数据
      console.log('刷新表格数据');
    },
    
    // 新增导出相关方法
    handleBatchExport() {
      // 模拟导出功能
      this.$message.success('开始导出数据...');
      
      // 这里应该是实际的导出逻辑，例如：
      // 1. 调用后端接口获取导出数据
      // 2. 创建并下载文件
      
      // 模拟导出过程
      setTimeout(() => {
        const link = document.createElement('a');
        link.href = 'data:text/plain;charset=utf-8,这是导出的数据示例';
        link.download = '批量导出数据.txt';
        link.click();
        this.$message.success('导出成功');
      }, 1000);
    }
  }
};
</script>

<style scoped>
.draft-container {
  padding: 15px;
}

.filter-card {
  margin-bottom: 20px;
}

.table-card {
  margin-top: 20px;
}

.pagination-container {
  text-align: center;
  margin-top: 20px;
}

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
</file1>