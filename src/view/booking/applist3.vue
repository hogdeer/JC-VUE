<template>
  <div>
    <!-- 搜索表单 -->
    <el-card class="search-card">
      <el-form :inline="true" class="demo-form-inline" label-width="120px">
        <el-row :gutter="20">
          
          <el-col :span="8">
            <el-form-item label="买方名称">
              <el-input v-model="searchForm.riskCompName" placeholder="请选择买方名称"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item label="买方代码">
              <el-input v-model="searchForm.buyerNo" placeholder="请输入买方代码"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item label="买方PICCCODE">
              <el-input v-model="searchForm.picccode" placeholder="请输入买方PICCCODE"></el-input>
            </el-form-item>
          </el-col>
        </el-row>
        <el-row :gutter="20">
          <el-col :span="8">
            <el-form-item label="有效/失效">
              <el-select v-model="searchForm.quotaState" placeholder="请选择有效/失效">
                <el-option label="有效" value="有效"></el-option>
                <el-option label="失效" value="失效"></el-option>
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item label="状态">
              <el-select v-model="searchForm.quotaState" placeholder="请选择">
                <el-option label="待受理" value="待受理"></el-option>
                <el-option label="申请中" value="申请中"></el-option>
                <el-option label="拒绝" value="拒绝"></el-option>
                <el-option label="通过" value="通过"></el-option>
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
            <!-- 新增导入按钮 -->
          </el-col>
        </el-row>
      </el-form>
    </el-card>

    <!-- 数据表格 -->
    <el-card class="table-card">
      <div style="margin-bottom: 10px;">
        <!-- 修改: 为批量导入按钮添加 Tooltip 说明 -->
        <el-tooltip content="根据限额流水号匹配数据，导入数据，将业务数据改为 通过状态" placement="top">
          <el-button type="success" @click="showImportDialog = true">批量导入</el-button>
        </el-tooltip>
        <!-- 新增批量导出按钮 -->
        <el-tooltip content="导出数据状态=已受理，并且需要将数据状态改为 申请中" placement="top">
          <el-button type="primary" @click="handleBatchExport" style="margin-left: 10px;">批量导出</el-button>
        </el-tooltip>
        <!-- 新增批量受理和批量拒绝按钮 -->
        <el-tooltip content="只能操作状态 = 待受理的数据" placement="top">
          <el-button type="primary" @click="handleBatchAccept" style="margin-left: 10px;" :disabled="selectedRows.length === 0">批量受理</el-button>
        </el-tooltip>
        <el-tooltip content="只能操作状态 = 待受理的数据" placement="top">
          <el-button type="danger" @click="showBatchRejectDialog = true" style="margin-left: 10px;" :disabled="selectedRows.length === 0">批量拒绝</el-button>
        </el-tooltip>
      </div>

      <el-table 
        :data="tableData" 
        border 
        style="width: 100%; "
        @selection-change="handleSelectionChange">
        // 添加批量选择列
        <el-table-column type="selection" width="55"></el-table-column>
        <el-table-column prop="corpSerialNo" label="限额流水号" width="120" fixed></el-table-column>
        <el-table-column prop="custName" label="客户名称" min-width="180" fixed></el-table-column>
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
        <el-table-column prop="quotaState" label="状态" width="80" fixed="right">
          <template slot-scope="scope" >
            <el-tag 
              :type="getStatusTagType(scope.row.quotaState)"
              disable-transitions>
              {{ scope.row.state }}
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
      <test3 v-if="drawerVisible" :corpSerialNo="currentCorpSerialNo" :show-buttons="true"></test3>
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
      <test v-if="testDrawerVisible" :show-buttons="true" />
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
    
    <!-- 批量拒绝对话框 -->
    <el-dialog title="批量拒绝" :visible.sync="showBatchRejectDialog" width="500px">
      <el-form :model="batchRejectForm" :rules="batchRejectRules" ref="batchRejectForm">
        <el-form-item label="拒绝理由" prop="reason">
          <el-input 
            type="textarea" 
            v-model="batchRejectForm.reason" 
            placeholder="请输入拒绝理由"
            maxlength="200"
            show-word-limit
            :autosize="{ minRows: 3, maxRows: 6 }">
          </el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="showBatchRejectDialog = false">取 消</el-button>
        <el-button type="primary" @click="handleBatchReject">确 定</el-button>
      </div>
    </el-dialog>
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
          custName: '宁波XXXXXXXXXXXXXXXXX有限公司',
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
          quotaState: '有效', 
          state: '待受理'
        }
      ],
      total: 1,
      pageSize: 10,
      currentPage: 1,
      drawerVisible: false,
      currentCorpSerialNo: '',
      test2DrawerVisible: false,
      currentTest2Data: null,
      testDrawerVisible: false,
      // 新增导入相关数据
      showImportDialog: false,
      fileList: [],
      uploadLoading: false,
      uploadAction: process.env.VUE_APP_BASE_API + '/quota/import', // 根据实际接口调整
      // 新增批量拒绝相关数据
      showBatchRejectDialog: false,
      batchRejectForm: {
        reason: ''
      },
      batchRejectRules: {
        reason: [
          { required: true, message: '请输入拒绝理由', trigger: 'blur' }
        ]
      },
      // 新增批量选择相关数据
      selectedRows: []
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
    },
    handleBatchExport() {
      // 导出状态为"已受理"的数据
      const acceptedData = this.tableData.filter(item => item.state === '已受理');
      
      if (acceptedData.length === 0) {
        this.$message.warning('没有状态为"已受理"的数据可以导出');
        return;
      }
      
      this.$message.success('开始导出数据...');
      
      // 模拟导出过程
      setTimeout(() => {
        // 创建一个虚拟的下载链接
        const link = document.createElement('a');
        link.href = 'data:text/plain;charset=utf-8,这是导出的数据示例';
        link.download = '批量导出数据.txt';
        link.click();
        
        // 将导出的数据状态从"已受理"改为"申请中"
        acceptedData.forEach(item => {
          item.state = '申请中';
        });
        
        this.$message.success(`导出成功，共${acceptedData.length}条记录，状态已更新为"申请中"`);
      }, 1000);
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
    exportData() {
      // 实际项目中，这里会调用后端API导出数据
      // 示例代码：
      // this.$http.post('/api/quota/export', this.searchForm)
      //   .then(response => {
      //     // 处理导出响应
      //     const blob = new Blob([response.data], { type: 'application/vnd.openxmlformats-officedocument.spreadsheetml.sheet' });
      //     const url = window.URL.createObjectURL(blob);
      //     const link = document.createElement('a');
      //     link.href = url;
      //     link.download = '限额批复数据.xlsx';
      //     link.click();
      //     window.URL.revokeObjectURL(url);
      //   })
      //   .catch(error => {
      //     this.$message.error('导出失败: ' + error.message);
      //   });
    },
    
    // 新增批量受理方法
    handleBatchAccept() {
      // 获取选中的行数据
      const selectedIds = this.selectedRows.map(row => row.corpSerialNo);
      this.$message.success(`批量受理操作已执行，共${selectedIds.length}条记录`);
      console.log('批量受理的ID列表:', selectedIds);
      // 实际项目中这里会调用后端接口处理批量受理逻辑
    },
    
    // 新增批量拒绝方法
    handleBatchReject() {
      this.$refs.batchRejectForm.validate((valid) => {
        if (valid) {
          // 获取选中的行数据
          const selectedIds = this.selectedRows.map(row => row.corpSerialNo);
          this.$message.success(`批量拒绝操作已执行，共${selectedIds.length}条记录，拒绝理由：${this.batchRejectForm.reason}`);
          this.showBatchRejectDialog = false;
          this.batchRejectForm.reason = '';
          console.log('批量拒绝的ID列表:', selectedIds);
          // 实际项目中这里会调用后端接口处理批量拒绝逻辑
        }
      });
    },
    
    // 新增批量选择方法
    handleSelectionChange(selection) {
      this.selectedRows = selection;
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