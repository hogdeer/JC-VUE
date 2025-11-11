<template>
  <div class="credit-quota-apply">
    <el-form 
      :model="form" 
      :rules="rules" 
      ref="form" 
      label-width="180px"
      label-position="right">
      
      <!-- 基础信息 -->
      <el-form-item label="发送方" prop="sender">
        <el-input v-model="form.sender" maxlength="600"></el-input>
      </el-form-item>
      
      <el-form-item label="接收方" prop="receiver">
        <el-input v-model="form.receiver" disabled value="PICC"></el-input>
      </el-form-item>
      
      <el-form-item label="发送时间" prop="creationDateTime">
        <el-date-picker v-model="form.creationDateTime" type="datetime"></el-date-picker>
      </el-form-item>
      
      <el-form-item label="接口类型" prop="messageType">
        <el-input v-model="form.messageType" disabled value="GOMS_QuotaApply"></el-input>
      </el-form-item>
      
      <!-- 买方信息 -->
      <el-form-item label="买方名称(英文大写)" prop="riskCompName">
        <el-input v-model="form.riskCompName" maxlength="600"></el-input>
      </el-form-item>
      
      <el-form-item label="买方国家和地区" prop="countryOrArea" v-if="!isDomesticTrade">
        <el-input v-model="form.countryOrArea" maxlength="50"></el-input>
      </el-form-item>
      
      <el-form-item label="买方地址" prop="riskCompAddress">
        <el-input v-model="form.riskCompAddress" maxlength="300"></el-input>
      </el-form-item>
      
      <!-- 限额信息 -->
      <el-form-item label="限额期限(天)" prop="paidTerm">
        <el-input-number v-model="form.paidTerm" :min="1" :max="99999"></el-input-number>
      </el-form-item>
      
      <el-form-item label="金额" prop="appliAmount">
        <el-input-number v-model="form.appliAmount" :precision="0" :min="0"></el-input-number>
      </el-form-item>
      
      <!-- 信用证相关信息 -->
      <div v-if="isLCPayment">
        <el-form-item label="信用证号" prop="creditNo">
          <el-input v-model="form.creditNo" maxlength="50"></el-input>
        </el-form-item>
        
        <el-form-item label="是否循环使用" prop="iscyCleUse">
          <el-radio-group v-model="form.iscyCleUse">
            <el-radio label="0">非循环</el-radio>
            <el-radio label="1">循环</el-radio>
          </el-radio-group>
        </el-form-item>
        
        <el-form-item label="开证行名称" prop="bankName">
          <el-input v-model="form.bankName" maxlength="150"></el-input>
        </el-form-item>
        
        <el-form-item label="开证行SWIFT" prop="bankSwift">
          <el-input v-model="form.bankSwift" maxlength="50"></el-input>
        </el-form-item>
        
        <el-form-item label="报告类型" prop="reportType">
          <el-radio-group v-model="form.reportType">
            <el-radio label="1">普通</el-radio>
            <el-radio label="2">加急</el-radio>
          </el-radio-group>
        </el-form-item>
      </div>
      
      <!-- 商品及交易信息 -->
      <el-form-item label="商品类别" prop="exportTrade">
        <el-input v-model="form.exportTrade" maxlength="200"></el-input>
      </el-form-item>
      
      <!-- 历史交易信息 -->
      <el-form-item label="历史交易存在性" prop="historyBusiness">
        <el-radio-group v-model="form.historyBusiness" @change="handleHistoryBusinessChange">
          <el-radio label="0">否</el-radio>
          <el-radio label="1">是</el-radio>
        </el-radio-group>
      </el-form-item>
      
      <div v-if="form.historyBusiness === '1'">
        <el-form-item label="最早成交年份" prop="earlyDealYear" v-if="!isDomesticTrade">
          <el-input v-model="form.earlyDealYear"></el-input>
        </el-form-item>
        
        <el-form-item label="买方付款表现" prop="riskPerformance">
          <el-select v-model="form.riskPerformance">
            <el-option v-for="item in paymentPerformanceOptions" :key="item.value" 
              :label="item.label" :value="item.value">
            </el-option>
          </el-select>
        </el-form-item>
        
        <el-form-item label="L/C交易金额" prop="transactionLc" v-if="!isDomesticTrade">
          <el-input-number v-model="form.transactionLc" :precision="0" :min="0"></el-input-number>
        </el-form-item>
      </div>
      
      <!-- 内贸专用字段 -->
      <div v-if="isDomesticTrade">
        <el-form-item label="交易商品" prop="bargainCommodity">
          <el-input v-model="form.bargainCommodity" maxlength="300"></el-input>
        </el-form-item>
        
        <el-form-item label="结算方式" prop="payType">
          <el-input v-model="form.payType" maxlength="200"></el-input>
        </el-form-item>
      </div>
      
      <el-form-item>
        <el-button type="primary" @click="submitForm">提交</el-button>
      </el-form-item>
    </el-form>
  </div>
</template>

<script>
export default {
  name: 'CreditQuotaApply',
  data() {
    return {
      form: {
        // 基础信息
        sender: '',
        receiver: 'PICC',
        creationDateTime: new Date(),
        messageType: 'GOMS_QuotaApply',
        messageStatus: 0,
        version: 1,
        documentId: '',
        usageIndicator: 'P',
        
        // 买方信息
        riskCompName: '',
        countryOrArea: '',
        riskCompAddress: '',
        province: '',
        riskPhone: '',
        riskMark: '',
        
        // 限额信息
        paidTerm: 0,
        appliAmount: 0,
        creditNo: '',
        reportType: '1',
        
        // 信用证信息
        iscyCleUse: '0',
        bankName: '',
        bankCountry: '',
        bankSwift: '',
        bankAddress: '',
        conBankName: '',
        conBankCountry: '',
        conBankSwift: '',
        conBankAddress: '',
        conReportType: '',
        
        // 商品及交易信息
        exportTrade: '',
        exportTradeInput: '',
        transType: '',
        historyBusiness: '0',
        
        // 历史交易信息
        earlyDealYear: '',
        bankPerformance: '',
        riskPerformance: '',
        transactionLc: '',
        transactionDp: '',
        transactionDa: '',
        isDefaultThis: '',
        defaultAmount: 0,
        defaultDate: 0,
        
        // 内贸专用
        bargainCommodity: '',
        intendingAmount: 0,
        totalAmount: 0,
        yjTotalAmount: 0,
        compactPayWayCondi: '',
        payType: '',
        maxSingleAmount: 0,
        earlyCooperateYear: 0,
        sntotaltransactionAmount: 0,
        currentlytotalAmount: 0,
        happenDate: '',
        startYear: '',
        
        // 系统信息
        corpserialNo: '',
        payWay: '',
        needTranslation: '',
        isVouch: '',
        bussinessNo: '',
        insuredPiccCode: ''
      },
      rules: {
        sender: [{ required: true, message: '请输入发送方', trigger: 'blur' }],
        riskCompName: [
          { required: true, message: '请输入买方名称', trigger: 'blur' },
          { max: 600, message: '长度不能超过600个字符', trigger: 'blur' }
        ],
        paidTerm: [
          { required: true, message: '请输入限额期限', trigger: 'blur' },
          { type: 'number', min: 1, message: '必须大于0', trigger: 'blur' }
        ],
        appliAmount: [
          { required: true, message: '请输入金额', trigger: 'blur' },
          { type: 'number', min: 0, message: '必须大于等于0', trigger: 'blur' }
        ],
        historyBusiness: [{ required: true, message: '请选择', trigger: 'change' }],
        riskPerformance: [{ required: true, message: '请选择付款表现', trigger: 'change' }],
        exportTrade: [{ required: true, message: '请输入商品类别', trigger: 'blur' }]
      },
      // ... existing options and flags ...
    }
  },
  // ... existing methods ...
}
</script>