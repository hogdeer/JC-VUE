<template>
  <div class="app-container">
    <h2 class="page-title">限额批复详细信息</h2>

    <div class="info-card">
      <el-form label-width="120px">
        <!-- 第一行 -->
        <el-row :gutter="24">
          <el-col :span="8">
            <el-form-item label="限额号">
              <el-input v-model="detail.limitNo" readonly></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item label="买方名称(英文)">
              <el-input v-model="detail.buyerNameEn" readonly></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item label="买方PICCCODE">
              <el-input v-model="detail.buyerPiccCode" readonly></el-input>
            </el-form-item>
          </el-col>
        </el-row>

        <!-- 第二行 -->
        <el-row :gutter="24">
          <!-- <el-col :span="8">
            <el-form-item label="开证行SWIFT码">
              <el-input v-model="detail.swiftCode" placeholder="请输入开证行SWIFT码"></el-input>
            </el-form-item>
          </el-col> -->
          <el-col :span="8">
            <el-form-item label="申请额度">
              <el-input v-model="detail.applyAmount" readonly></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item label="申请支付方式">
              <el-input v-model="detail.applyPaymentMethod" readonly></el-input>
            </el-form-item>
          </el-col>
        </el-row>

        <!-- 第三行 -->
        <el-row :gutter="24">
          <el-col :span="8">
            <el-form-item label="申请信用期限">
              <el-input :value="`${detail.applyCreditTerm} 天`" readonly></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item label="批复额度">
              <el-input v-model="detail.approvedAmount" readonly></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item label="批复支付方式">
              <el-input v-model="detail.approvedPaymentMethod" readonly></el-input>
            </el-form-item>
          </el-col>
        </el-row>

        <!-- 第四行 -->
        <el-row :gutter="24">
          <el-col :span="8">
            <el-form-item label="批复信用期限">
              <el-input :value="`${detail.approvedCreditTerm} 天`" readonly></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item label="批复日">
              <el-input v-model="detail.approvalDate" readonly></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item label="限额生效日">
              <el-input v-model="detail.effectiveDate" readonly></el-input>
            </el-form-item>
          </el-col>
        </el-row>

        <!-- 第五行 -->
        <el-row :gutter="24">
          <el-col :span="8">
            <el-form-item label="限额失效日">
              <el-input v-model="detail.expiryDate" readonly></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item label="拒收赔偿比例">
              <el-input :value="`${detail.refusalCompensationRatio}%`" readonly></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item label="其它商业风险赔偿比例">
              <el-input :value="`${detail.otherRiskCompensationRatio}%`" readonly></el-input>
            </el-form-item>
          </el-col>
        </el-row>

        <!-- 特别条件 -->
        <el-row :gutter="24">
          <el-col :span="24">
            <el-form-item label="特别条件">
              <el-input 
                type="textarea" 
                v-model="detail.remark" 
                placeholder="请输入特别条件"
                :rows="3"
                resize="none"
                style="width: 100%;" readonly>
              </el-input>
            </el-form-item>
          </el-col>
        </el-row>

        <!-- 限额审批说明 -->
        <el-row :gutter="20">
          <el-col :span="24">
            <el-form-item label="限额审批说明">
              <el-input 
                type="textarea" 
                v-model="detail.approvalRemark" 
                placeholder="请输入限额审批说明"
                :rows="3"
                resize="none"
                style="width: 100%;" readonly>
              </el-input>
            </el-form-item>
          </el-col>
        </el-row>
      </el-form>
    </div>
    
    <!-- 添加保存按钮 -->
    <div v-if="showButtons" class="button-container">
      <el-button type="primary" @click="saveData">保存</el-button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'LimitApprovalDetail',
  props: {
    corpSerialNo: {
      type: String,
      default: ''
    },
    // 添加控制按钮显示的prop
    showButtons: {
      type: Boolean,
      default: false
    }
  },
  data() {
    return {
      detail: {
        limitNo: '1195833',
        buyerNameEn: '烟台杰瑞石油服务集团股份有限公司',
        swiftCode: '',
        applyAmount: '700,000.00',
        applyPaymentMethod: 'OA',
        applyCreditTerm: '90',
        approvedAmount: '700,000.00',
        approvedPaymentMethod: 'OA',
        approvedCreditTerm: '90',
        approvalDate: '2025-07-08',
        effectiveDate: '2025-07-02',
        expiryDate: '2026-06-29',
        refusalCompensationRatio: '90',
        otherRiskCompensationRatio: '90',
        remark: ''
      },
      specialConditions: []
    }
  },
  methods: {
    saveData() {
      this.$emit('save', this.detail);
    }
  }
}
</script>

<style lang="scss" scoped>
.page-title {
  text-align: center;
  margin-bottom: 30px;
  color: #333;
}

.info-card {
  background: #fff;
  padding: 20px;
  margin-bottom: 20px;
  border-radius: 4px;
  box-shadow: 0 1px 4px rgba(0, 0, 0, 0.1);

  .el-form-item {
    margin-bottom: 20px;
  }

  .el-input, .el-textarea {
    width: 100%;
  }

  .el-table {
    margin-top: 20px;
  }
}

.special-conditions {
  background: #fff;
  padding: 20px;
  border-radius: 4px;
  box-shadow: 0 1px 4px rgba(0, 0, 0, 0.1);
  
  h3 {
    margin-bottom: 20px;
    color: #333;
  }
}

// 添加按钮容器样式
.button-container {
  text-align: center;
  padding: 20px 0;
}
</style>