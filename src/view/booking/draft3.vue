<template>
  <div class="draft-container">
    
    <!-- 批量导入区域 -->
    <el-card v-show="currentStep === 1" class="import-card">
      <div slot="header" class="clearfix">
        <span>批量导入 - 第一步：选择文件</span>
      </div>
      <el-form>
        <el-form-item label="选择文件">
          <el-upload
            ref="upload"
            :action="uploadAction"
            :auto-upload="false"
            :on-change="handleFileChange"
            :on-remove="handleFileRemove"
            :file-list="fileList"
            :on-exceed="handleExceed"
            :limit="1"
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
      <div  class="dialog-footer">
        <el-button @click="cancelUpload">取 消</el-button>
        <el-button type="primary" @click="submitUpload" :loading="uploadLoading" :disabled="fileList.length === 0">下一步</el-button>
      </div>
    </el-card>

    <!-- 导入预览区域 -->
    <el-card v-show="currentStep === 2" class="preview-card">
      <div slot="header" class="clearfix">
        <span>批量导入 - 第二步：预览数据</span>
        <el-tag type="info" style="float: right; margin-top: 5px;">共 {{ previewData.length }} 条记录</el-tag>
      </div>
      <el-table 
        :data="paginatedData" 
        height="400" 
        border 
        v-loading="previewLoading"
        element-loading-text="数据加载中..."
        element-loading-spinner="el-icon-loading">
        <el-table-column prop="buyerName" label="买方名称" min-width="120"></el-table-column>
        <el-table-column prop="invoiceNo" label="发票号" min-width="120"></el-table-column>
        <el-table-column prop="shipmentDate" label="出货日" min-width="120"></el-table-column>
        <el-table-column prop="creditAmount" label="赊销金额" min-width="120"></el-table-column>
        <el-table-column prop="currency" label="币种" min-width="80"></el-table-column>
        <el-table-column prop="customsCode" label="海关商品代码" min-width="120"></el-table-column>
        <el-table-column prop="productQuantity" label="商品数量及单位" min-width="120"></el-table-column>
        <el-table-column prop="productCategory" label="商品类别" min-width="120"></el-table-column>
        <el-table-column prop="creditStartDate" label="信用起始日" min-width="120"></el-table-column>
        <el-table-column prop="creditTerm" label="信用期限（天）" min-width="120"></el-table-column>
        <el-table-column prop="paymentDate" label="回款日期" min-width="120"></el-table-column>
        <el-table-column prop="country" label="国家及地区" min-width="120"></el-table-column>
        <el-table-column prop="transportMode" label="运输方式" min-width="120"></el-table-column>
        <el-table-column prop="preShipment" label="是否出运前" min-width="120"></el-table-column>
        <el-table-column prop="remark" label="备注" min-width="120"></el-table-column>
      </el-table>
      
      
      
      <div  class="dialog-footer" style="margin-top: 20px;">
        <el-button @click="currentStep = 1" :disabled="previewLoading">上一步</el-button>
        <el-button type="primary" @click="confirmImport" :loading="confirmLoading">确认导入</el-button>
      </div>
    </el-card>
    
    <!-- 导入结果区域 -->
    <el-card v-show="currentStep === 3" class="result-card">
      <div slot="header" class="clearfix">
        <span>批量导入 - 第三步：导入结果</span>
      </div>
      <div class="result-content">
        <div class="circular-progress-container">
          <el-progress type="circle" :percentage="importProgress" status="success"></el-progress>
          <p class="progress-text">数据已成功导入系统</p>
          <div class="progress-buttons">
            <el-button type="primary" @click="resetImport">继续导入</el-button>
            <el-button @click="finishImport">完成</el-button>
          </div>
        </div>
      </div>
    </el-card>
  </div>
</template>

<script>
export default {
  data() {
    return {
      // 新增导入相关数据
      currentStep: 1, // 1: 选择文件, 2: 预览数据, 3: 导入结果
      fileList: [],
      uploadLoading: false,
      previewLoading: false,
      confirmLoading: false,
      uploadAction: process.env.VUE_APP_BASE_API + '/draft/import', // 根据实际接口调整
      previewData: [], // 预览数据
      // 添加分页相关数据
      pagination: {
        currentPage: 1,
        pageSize: 10,
        total: 0
      },
      // 添加导入进度数据
      importProgress: 100
    };
  },
  computed: {
    // 计算分页数据
    paginatedData() {
      const start = (this.pagination.currentPage - 1) * this.pagination.pageSize;
      const end = start + this.pagination.pageSize;
      return this.previewData.slice(start, end);
    }
  },
  methods: {
    
    // 新增导入相关方法
    handleFileChange(file, fileList) {
      this.fileList = fileList.slice(-1); // 只保留最后一个文件
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
    
    handleExceed() {
      this.$message.warning('只能上传一个文件，请先移除已选择的文件');
    },
    
    cancelUpload() {
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
        // 模拟预览数据
        this.previewData = [
          {
            buyerName: '测试买方1',
            invoiceNo: 'INV2023001',
            shipmentDate: '2023-06-01',
            creditAmount: '100000',
            currency: 'USD',
            customsCode: '84719000',
            productQuantity: '100台',
            productCategory: '电子产品',
            creditStartDate: '2023-06-01',
            creditTerm: '90',
            paymentDate: '2023-08-30',
            country: '美国',
            transportMode: '海运',
            preShipment: '否',
            remark: '测试数据1'
          },
          {
            buyerName: '测试买方2',
            invoiceNo: 'INV2023002',
            shipmentDate: '2023-06-05',
            creditAmount: '50000',
            currency: 'EUR',
            customsCode: '85176200',
            productQuantity: '50套',
            productCategory: '通讯设备',
            creditStartDate: '2023-06-05',
            creditTerm: '60',
            paymentDate: '2023-08-04',
            country: '德国',
            transportMode: '空运',
            preShipment: '是',
            remark: '测试数据2'
          }
          // 添加更多测试数据以展示分页效果
        ];
        this.pagination.total = this.previewData.length;
        this.currentStep = 2;
        this.$message.success('文件上传成功');
      }, 1500);
    },
    
    confirmImport() {
      this.confirmLoading = true;
      
      // 模拟导入过程
      setTimeout(() => {
        this.confirmLoading = false;
        this.$message.success('导入成功');
        this.currentStep = 3;
        // 实际项目中这里会调用后端接口处理导入逻辑
        // 刷新表格数据
        this.refreshTableData();
      }, 1500);
    },
    
    resetImport() {
      this.currentStep = 1;
      this.fileList = [];
      this.previewData = [];
    },
    
    finishImport() {
      this.resetImport();
      // 返回到主列表页面或其他操作
      this.$emit('import-finished');
    },
    
    refreshTableData() {
      // 模拟刷新数据
      console.log('刷新表格数据');
    },
    
    // 添加分页改变处理函数
    handlePageChange(page) {
      this.pagination.currentPage = page;
    },
    
    // 添加页面大小改变处理函数
    handleSizeChange(size) {
      this.pagination.pageSize = size;
      this.pagination.currentPage = 1;
    }
  }
};
</script>

<style scoped>
.draft-container {
  padding: 20px;
}

.import-card,
.preview-card,
.result-card {
  margin: 0 auto;
}

.dialog-footer {
  text-align: right;
  padding: 20px 0;
}

.result-content {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 300px;
}

.clearfix:before,
.clearfix:after {
  display: table;
  content: "";
}

.clearfix:after {
  clear: both;
}

/* 添加表格样式优化 */
.pagination-container {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-top: 20px;
}

.pagination-info {
  font-size: 14px;
  color: #606266;
}

/* 添加环形进度条样式 */
.circular-progress-container {
  text-align: center;
}

.progress-text {
  margin-top: 20px;
  font-size: 16px;
  color: #67C23A;
}

.progress-buttons {
  margin-top: 20px;
}
</style>