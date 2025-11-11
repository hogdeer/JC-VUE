<template>
  <div class="draft-container">
    
    <!-- 批量导入区域 -->
    <el-card v-if="currentStep === 1" class="import-card">
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
      <div slot="footer" class="dialog-footer">
        <el-button @click="cancelUpload">取 消</el-button>
        <el-button type="primary" @click="submitUpload" :loading="uploadLoading" :disabled="fileList.length === 0">下一步</el-button>
      </div>
    </el-card>

    <!-- 导入预览区域 -->
    <el-card v-if="currentStep === 2" class="preview-card">
      <div slot="header" class="clearfix">
        <span>批量导入 - 第二步：预览数据</span>
        <el-tag type="info" style="float: right; margin-top: 5px;">共 {{ previewData.length }} 条记录</el-tag>
      </div>
      <el-table 
        :data="previewData" 
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
      <div slot="footer" class="dialog-footer" style="margin-top: 20px;">
        <el-button @click="currentStep = 1" :disabled="previewLoading">上一步</el-button>
        <el-button type="primary" @click="confirmImport" :loading="confirmLoading">确认导入</el-button>
      </div>
    </el-card>
    
    <!-- 导入结果区域 -->
    <el-card v-if="currentStep === 3" class="result-card">
      <div slot="header" class="clearfix">
        <span>批量导入 - 第三步：导入结果</span>
      </div>
      <div class="result-content">
        <el-result 
          icon="success" 
          title="导入成功" 
          subTitle="数据已成功导入系统">
          <template slot="extra">
            <el-button type="primary" @click="resetImport">继续导入</el-button>
            <el-button @click="finishImport">完成</el-button>
          </template>
        </el-result>
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
      previewData: [] // 预览数据
    };
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
      
      // 使用element-ui的submit方法上传文件
      this.$refs.upload.submit();
    },
    
    // 添加一个新的方法处理上传成功的回调
    handleUploadSuccess(response, file, fileList) {
      this.uploadLoading = false;
      
      // 根据实际接口返回的数据结构进行调整
      if (response.code === 200) {
        // 假设后端返回预览数据在response.data中
        this.previewData = response.data;
        this.currentStep = 2;
        this.$message.success('文件上传成功');
      } else {
        this.$message.error(response.message || '上传失败');
      }
    },
    
    // 添加处理上传失败的回调
    handleUploadError(err, file, fileList) {
      this.uploadLoading = false;
      this.$message.error('文件上传失败');
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
  max-width: 1200px;
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
</style>