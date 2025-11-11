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
          <!-- <el-col :span="8">
            <el-form-item label="开证行名称">
              <el-input v-model="searchForm.bankName" placeholder="请输入开证行名称"></el-input>
            </el-form-item>
          </el-col> -->
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
          <!-- <el-col :span="8">
            <el-form-item label="申报人">
              <el-select v-model="searchForm.declarant" placeholder="请选择申报人">
                <el-option label="全部" value=""></el-option>
              </el-select>
            </el-form-item>
          </el-col> -->
        <el-row :gutter="20">
          <el-col :span="8">
            <el-form-item label="发票号">
              <el-input v-model="searchForm.invoiceNo" placeholder="请输入发票号"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item label="币种">
              <el-select v-model="searchForm.currency" placeholder="请选择币种"></el-select>
            </el-form-item>

          </el-col>
          <el-col :span="8">
            <el-button type="primary" @click="handleSearch">查询</el-button>
          <el-button @click="resetForm">重置</el-button>
          </el-col>
        </el-row>
        <el-row :gutter="20">
          <!-- <el-col :span="8">
            <el-form-item label="赊销申报日期">
              <el-date-picker
                v-model="searchForm.declarationDateRange"
                type="daterange"
                range-separator="至"
                start-placeholder="开始日期"
                end-placeholder="结束日期"
              ></el-date-picker>
            </el-form-item>
          </el-col> -->
          
          
        </el-row>
        
        <!-- <el-row :gutter="20" style="text-align: center;">
          <el-button type="primary" @click="handleSearch">查询</el-button>
          <el-button @click="resetForm">重置</el-button>
         
        </el-row> -->
      </el-form>
    </el-card>

    <!-- 表格区域 -->
    <el-card shadow="never" class="table-card">
       <el-button type="success" @click="handleAdd">新增</el-button>
       <!-- 新增批量导入按钮 -->
      <el-table :data="tableData" style="width: 100%">
        <!-- <el-table-column prop="bankName" label="开证行/保兑行SWIFT" width="180"></el-table-column> -->
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
        <!-- <el-table-column prop="duePayment" label="收款状态" width="120"></el-table-column> -->
        <el-table-column prop="processStatus" label="数据状态" width="120"></el-table-column>
        <el-table-column prop="declarationDate" label="申请日期" width="120"></el-table-column>
        <el-table-column prop="remarkInfo" label="备注信息" width="120"></el-table-column>
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

    <!-- 导入预览对话框 -->
    <el-dialog title="导入预览" :visible.sync="showPreviewDialog" width="80%">
      <el-table :data="previewData" height="400" border>
        <el-table-column prop="invoiceNo" label="发票号" width="120"></el-table-column>
        <el-table-column prop="buyerName" label="买方名称" width="180"></el-table-column>
        <el-table-column prop="currency" label="币种" width="80"></el-table-column>
        <el-table-column prop="country" label="国家和地区" width="120"></el-table-column>
        <el-table-column prop="quotaCalculationStandard" label="费率计算标准" width="180"></el-table-column>
        <el-table-column prop="quotaAmount" label="赊销金额" width="120"></el-table-column>
        <el-table-column prop="rate" label="费率%" width="80"></el-table-column>
        <el-table-column prop="premium" label="保费" width="120"></el-table-column>
        <el-table-column prop="recoveryAmount" label="回收金额" width="120"></el-table-column>
        <el-table-column prop="creditStartDate" label="信用期限起始日" width="120"></el-table-column>
        <el-table-column prop="shipmentDate" label="出货日" width="120"></el-table-column>
        <el-table-column prop="duePaymentDate" label="应收款日" width="120"></el-table-column>
      </el-table>
      <div slot="footer" class="dialog-footer">
        <el-button @click="showPreviewDialog = false">取 消</el-button>
        <el-button type="primary" @click="confirmImport">确认导入</el-button>
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
      showPreviewDialog: false,
      fileList: [],
      uploadLoading: false,
      uploadAction: process.env.VUE_APP_BASE_API + '/draft/import', // 根据实际接口调整
      previewData: [] // 预览数据
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
      
      // 模拟上传和预览过程
      setTimeout(() => {
        this.uploadLoading = false;
        this.showImportDialog = false;
        
        // 模拟预览数据
        this.previewData = [
          {
            invoiceNo: 'INV003',
            buyerName: '客户C',
            currency: 'USD',
            country: '德国',
            quotaCalculationStandard: 'OA',
            quotaAmount: '15000$',
            rate: '1.5%',
            premium: '150$',
            recoveryAmount: '13500$',
            creditStartDate: '2023-09-01',
            shipmentDate: '2023-09-02',
            duePaymentDate: '2023-09-23'
          },
          {
            invoiceNo: 'INV004',
            buyerName: '客户D',
            currency: 'EUR',
            country: '法国',
            quotaCalculationStandard: 'DA',
            quotaAmount: '25000$',
            rate: '2.5%',
            premium: '250$',
            recoveryAmount: '22500$',
            creditStartDate: '2023-10-01',
            shipmentDate: '2023-10-02',
            duePaymentDate: '2023-10-23'
          }
        ];
        
        this.showPreviewDialog = true;
      }, 2000);
    },
    
    confirmImport() {
      this.$message.success('导入成功');
      this.showPreviewDialog = false;
      // 实际项目中这里会调用后端接口处理导入逻辑
      // 刷新表格数据
      this.refreshTableData();
    },
    
    refreshTableData() {
      // 模拟刷新数据
      console.log('刷新表格数据');
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