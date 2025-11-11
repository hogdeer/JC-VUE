<template>
  <div class="app-container">
    <el-form 
      ref="creditSaleForm" 
      :model="form" 
      :rules="rules" 
      label-width="150px"
      label-position="right"
    >
      <!-- 基本信息区域 -->
      <el-card class="box-card" style="margin-bottom: 20px;">
        <div slot="header" class="clearfix">
          <span>出运信息</span>
        </div>
        <el-row :gutter="20">
          <!-- <el-col :span="8">
            <el-form-item label="业务流水号" prop="corpSerialNo">
              <el-input v-model="form.corpSerialNo" placeholder="请输入业务流水号" style="width: 100%" />
            </el-form-item>
          </el-col> -->
          <el-col :span="8">
            <el-form-item label="买方名称" prop="riskPiccCode">
              <el-select v-model="form.riskPiccCode" placeholder="请选择买方" filterable style="width: 100%">
                <el-option 
                  v-for="item in piccCodeOptions" 
                  :key="item.value" 
                  :label="item.label" 
                  :value="item.value"
                />
              </el-select>
            </el-form-item>
          </el-col>

          <el-col :span="8">
            <el-form-item label="费率计算标准" prop="contractPayment">
              <el-select v-model="form.contractPayment" placeholder="请选择费率计算标准" @change="handlePaymentChange" style="width: 100%">
                <el-option label="LC" value="LC" />
                <el-option label="LCOA" value="LCOA" />
                <el-option label="OA" value="OA" />
                <el-option label="DP" value="DP" />
                <el-option label="DA" value="DA" />
              </el-select>
            </el-form-item>
          </el-col>



          <el-col :span="8">
            <el-form-item label="发票号" prop="contractNo">
              <el-input v-model="form.contractNo" placeholder="请输入发票号" maxlength="100" style="width: 100%" />
            </el-form-item>
          </el-col>
        </el-row>




         <!-- 商品信息区域 -->
        <el-row :gutter="20">
          
          <el-col :span="8">
            <el-form-item label="海关商品代码" prop="customsCode">
              <el-input v-model="form.customsCode" placeholder="请输入海关商品代码" maxlength="40" style="width: 100%" />
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item label="商品数量及单位" prop="productNum">
              <el-input v-model="form.productNum" placeholder="请输入商品数量及单位" maxlength="30" style="width: 100%" />
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item label="商品类别" prop="exportTrade">
              <el-select 
                v-model="form.exportTrade" 
                multiple
                placeholder="请选择商品类别"
                style="width: 100%"
              >
                <el-option 
                  v-for="category in productCategoryOptions"
                  :key="category.code"
                  :label="`${category.code} - ${category.name}`"
                  :value="category.code"
                />
              </el-select>
            </el-form-item>
          </el-col>
        </el-row>

        <!-- 商品类别和备注区域
        <el-row :gutter="20">
          <el-col :span="8">
            <el-form-item label="商品类别" prop="exportTrade">
              <el-select 
                v-model="form.exportTrade" 
                multiple
                placeholder="请选择商品类别"
                style="width: 100%"
              >
                <el-option 
                  v-for="category in productCategoryOptions"
                  :key="category.code"
                  :label="`${category.code} - ${category.name}`"
                  :value="category.code"
                />
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="8" v-if="showTradeRemark">
            <el-form-item label="商品类别备注" prop="exportTradeInput">
              <el-input v-model="form.exportTradeInput" placeholder="商品类别包含'其他'时必填" maxlength="50" style="width: 100%" />
            </el-form-item>
          </el-col>
        </el-row>
 -->





        <!-- 日期信息区域 -->
        <el-row :gutter="20">
          <el-col :span="8">
            <el-form-item label="出货日" prop="happenDate">
              <el-date-picker
                v-model="form.happenDate"
                type="date"
                placeholder="选择出货日"
                value-format="yyyy-MM-dd"
                :picker-options="happenDateOptions"
                style="width: 100%"
              />
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item label="信用起始日" prop="startDate">
              <el-date-picker
                v-model="form.startDate"
                type="date"
                placeholder="选择信用起始日"
                value-format="yyyy-MM-dd"
                :picker-options="startDateOptions"
                style="width: 100%"
              />
            </el-form-item>
          </el-col>

            <el-col :span="8" v-if="showTradeRemark">
            <el-form-item label="商品类别备注" prop="exportTradeInput">
              <el-input v-model="form.exportTradeInput" placeholder="商品类别包含'其他'时必填" maxlength="50" style="width: 100%" />
            </el-form-item>
          </el-col>
          
        </el-row>

        <!-- 金额和期限区域 -->
        <el-row :gutter="20">
          <el-col :span="8">
            <el-form-item label="赊销金额" prop="xamInvoiceAmout">
              <el-input 
                v-model="form.xamInvoiceAmout"
                placeholder="请输入赊销金额"
                style="width: 100%"
                @input="handleInvoiceAmountChange"
              />
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item label="信用期限(天)" prop="paymentTerms">
              <el-input-number v-model="form.paymentTerms" :min="1" :controls="false" style="width: 100%" />
            </el-form-item>
          </el-col>
        
        </el-row>

        <!-- 新增回款日期字段 -->
        <el-row :gutter="20">
              <el-col :span="8">
            <el-form-item label="确认收汇金额" prop="xamRecoverAmount">
              <el-input-number 
                v-model="form.xamRecoverAmount" 
                :precision="2" 
                :controls="false"
                :disabled="tr"
                placeholder="请输入确认收汇金额"
                style="width: 100%"
                @change="handleRecoverAmountChange"
              />
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item label="回款日期" prop="recoverDate">
              <el-date-picker
                v-model="form.recoverDate"
                type="date"
                placeholder="选择回款日期"
                value-format="yyyy-MM-dd"
                :disabled="!form.xamRecoverAmount"
                style="width: 100%"
              />
            </el-form-item>
          </el-col>
        </el-row>

        <!-- 国际贸易相关信息 -->
        <el-row :gutter="20">
          <el-col :span="8">
            <el-form-item label="币种" prop="currency">
              <el-select v-model="form.currency" placeholder="请选择币种" style="width: 100%">
                <el-option 
                  v-for="currency in currencyOptions"
                  :key="currency.code"
                  :label="`${currency.code} - ${currency.value}`"
                  :value="currency.code"
                />
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item label="国家和地区" prop="countryOrArea">
              <el-select v-model="form.countryOrArea" placeholder="请选择国家和地区" filterable style="width: 100%">
                <el-option 
                  v-for="country in countryOptions"
                  :key="country.value"
                  :label="country.label"
                  :value="country.value"
                />
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item label="运输方式" prop="transportMode">
              <el-select v-model="form.transportMode" placeholder="请选择运输方式" style="width: 100%">
                <el-option 
                  v-for="mode in transportModeOptions"
                  :key="mode.value"
                  :label="mode.label"
                  :value="mode.value"
                />
              </el-select>
            </el-form-item>
          </el-col>
        </el-row>

        <!-- 费率计算标准区域 -->
        <el-row :gutter="20">
          
          <el-col :span="8">
            <el-form-item label="是否为出口前" prop="isAdditionalRisk">
              <el-radio-group v-model="form.isAdditionalRisk">
                <el-radio label="0">出口后</el-radio>
                <el-radio label="1">出口前</el-radio>
              </el-radio-group>
            </el-form-item>
          </el-col>
          <el-col :span="8" v-if="showLcFields">
            <el-form-item label="开证行Swift码" prop="bankSwift">
              <el-input v-model="form.bankSwift" placeholder="请输入开证行Swift码" maxlength="150" style="width: 100%" />
            </el-form-item>
          </el-col>
          <el-col :span="8"  v-if="showLcFields">
            <el-form-item label="提交单据日" prop="comitFormDate">
              <el-date-picker
                v-model="form.comitFormDate"
                type="date"
                placeholder="选择提交单据日"
                value-format="yyyy-MM-dd"
                :picker-options="comitFormDateOptions"
                style="width: 100%"
              />
            </el-form-item>
          </el-col>
        </el-row>


        <el-row>
          <el-col :span="24">
            <el-form-item label="备注" prop="remark">
              <el-input 
                v-model="form.remark" 
                type="textarea" 
                :rows="3" 
                placeholder="请输入备注" 
                maxlength="300" 
                show-word-limit
              />
            </el-form-item>
          </el-col>
        </el-row>

        <!-- 操作按钮区域 -->
        <el-row>
          <el-col :span="24" style="text-align: center;">
            <el-form-item>
              <el-button type="primary" @click="submitForm">提交</el-button>
              <el-button @click="resetForm">重置</el-button>
            </el-form-item>
          </el-col>
        </el-row>
      </el-card>

    
    </el-form>
  </div>
</template>

<script>
export default {
  data() {
    // 自定义校验规则
    const validateXamRecoverAmount = (rule, value, callback) => {
      if (value && this.form.xamInvoiceAmout && value > this.form.xamInvoiceAmout) {
        callback(new Error('确认收汇金额不能大于赊销金额'))
      } else {
        callback()
      }
    }

    const validateExportTradeInput = (rule, value, callback) => {
      if (this.form.exportTrade && this.form.exportTrade.includes('其他') && !value) {
        callback(new Error('商品类别包含"其他"时必填'))
      } else {
        callback()
      }
    }

    const validateRecoverDate = (rule, value, callback) => {
      if (this.form.xamRecoverAmount && !value) {
        callback(new Error('确认收汇金额有值时回款日期必填'))
      } else {
        callback()
      }
    }

    return {
      piccCodeOptions: [
        { value: 'PICC001', label: 'PICC001 - 买方A' },
        { value: 'PICC002', label: 'PICC002 - 买方B' },
        { value: 'PICC003', label: 'PICC003 - 买方C' },
      ],
      currencyOptions: [
        { code: 'CNY', value: '人民币' },
        { code: 'EUR', value: '欧元' },
        { code: 'GBP', value: '英镑' },
        { code: 'HKD', value: '港元' },
        { code: 'USD', value: '美元' },
        { code: 'CAD', value: '加拿大元' },
        { code: 'CHF', value: '瑞士法郎' },
        { code: 'JPY', value: '日元' }
      ],
      transportModeOptions: [
        { value: 'AIR', label: '空运' },
        { value: 'SEA', label: '海运' },
        { value: 'LAND', label: '陆运' },
        { value: 'RAIL', label: '铁路' }
      ],
      countryOptions: [
        { value: 'CHINA', label: '中国' },
        { value: 'USA', label: '美国' },
        { value: 'JAPAN', label: '日本' },
        { value: 'GERMANY', label: '德国' },
        { value: 'UK', label: '英国' }
      ],
      productCategoryOptions: [
        { code: '01', name: '家用电器、电脑及其周边产品' },
        { code: '02', name: '汽车、模特车及其零配件' },
        { code: '03', name: '医药化学品' },
        { code: '04', name: '农产品及农药、化肥' },
        { code: '05', name: '纺织品、皮革及其制品' },
        { code: '06', name: '金属及其制品' },
        { code: '07', name: '家具' },
        { code: '08', name: '陶瓷产品' },
        { code: '09', name: '通讯产品' },
        { code: '10', name: '光伏产品' },
        { code: '11', name: '燃料' },
        { code: '12', name: '机械及其零部件' },
        { code: '13', name: '船舶、零件及修理' },
        { code: '14', name: '印刷电路板及原材料' },
        { code: '15', name: '玩具' },
        { code: '16', name: '其他' }
      ],
      form: {
        corpSerialNo: '',
        happenDate: '',
        happen2Date: '',
        riskPiccCode: '',
        productNum: '',
        startDate: '',
        accrualDate: '',
        comitFormDate: '',
        bankSwift: '',
        currency: '',
        transportMode: '',
        countryOrArea: '',
        customsCode: '',
        xamInvoiceAmout: null,
        recoverDate: '',
        paymentTerms: null,
        contractNo: '',
        contractPayment: '',
        xamRecoverAmount: null,
        exportTrade: '',
        exportTradeInput: '',
        isAdditionalRisk: '0',
        remark: ''
      },
      rules: {
        corpSerialNo: [{ required: true, message: '业务流水号不能为空', trigger: 'blur' }],
        happenDate: [{ required: true, message: '出货日不能为空', trigger: 'change' }],
        riskPiccCode: [{ required: true, message: '买方PICC Code不能为空', trigger: 'blur' }],
        startDate: [{ required: true, message: '信用起始日不能为空', trigger: 'change' }],
        comitFormDate: [{ required: true, message: '提交单据日不能为空', trigger: 'change' }],
        bankSwift: [{ required: true, message: '开证行Swift码不能为空', trigger: 'blur' }],
        currency: [{ required: true, message: '币种不能为空', trigger: 'blur' }],
        countryOrArea: [{ required: true, message: '国家和地区不能为空', trigger: 'blur' }],
        customsCode: [{ required: true, message: '海关商品代码不能为空', trigger: 'blur' }],
        xamInvoiceAmout: [{ required: true, message: '赊销金额不能为空', trigger: 'blur' }],
        paymentTerms: [{ required: true, message: '信用期限不能为空', trigger: 'blur' }],
        contractNo: [{ required: true, message: '发票号不能为空', trigger: 'blur' }],
        contractPayment: [{ required: true, message: '费率计算标准不能为空', trigger: 'change' }],
        exportTrade: [{ required: false, message: '商品类别不能为空', trigger: 'blur' }],
        exportTradeInput: [
          { validator: validateExportTradeInput, trigger: 'blur' }
        ],
        xamRecoverAmount: [
          { validator: validateXamRecoverAmount, trigger: 'blur' }
        ],
        xamInvoiceAmout: [
          { required: true, message: '赊销金额不能为空', trigger: 'blur' },
          { 
            validator: (rule, value, callback) => {
              if (!/^\d+(\.\d{1,2})?$/.test(value)) {
                callback(new Error('请输入数字且最多保留两位小数'))
              } else {
                callback()
              }
            },
            trigger: 'blur'
          }
        ],
        recoverDate: [
          { validator: validateRecoverDate, trigger: 'change' }
        ],
      },
      happenDateOptions: {
        disabledDate(time) {
          return time.getTime() > Date.now()
        }
      },
      startDateOptions: {
        disabledDate: (time) => {
          if (!this.form.happenDate) return false
          const happenDate = new Date(this.form.happenDate)
          const minDate = new Date(happenDate)
          const maxDate = new Date(happenDate)
          maxDate.setDate(maxDate.getDate() + 30)
          return time.getTime() < minDate.getTime() || time.getTime() > maxDate.getTime()
        }
      },
      comitFormDateOptions: {
        disabledDate: (time) => {
          if (!this.form.happenDate) return false
          const happenDate = new Date(this.form.happenDate)
          return time.getTime() < happenDate.getTime()
        }
      }
    }
  },
  computed: {
    showLcFields() {
      return ['LC', 'LCOA'].includes(this.form.contractPayment)
    },
    showTradeRemark() {
      return this.form.exportTrade && this.form.exportTrade.includes('16')
    }
  },
  methods: {
    handlePaymentChange(value) {
      this.$refs.creditSaleForm.clearValidate()
      if (['LC', 'LCOA'].includes(value)) {
        this.rules.comitFormDate = [{ required: true, message: '提交单据日不能为空', trigger: 'change' }]
        this.rules.bankSwift = [{ required: true, message: '开证行Swift码不能为空', trigger: 'blur' }]
      } else {
        this.rules.comitFormDate = []
        this.rules.bankSwift = []
      }
    },
    submitForm() {
      this.$refs.creditSaleForm.validate(valid => {
        if (valid) {
          // 提交表单逻辑
          console.log('表单验证通过', this.form)
        } else {
          console.log('表单验证失败')
          return false
        }
      })
    },
    resetForm() {
      this.$refs.creditSaleForm.resetFields()
    },
    handleInvoiceAmountChange(value) {
      if (value && !/^\d+(\.\d{1,2})?$/.test(value)) {
        this.$nextTick(() => {
          this.form.xamInvoiceAmout = this.form.xamInvoiceAmout.replace(/[^\d.]/g, '')
        })
      }
    //   if (value ) {
        this.form.xamRecoverAmount = value
    //   }
    },
    handleRecoverAmountChange(value) {
      if (!value) {
        this.form.recoverDate = ''
      }
    }
  }
}
</script>

<style scoped>
.app-container {
  padding: 20px;
}
.el-form-item {
  margin-bottom: 22px;
}
.box-card {
  margin-bottom: 20px;
}
.clearfix {
  font-weight: bold;
  font-size: 16px;
}
</style>