<template>
  <div class="credit-quota-apply">
    <el-form 
      :model="form" 
      :rules="rules" 
      ref="form" 
      label-width="150px"
      label-position="right"
      class="form-container">
      
      <!-- 基础信息 -->
      <el-card class="box-card">
        <div slot="header" class="clearfix">
          <span>基础信息</span>
          <div style="float: right;" v-if="showButtons">
            <el-button type="primary" @click="acceptApplication">受理</el-button>
            <el-button type="danger" @click="showRejectDialog = true">拒绝</el-button>
          </div>
        </div>
        <el-row :gutter="20">
          <el-col :span="24">
            <el-form-item label="业务类型" prop="businessType" class="form-item">
              <el-radio-group v-model="form.businessType" @change="handleBusinessTypeChange">
               <!--  <el-radio label="LC">信用证限额</el-radio>-->
                <el-radio label="NON_LC">非信用证限额</el-radio>
              </el-radio-group>
            </el-form-item>
          </el-col>
        </el-row>
      </el-card>

      <!-- 买方信息 -->
      <el-card class="box-card">
        <div slot="header" class="clearfix">
          <span>买方信息</span>
        </div>
        <el-row :gutter="20">
          <el-col :span="12">
            <el-form-item label="买方名称" prop="riskCompName" class="form-item">
              <el-input v-model="form.riskCompName" maxlength="600" placeholder="英文大写"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="12">
            <el-form-item label="买方国家和地区" prop="countryOrArea" class="form-item">
              <el-select 
                v-model="form.countryOrArea" 
                placeholder="请选择国家和地区"
                @change="handleCountryChange">
                <el-option label="中国" value="CHN"></el-option>
                <el-option label="美国" value="USA"></el-option>
                <el-option label="日本" value="JPN"></el-option>
                <el-option label="德国" value="DEU"></el-option>
                <el-option label="英国" value="GBR"></el-option>
              </el-select>
            </el-form-item>
          </el-col>
        </el-row>

        <el-row :gutter="20">
          <el-col :span="12">
            <el-form-item label="买方地址" prop="riskCompAddress" class="form-item">
              <el-input v-model="form.riskCompAddress" maxlength="300" placeholder="英文大写"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="12">
            <el-form-item label="买方所属省份" prop="province" class="form-item">
              <el-select 
                v-model="form.province" 
                placeholder="请选择省份" 
                clearable
                :disabled="form.countryOrArea !== 'CHN'">
                <el-option 
                  v-for="province in provinceOptions" 
                  :key="province.code"
                  :label="province.name"
                  :value="province.code">
                </el-option>
              </el-select>
            </el-form-item>
          </el-col>
        </el-row>

        <el-row :gutter="20">
               <el-col :span="12">
            <el-form-item label="买方注册号" prop="riskMark" class="form-item">
              <el-input v-model="form.riskMark" maxlength="100"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="12">
            <el-form-item label="买方联系电话" prop="riskPhone" class="form-item">
              <el-input v-model="form.riskPhone" maxlength="50"></el-input>
            </el-form-item>
          </el-col>
     
        </el-row>

      </el-card>

      <!-- 限额信息 -->
      <el-card class="box-card">
        <div slot="header" class="clearfix">
          <span>限额信息</span>
        </div>
        <el-row :gutter="20">
          <el-col :span="8">
            <el-form-item label="限额期限(天)" prop="paidTerm" class="form-item">
              <el-input-number v-model="form.paidTerm" :min="1" :max="99999"></el-input-number>
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item label="金额" prop="appliAmount" class="form-item">
              <el-input-number v-model="form.appliAmount" :precision="0" :min="0"></el-input-number>
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item label="报告类型" prop="reportType" class="form-item">
              <el-radio-group v-model="form.reportType">
                <el-radio label="1">普通</el-radio>
                <el-radio label="2">加急</el-radio>
              </el-radio-group>
            </el-form-item>
          </el-col>
        </el-row>
        
        <el-row :gutter="20" >
          <el-col :span="12">
            <el-form-item label="支付方式" prop="payWay" class="form-item">
              <el-select 
                v-model="form.payWay" 
                placeholder="请选择支付方式"
                :disabled="form.businessType === 'LC'">
                <el-option 
                  v-if="form.businessType === 'LC'" 
                  label="LC" 
                  value="LC">
                </el-option>
                <el-option 
                  v-if="form.businessType === 'NON_LC'" 
                  v-for="item in ['DP','DA','OA']" 
                  :key="item"
                  :label="item"
                  :value="item">
                </el-option>
              </el-select>
            </el-form-item>
          </el-col>
        </el-row>
      </el-card>

      <!-- 合并后的商品信息 -->
      <el-card class="box-card">
        <div slot="header" class="clearfix">
          <span>商品信息</span>
        </div>
        <el-row :gutter="20">
          <el-col :span="12">
            <el-form-item label="商品类别" prop="exportTrade" class="form-item">
              <el-select v-model="form.exportTrade" placeholder="请选择商品类别" @change="handleExportTradeChange">
                <el-option 
                  v-for="item in productCategoryOptions" 
                  :key="item.code"
                  :label="item.name"
                  :value="item.code">
                </el-option>
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="12" v-if="form.exportTrade === '16'">
            <el-form-item label="商品类别说明" prop="exportTradeInput" class="form-item">
              <el-input v-model="form.exportTradeInput" maxlength="40" placeholder="请输入商品类别说明"></el-input>
            </el-form-item>
          </el-col>
        </el-row>

        <el-row :gutter="20">
          <el-col :span="24">
            <el-form-item label="运输方式" prop="transType" class="form-item">
              <el-radio-group v-model="form.transType">
                <el-radio label="1">海运</el-radio>
                <el-radio label="2">空运</el-radio>
                <el-radio label="3">陆运</el-radio>
                <el-radio label="4">海陆联运</el-radio>
              </el-radio-group>
            </el-form-item>
          </el-col>
        </el-row>
        
        <el-row :gutter="20" v-if="form.businessType === 'LC'">
          <el-col :span="24">
            <el-form-item label="出口商品名称" prop="exportComName" class="form-item">
              <el-input v-model="form.exportComName" maxlength="100"></el-input>
            </el-form-item>
          </el-col>
        </el-row>
        <el-row :gutter="20" v-if="form.businessType === 'NON_LC'">
          <el-col :span="24">
            <el-form-item label="出口商品名称" prop="exportName" class="form-item">
              <el-input v-model="form.exportName" maxlength="100"></el-input>
            </el-form-item>
          </el-col>
        </el-row>

      </el-card>

      <!-- 信用证信息 -->
      <el-card class="box-card" v-if="form.businessType === 'LC'">
        <div slot="header" class="clearfix">
          <span>信用证信息</span>
        </div>
        <el-form-item label="信用证号" prop="creditNo" class="form-item">
          <el-input v-model="form.creditNo" maxlength="50"></el-input>
        </el-form-item>

         <!-- 新增的信用证银行信息 -->
        <el-row :gutter="20">
          <el-col :span="12">
            <el-form-item label="开证行名称" prop="bankName" class="form-item">
              <el-input v-model="form.bankName" maxlength="150" placeholder="英文大写"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="12">
            <el-form-item label="开证行国家和地区" prop="bankCountry" class="form-item">
              <el-select v-model="form.bankCountry" placeholder="请选择国家和地区">
                <el-option label="中国" value="CHN"></el-option>
                <el-option label="美国" value="USA"></el-option>
                <el-option label="日本" value="JPN"></el-option>
                <el-option label="德国" value="DEU"></el-option>
                <el-option label="英国" value="GBR"></el-option>
              </el-select>
            </el-form-item>
          </el-col>
        </el-row>

        <el-row :gutter="20">
          <el-col :span="12">
            <el-form-item label="开证行SWIFT" prop="bankSwift" class="form-item">
              <el-input v-model="form.bankSwift" maxlength="50"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="12">
            <el-form-item label="开证行地址" prop="bankAddress" class="form-item">
              <el-input v-model="form.bankAddress" maxlength="200"></el-input>
            </el-form-item>
          </el-col>
        </el-row>
        
        <el-form-item label="是否循环使用" prop="isCycleUse" class="form-item">
          <el-radio-group v-model="form.isCycleUse">
            <el-radio label="0">非循环</el-radio>
            <el-radio label="1">循环</el-radio>
          </el-radio-group>
        </el-form-item>
        
       

        <!-- 新增的是否分批字段 -->
        <el-form-item label="是否分批" prop="isBatch" class="form-item">
          <el-radio-group v-model="form.isBatch">
            <el-radio label="0">否</el-radio>
            <el-radio label="1">是</el-radio>
          </el-radio-group>
        </el-form-item>

        <template v-if="form.isBatch === '1'">
          <el-row :gutter="20">
            <el-col :span="12">
              <el-form-item label="第一批金额" prop="maxAmount1" class="form-item">
                <el-input-number v-model="form.maxAmount1" :precision="0" :min="0"></el-input-number>
              </el-form-item>
            </el-col>
            <el-col :span="12">
              <el-form-item label="第二批金额" prop="maxAmount2" class="form-item">
                <el-input-number v-model="form.maxAmount2" :precision="0" :min="0"></el-input-number>
              </el-form-item>
            </el-col>
          </el-row>
        </template>

       

      </el-card>
      


      <!-- 历史交易信息 -->
      <el-card class="box-card">
        <div slot="header" class="clearfix">
          <span>历史交易信息</span>
        </div>
        <el-form-item label="是否存在历史交易" prop="historyBusiness" class="form-item">
          <el-radio-group v-model="form.historyBusiness">
            <el-radio label="0">否</el-radio>
            <el-radio label="1">是</el-radio>
          </el-radio-group>
        </el-form-item>

        <template v-if="form.historyBusiness === '1'">
          <el-row :gutter="20">
            <el-col :span="12">
              <el-form-item label="最早成交年份" prop="earlyDealYear" class="form-item">
                <el-input v-model="form.earlyDealYear" maxlength="50"></el-input>
              </el-form-item>
            </el-col>
            <el-col :span="12">
              <el-form-item 
                label="银行付款表现" 
                prop="bankPerformance"
                v-if="form.businessType === 'LC'"
                class="form-item">
                <el-select v-model="form.bankPerformance">
                  <el-option label="及时" value="及时"></el-option>
                  <el-option label="尚可" value="尚可"></el-option>
                  <el-option label="较慢" value="较慢"></el-option>
                </el-select>
              </el-form-item>
            </el-col>
          </el-row>

          <el-row :gutter="20">
            <el-col :span="12">
              <el-form-item label="买方付款表现" prop="riskPerformance" class="form-item">
                <el-select v-model="form.riskPerformance">
                  <el-option label="及时" value="及时"></el-option>
                  <el-option label="不及时" value="不及时"></el-option>
                </el-select>
              </el-form-item>
            </el-col>
            <el-col :span="12">
              <el-form-item label="开始放账年份" prop="startYear" class="form-item">
                <el-input v-model="form.startYear" maxlength="50"></el-input>
              </el-form-item>
            </el-col>
          </el-row>

          <el-row :gutter="20">
            <el-col :span="8">
              <el-form-item label="L/C交易金额" prop="transactionLc" class="form-item">
                <el-input v-model="form.transactionLc" maxlength="50"></el-input>
              </el-form-item>
            </el-col>
            <el-col :span="8">
              <el-form-item label="D/P交易金额" prop="transactionDp" class="form-item">
                <el-input v-model="form.transactionDp" maxlength="50"></el-input>
              </el-form-item>
            </el-col>
            <el-col :span="8">
              <el-form-item label="D/A&OA交易金额" prop="transactionDa" class="form-item">
                <el-input v-model="form.transactionDa" maxlength="50"></el-input>
              </el-form-item>
            </el-col>
          </el-row>

          <el-form-item label="历史上是否有拖欠" prop="isDefaultThis" class="form-item">
            <el-radio-group v-model="form.isDefaultThis">
              <el-radio label="0">无</el-radio>
              <el-radio label="1">有</el-radio>
            </el-radio-group>
          </el-form-item>

          <template v-if="form.isDefaultThis === '1'">
            <el-row :gutter="20">
              <el-col :span="12">
                <el-form-item label="拖欠金额" prop="defaultAmount" class="form-item">
                  <el-input-number v-model="form.defaultAmount" :precision="0" :min="0"></el-input-number>
                </el-form-item>
              </el-col>
              <el-col :span="12">
                <el-form-item label="拖欠时间(天)" prop="defaultDate" class="form-item">
                  <el-input-number v-model="form.defaultDate" :min="1"></el-input-number>
                </el-form-item>
              </el-col>
            </el-row>
          </template>
        </template>

      </el-card>

      <!-- 其他信息 -->
      <el-card class="box-card">
        <div slot="header" class="clearfix">
          <span>其他信息</span>
        </div>
        <el-form-item label="备注" prop="remark" class="form-item">
          <el-input v-model="form.remark" type="textarea" maxlength="1500"></el-input>
        </el-form-item>
      </el-card>

      <el-form-item>
        <el-button type="primary" @click="submitForm">提交</el-button>
      </el-form-item>
    </el-form>
    
    <!-- 拒绝原因弹窗 -->
    <el-dialog title="拒绝原因" :visible.sync="showRejectDialog" width="400px">
      <el-form :model="rejectForm" :rules="rejectRules" ref="rejectForm">
        <el-form-item label="拒绝原因" prop="reason">
          <el-input 
            type="textarea" 
            v-model="rejectForm.reason" 
            placeholder="请输入拒绝原因"
            maxlength="200"
            show-word-limit
            :autosize="{ minRows: 3, maxRows: 6 }">
          </el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="showRejectDialog = false">取 消</el-button>
        <el-button type="primary" @click="submitRejection">确 定</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
export default {
  name: 'CreditQuotaApply',
  props: {
    showButtons: {
      type: Boolean,
      default: false
    }
  },
  data() {
    return {
      showRejectDialog: false,
      rejectForm: {
        reason: ''
      },
      rejectRules: {
        reason: [
          { required: true, message: '请输入拒绝原因', trigger: 'blur' }
        ]
      },
      provinceOptions: [
        {code: '11000000', name: '北京'},
        {code: '12000000', name: '天津'},
        {code: '13000000', name: '河北'},
        {code: '14000000', name: '山西'},
        {code: '15000000', name: '内蒙古'},
        {code: '21000000', name: '辽宁'},
        {code: '22000000', name: '吉林'},
        {code: '23000000', name: '黑龙江'},
        {code: '31000000', name: '上海'},
        {code: '32000000', name: '江苏'},
        {code: '33000000', name: '浙江'},
        {code: '34000000', name: '安徽'},
        {code: '35000000', name: '福建'},
        {code: '36000000', name: '江西'},
        {code: '37000000', name: '山东'},
        {code: '41000000', name: '河南'},
        {code: '42000000', name: '湖北'},
        {code: '43000000', name: '湖南'},
        {code: '44000000', name: '广东'},
        {code: '45000000', name: '广西'},
        {code: '46000000', name: '海南'},
        {code: '50000000', name: '重庆'},
        {code: '51000000', name: '四川'},
        {code: '52000000', name: '贵州'},
        {code: '53000000', name: '云南'},
        {code: '54000000', name: '西藏'},
        {code: '61000000', name: '陕西'},
        {code: '62000000', name: '甘肃'},
        {code: '63000000', name: '青海'},
        {code: '64000000', name: '宁夏'},
        {code: '65000000', name: '新疆'}
      ],
      productCategoryOptions: [
        {code: '01', name: '家用电器、电脑及其周边产品'},
        {code: '02', name: '汽车、模特车及其零配件'},
        {code: '03', name: '医药化学品'},
        {code: '04', name: '农产品及农药、化肥'},
        {code: '05', name: '纺织品、皮革及其制品'},
        {code: '06', name: '金属及其制品'},
        {code: '07', name: '家具'},
        {code: '08', name: '陶瓷产品'},
        {code: '09', name: '通讯产品'},
        {code: '10', name: '光伏产品'},
        {code: '11', name: '燃料'},
        {code: '12', name: '机械及其零部件'},
        {code: '13', name: '船舶、零件及修理'},
        {code: '14', name: '印刷电路板及原材料'},
        {code: '15', name: '玩具'},
        {code: '16', name: '其他'}
      ],
      form: {
        // 基础信息
        id: null,
        tenantId: null,
        businessType: 'NON_LC',
        
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
        reportType: '1',
        
        // 商品信息
        exportTrade: '',
        exportTradeInput: '',
        transType: '',
        
        // 信用证信息
        creditNo: '',
        isCycleUse: '0',
        bankName: '',
        bankCountry: '',
        bankSwift: '',
        bankAddress: '',
        exportComName: '',
        isBatch: '0',
        maxAmount1: 0,
        maxAmount2: 0,
        
        // 历史交易信息
        historyBusiness: '0',
        earlyDealYear: '',
        bankPerformance: '',
        riskPerformance: '',
        transactionLc: '',
        transactionDp: '',
        transactionDa: '',
        isDefaultThis: '',
        defaultAmount: 0,
        defaultDate: 0,
        startYear: '',
        
        // 其他信息
        remark: '',
        changeReasonCode: '',
        changeReasonRemark: '',
        exportName: '',
        payWay: 'OA',
        needTranslation: '',
        corpserialNo: ''
      },
      rules: {
        businessType: [
          { required: true, message: '请选择业务类型', trigger: 'change' }
        ],
        riskCompName: [
          { required: true, message: '请输入买方名称', trigger: 'blur' },
          { max: 600, message: '长度不能超过600个字符', trigger: 'blur' }
        ],
        countryOrArea: [
          { required: true, message: '请选择买方国家和地区', trigger: 'change' }
        ],
        riskCompAddress: [
          { required: true, message: '请输入买方地址', trigger: 'blur' },
          { max: 300, message: '长度不能超过300个字符', trigger: 'blur' }
        ],
        riskMark: [
          { required: true, message: '请输入买方注册号', trigger: 'blur' },
          { max: 100, message: '长度不能超过100个字符', trigger: 'blur' }
        ],
        province: [
          { 
            validator: (rule, value, callback) => {
              if (this.form.countryOrArea === 'CHN' && !value) {
                callback(new Error('买方所属省份为必填项'));
              } else {
                callback();
              }
            },
            trigger: 'blur'
          },
          { max: 12, message: '长度不能超过12个字符', trigger: 'blur' }
        ],
        paidTerm: [
          { required: true, message: '请输入限额期限', trigger: 'blur' },
          { type: 'number', min: 1, message: '必须大于0', trigger: 'blur' }
        ],
        appliAmount: [
          { required: true, message: '请输入金额', trigger: 'blur' },
          { type: 'number', min: 0, message: '必须大于等于0', trigger: 'blur' }
        ],
        reportType: [
          { required: true, message: '请选择报告类型', trigger: 'change' }
        ],
        exportTrade: [
          { required: true, message: '请选择商品类别', trigger: 'change' }
        ],
        exportTradeInput: [
          { 
            validator: (rule, value, callback) => {
              if (this.form.exportTrade === '16' && !value) {
                callback(new Error('商品类别说明为必填项'));
              } else {
                callback();
              }
            },
            trigger: 'blur'
          },
          { max: 40, message: '长度不能超过40个字符', trigger: 'blur' }
        ],
        earlyDealYear: [
          { 
            validator: (rule, value, callback) => {
              if (this.form.historyBusiness === '1' && !value) {
                callback(new Error('请输入最早成交年份'));
              } else {
                callback();
              }
            },
            trigger: 'blur'
          }
        ],
        riskPerformance: [
          { 
            validator: (rule, value, callback) => {
              if (this.form.historyBusiness === '1' && !value) {
                callback(new Error('请选择买方付款表现'));
              } else {
                callback();
              }
            },
            trigger: 'change'
          }
        ],
        startYear: [
          { 
            validator: (rule, value, callback) => {
              if (this.form.historyBusiness === '1' && !value) {
                callback(new Error('请输入开始放账年份'));
              } else {
                callback();
              }
            },
            trigger: 'blur'
          }
        ],
        isDefaultThis: [
          { 
            validator: (rule, value, callback) => {
              if (this.form.historyBusiness === '1' && !value) {
                callback(new Error('请选择历史上是否有拖欠'));
              } else {
                callback();
              }
            },
            trigger: 'change'
          }
        ],
        defaultAmount: [
          { 
            validator: (rule, value, callback) => {
              if (this.form.isDefaultThis === '1' && !value) {
                callback(new Error('请输入拖欠金额'));
              } else {
                callback();
              }
            },
            trigger: 'blur'
          }
        ],
        defaultDate: [
          { 
            validator: (rule, value, callback) => {
              if (this.form.isDefaultThis === '1' && !value) {
                callback(new Error('请输入拖欠时间'));
              } else {
                callback();
              }
            },
            trigger: 'blur'
          }
        ],
        transactionLc: [
            { 
                validator: (rule, value, callback) => {
                    if (this.form.historyBusiness === '1' && 
                        !value && 
                        !this.form.transactionDp && 
                        !this.form.transactionDa) {
                        callback(new Error('L/C、D/P、D/A&OA交易金额至少填写一项'));
                    } else {
                        callback();
                    }
                },
                trigger: 'blur'
            }
        ],
        transactionDp: [
            { 
                validator: (rule, value, callback) => {
                    if (this.form.historyBusiness === '1' && 
                        !value && 
                        !this.form.transactionLc && 
                        !this.form.transactionDa) {
                        callback(new Error('L/C、D/P、D/A&OA交易金额至少填写一项'));
                    } else {
                        callback();
                    }
                },
                trigger: 'blur'
            }
        ],
        transactionDa: [
            { 
                validator: (rule, value, callback) => {
                    if (this.form.historyBusiness === '1' && 
                        !value && 
                        !this.form.transactionLc && 
                        !this.form.transactionDp) {
                        callback(new Error('L/C、D/P、D/A&OA交易金额至少填写一项'));
                    } else {
                        callback();
                    }
                },
                trigger: 'blur'
            }
        ],
        creditNo: [
          { required: true, message: '请输入信用证号', trigger: 'blur' },
          { max: 50, message: '长度不能超过50个字符', trigger: 'blur' }
        ],
        isCycleUse: [
          { required: true, message: '请选择是否循环使用', trigger: 'change' }
        ],
        bankName: [
          { required: true, message: '请输入开证行名称', trigger: 'blur' },
          { max: 150, message: '长度不能超过150个字符', trigger: 'blur' }
        ],
        bankCountry: [
          { required: true, message: '请选择开证行国家和地区', trigger: 'change' }
        ],
        bankSwift: [
          { required: true, message: '请输入开证行SWIFT', trigger: 'blur' },
          { max: 50, message: '长度不能超过50个字符', trigger: 'blur' }
        ],
        bankAddress: [
          { required: true, message: '请输入开证行地址', trigger: 'blur' },
          { max: 200, message: '长度不能超过200个字符', trigger: 'blur' }
        ],
        exportComName: [
          { required: true, message: '请输入出口商品名称', trigger: 'blur' },
          { max: 100, message: '长度不能超过100个字符', trigger: 'blur' }
        ],
        isBatch: [
          { required: true, message: '请选择是否分批', trigger: 'change' }
        ],
        maxAmount1: [
          { 
            validator: (rule, value, callback) => {
              if (this.form.isBatch === '1' && !value) {
                callback(new Error('请输入第一批金额'));
              } else {
                callback();
              }
            },
            trigger: 'blur'
          }
        ],
        maxAmount2: [
          { 
            validator: (rule, value, callback) => {
              if (this.form.isBatch === '1' && !value) {
                callback(new Error('请输入第二批金额'));
              } else {
                callback();
              }
            },
            trigger: 'blur'
          }
        ],
        payWay: [
          { required: true, message: '请选择支付方式', trigger: 'change' }
        ],
        exportName: [
                    { required: true, message: '请输入出口商品名称', trigger: 'change' },

          { 
            validator: (rule, value, callback) => {
              if ( !value) {
                callback(new Error('请输入出口商品名称'));
              } else {
                callback();
              }
            },
            trigger: 'blur'
          },
          { max: 100, message: '长度不能超过100个字符', trigger: 'blur' }
        ],
      }
    }
  },
  methods: {
    acceptApplication() {
      this.$message.success('已受理该申请');
      // 这里可以添加实际的受理逻辑
    },
    
    submitRejection() {
      this.$refs.rejectForm.validate((valid) => {
        if (valid) {
          this.$message.success('已拒绝该申请，原因：' + this.rejectForm.reason);
          this.showRejectDialog = false;
          this.rejectForm.reason = '';
          // 这里可以添加实际的拒绝逻辑
        }
      });
    },
    
    handleCountryChange(value) {
      if (value !== 'CHN') {
        this.form.province = '';
      }
    },
    handleExportTradeChange(value) {
      if (value !== '16') {
        this.form.exportTradeInput = '';
      }
    },
    handleBusinessTypeChange(val) {
      // 业务类型变化时重置信用证相关字段
      if (val !== 'LC') {
        this.form.creditNo = '';
        this.form.isCycleUse = '0';
        this.form.bankName = '';
        this.form.bankCountry = '';
        this.form.bankSwift = '';
        this.form.bankAddress = '';
        this.form.exportComName = '';
        this.form.isBatch = '';
        this.form.maxAmount1 = 0;
        this.form.maxAmount2 = 0;
        this.form.payWay = ''; // 重置支付方式
        
        // 清除所有相关验证规则
        this.$nextTick(() => {
          this.$refs.form.clearValidate([
            'creditNo', 
            'isCycleUse',
            'bankName',
            'bankCountry',
            'bankSwift',
            'bankAddress',
            'exportComName',
            'isBatch',
            'maxAmount1',
            'maxAmount2',
            'exportName',
            'payWay'
          ]);
        });
      } else {
        // 当业务类型变为LC时，自动设置支付方式为LC
        this.form.payWay = 'LC';
        this.form.exportName = '';
        // 清除非LC相关的验证规则
        this.$nextTick(() => {
          this.$refs.form.clearValidate([
            'exportName',
            'payWay'
          ]);
        });
      }
    },
    submitForm() {
      this.$refs.form.validate((valid) => {
        if (valid) {
          console.log('submit!', this.form)
        } else {
          console.log('error submit!!')
          return false
        }
      })
    }
  }
}
</script>

<style scoped>
.credit-quota-apply {
  padding: 20px;
}

.form-container {
  max-width: 1200px;
  margin: 0 auto;
}

.box-card {
  margin-bottom: 20px;
}

.form-item {
  margin-bottom: 22px;
}

.el-select, .el-input {
  width: 100%;
}
</style>