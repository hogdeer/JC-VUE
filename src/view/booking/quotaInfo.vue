<template>
  <div class="quota-info-container">
    <!-- 顶部：页面标题和操作类型选择 -->
    <el-card class="top-card">
      <div class="header-section">
        <h2 class="page-title">限额申请</h2>
        <div class="operation-selector">
          <el-radio-group v-model="operationType">
            <el-radio-button label="new">新增</el-radio-button>
            <el-radio-button label="modify">修改</el-radio-button>
            <el-radio-button label="view">查看</el-radio-button>
          </el-radio-group>
        </div>
      </div>
    </el-card>

    <div class="content-wrapper">
      <!-- 左侧：买方基本信息区域 -->
      <el-card class="info-card buyer-card">
        <div slot="header" class="card-header">
          <span>买方基本信息</span>
        </div>
        <el-form :model="buyerInfo" :rules="buyerRules" ref="buyerForm" label-width="120px" class="info-form">
          <el-row :gutter="20">
            <el-col :span="24" :md="12">
              <el-form-item label="买方名称" prop="name">
                <el-input 
                  v-model="buyerInfo.name" 
                  placeholder="请输入买方名称 英文大写" 
                  maxlength="600"
                  show-word-limit
                  @focus="showTooltip('name')"
                  @blur="hideTooltip">
                </el-input>
              </el-form-item>
            </el-col>
            <el-col :span="24" :md="12">
              <el-form-item label="买方代码" prop="code">
                <el-input 
                  v-model="buyerInfo.code" 
                  placeholder="请输入买方代码"
                  @focus="showTooltip('code')"
                  @blur="hideTooltip">
                </el-input>
              </el-form-item>
            </el-col>
            <el-col :span="24" :md="12">
              <el-form-item label="买方国家/地区" prop="country">
                <el-select 
                  v-model="buyerInfo.country" 
                  placeholder="请选择买方国家/地区" 
                  @change="handleCountryChange"
                  filterable
                  @focus="showTooltip('country')"
                  @blur="hideTooltip">
                  <el-option label="中国(CHN)" value="CHN"></el-option>
                  <el-option label="美国(USA)" value="USA"></el-option>
                  <el-option label="日本(JPN)" value="JPN"></el-option>
                  <el-option label="英国(GBR)" value="GBR"></el-option>
                  <el-option label="德国(DEU)" value="DEU"></el-option>
                </el-select>
              </el-form-item>
            </el-col>
            <el-col :span="24" :md="12">
              <el-form-item label="买方注册号" prop="registrationNumber">
                <el-input 
                  v-model="buyerInfo.registrationNumber" 
                  placeholder="请输入买方注册号" 
                  maxlength="100"
                  show-word-limit
                  @focus="showTooltip('registrationNumber')"
                  @blur="hideTooltip">
                </el-input>
              </el-form-item>
            </el-col>
            <el-col :span="24">
              <el-form-item label="买方地址" prop="address">
                <el-input 
                  v-model="buyerInfo.address" 
                  placeholder="请输入买方地址 英文大写" 
                  maxlength="300"
                  show-word-limit
                  @focus="showTooltip('address')"
                  @blur="hideTooltip">
                </el-input>
              </el-form-item>
            </el-col>
            <el-col :span="24" :md="12">
              <transition name="fade" mode="out-in">
                <el-form-item v-if="showProvinceField" label="买方所属省份" prop="province">
                  <el-select 
                    v-model="buyerInfo.province" 
                    placeholder="请选择买方所属省份"
                    @focus="showTooltip('province')"
                    @blur="hideTooltip">
                    <el-option label="北京市" value="Beijing"></el-option>
                    <el-option label="上海市" value="Shanghai"></el-option>
                    <el-option label="广东省" value="Guangdong"></el-option>
                    <el-option label="浙江省" value="Zhejiang"></el-option>
                    <el-option label="江苏省" value="Jiangsu"></el-option>
                  </el-select>
                </el-form-item>
              </transition>
            </el-col>
            <el-col :span="24" :md="12">
              <el-form-item label="联系电话" prop="phone">
                <el-input 
                  v-model="buyerInfo.phone" 
                  placeholder="请输入联系电话" 
                  maxlength="50"
                  show-word-limit
                  @focus="showTooltip('phone')"
                  @blur="hideTooltip">
                </el-input>
              </el-form-item>
            </el-col>
            <el-col :span="24" :md="12">
              <el-form-item label="联系人" prop="contact">
                <el-input 
                  v-model="buyerInfo.contact" 
                  placeholder="请输入联系人"
                  @focus="showTooltip('contact')"
                  @blur="hideTooltip">
                </el-input>
              </el-form-item>
            </el-col>
          </el-row>
        </el-form>
      </el-card>

      <!-- 右侧：限额详情区域 -->
      <el-card class="info-card quota-card">
        <div slot="header" class="card-header">
          <span>限额详情</span>
        </div>
        <el-form :model="quotaInfo" :rules="quotaRules" ref="quotaForm" label-width="100px" class="info-form">
          <el-row :gutter="20">
            <el-col :span="24" :md="12">
              <el-form-item label="限额类型" prop="type">
                <el-select 
                  v-model="quotaInfo.type" 
                  placeholder="请选择限额类型" 
                  style="width: 100%;"
                  @focus="showTooltip('quotaType')"
                  @blur="hideTooltip">
                  <el-option label="信用限额" value="credit"></el-option>
                  <el-option label="预付款限额" value="advance"></el-option>
                  <el-option label="发货限额" value="delivery"></el-option>
                  <el-option label="非信用证业务" value="NON_LC"></el-option>
                </el-select>
              </el-form-item>
            </el-col>
            <el-col :span="24" :md="12">
              <el-form-item label="币种" prop="currency">
                <el-select 
                  v-model="quotaInfo.currency" 
                  placeholder="请选择币种" 
                  style="width: 100%;"
                  @focus="showTooltip('currency')"
                  @blur="hideTooltip">
                  <el-option label="人民币(CNY)" value="CNY"></el-option>
                  <el-option label="美元(USD)" value="USD"></el-option>
                  <el-option label="欧元(EUR)" value="EUR"></el-option>
                </el-select>
              </el-form-item>
            </el-col>
            <el-col :span="24" :md="12">
              <el-form-item label="申请金额" prop="amount">
                <el-input-number 
                  v-model="quotaInfo.amount" 
                  :min="0" 
                  :step="1000" 
                  controls-position="right" 
                  style="width: 100%;"
                  placeholder="请输入申请金额"
                  @focus="showTooltip('amount')"
                  @blur="hideTooltip">
                </el-input-number>
              </el-form-item>
            </el-col>
            <el-col :span="24" :md="12">
              <el-form-item label="生效日期" prop="startDate">
                <el-date-picker
                  v-model="quotaInfo.startDate"
                  type="date"
                  placeholder="请选择生效日期"
                  style="width: 100%;"
                  @focus="showTooltip('startDate')"
                  @blur="hideTooltip">
                </el-date-picker>
              </el-form-item>
            </el-col>
            <el-col :span="24" :md="12">
              <el-form-item label="失效日期" prop="endDate">
                <el-date-picker
                  v-model="quotaInfo.endDate"
                  type="date"
                  placeholder="请选择失效日期"
                  style="width: 100%;"
                  @focus="showTooltip('endDate')"
                  @blur="hideTooltip">
                </el-date-picker>
              </el-form-item>
            </el-col>
            <el-col :span="24">
              <el-form-item label="申请理由" prop="reason">
                <el-input 
                  v-model="quotaInfo.reason" 
                  type="textarea" 
                  :rows="3" 
                  placeholder="请输入申请理由"
                  @focus="showTooltip('reason')"
                  @blur="hideTooltip">
                </el-input>
              </el-form-item>
            </el-col>
            
            <!-- 信用证业务专用字段组 -->
            <transition name="slide-fade" mode="out-in">
              <template v-if="quotaInfo.type === 'credit'">
                <el-col :span="24">
                  <el-divider content-position="left">信用证信息</el-divider>
                </el-col>
                <el-col :span="24" :md="12">
                  <el-form-item label="信用证号" prop="lcNumber">
                    <el-input 
                      v-model="quotaInfo.lcNumber" 
                      placeholder="请输入信用证号" 
                      maxlength="50"
                      show-word-limit
                      @focus="showTooltip('lcNumber')"
                      @blur="hideTooltip">
                    </el-input>
                  </el-form-item>
                </el-col>
                <el-col :span="24" :md="12">
                  <el-form-item label="是否循环使用" prop="isRevocable">
                    <el-radio-group 
                      v-model="quotaInfo.isRevocable"
                      @focus="showTooltip('isRevocable')"
                      @blur="hideTooltip">
                      <el-radio label="non-revocable">非循环使用</el-radio>
                      <el-radio label="revocable">循环使用</el-radio>
                    </el-radio-group>
                  </el-form-item>
                </el-col>
                <el-col :span="24" :md="12">
                  <el-form-item label="开证行名称" prop="issuingBankName">
                    <el-input 
                      v-model="quotaInfo.issuingBankName" 
                      placeholder="请输入开证行名称 英文大写" 
                      maxlength="150"
                      show-word-limit
                      @focus="showTooltip('issuingBankName')"
                      @blur="hideTooltip">
                    </el-input>
                  </el-form-item>
                </el-col>
                <el-col :span="24" :md="12">
                  <el-form-item label="开证行国家/地区" prop="issuingBankCountry">
                    <el-select 
                      v-model="quotaInfo.issuingBankCountry" 
                      placeholder="请选择开证行国家/地区" 
                      style="width: 100%;"
                      filterable
                      @focus="showTooltip('issuingBankCountry')"
                      @blur="hideTooltip">
                      <el-option label="中国(CHN)" value="CHN"></el-option>
                      <el-option label="美国(USA)" value="USA"></el-option>
                      <el-option label="日本(JPN)" value="JPN"></el-option>
                      <el-option label="英国(GBR)" value="GBR"></el-option>
                      <el-option label="德国(DEU)" value="DEU"></el-option>
                    </el-select>
                  </el-form-item>
                </el-col>
                <el-col :span="24" :md="12">
                  <el-form-item label="开证行SWIFT" prop="issuingBankSwift">
                    <el-input 
                      v-model="quotaInfo.issuingBankSwift" 
                      placeholder="请输入开证行SWIFT" 
                      maxlength="50"
                      show-word-limit
                      @focus="showTooltip('issuingBankSwift')"
                      @blur="hideTooltip">
                    </el-input>
                  </el-form-item>
                </el-col>
                <el-col :span="24">
                  <el-form-item label="开证行地址" prop="issuingBankAddress">
                    <el-input 
                      v-model="quotaInfo.issuingBankAddress" 
                      placeholder="请输入开证行地址" 
                      maxlength="200"
                      show-word-limit
                      @focus="showTooltip('issuingBankAddress')"
                      @blur="hideTooltip">
                    </el-input>
                  </el-form-item>
                </el-col>
                <el-col :span="24" :md="12">
                  <el-form-item label="出口商品名称" prop="exportProductName">
                    <el-input 
                      v-model="quotaInfo.exportProductName" 
                      placeholder="请输入出口商品名称" 
                      maxlength="100"
                      show-word-limit
                      @focus="showTooltip('exportProductName')"
                      @blur="hideTooltip">
                    </el-input>
                  </el-form-item>
                </el-col>
                <el-col :span="24" :md="12">
                  <el-form-item label="是否分批" prop="isPartialShipment">
                    <el-radio-group 
                      v-model="quotaInfo.isPartialShipment" 
                      @change="handlePartialShipmentChange"
                      @focus="showTooltip('isPartialShipment')"
                      @blur="hideTooltip">
                      <el-radio label="no">否</el-radio>
                      <el-radio label="yes">是</el-radio>
                    </el-radio-group>
                  </el-form-item>
                </el-col>
                
                <!-- 分批金额输入（条件显示） -->
                <transition name="slide-fade" mode="out-in">
                  <template v-if="quotaInfo.isPartialShipment === 'yes'">
                    <el-col :span="24" :md="12">
                      <el-form-item label="第一批金额" prop="firstBatchAmount">
                        <el-input-number 
                          v-model="quotaInfo.firstBatchAmount" 
                          :min="0" 
                          controls-position="right" 
                          style="width: 100%;"
                          placeholder="请输入第一批金额"
                          @focus="showTooltip('firstBatchAmount')"
                          @blur="hideTooltip">
                        </el-input-number>
                      </el-form-item>
                    </el-col>
                    <el-col :span="24" :md="12">
                      <el-form-item label="第二批金额" prop="secondBatchAmount">
                        <el-input-number 
                          v-model="quotaInfo.secondBatchAmount" 
                          :min="0" 
                          controls-position="right" 
                          style="width: 100%;"
                          placeholder="请输入第二批金额"
                          @focus="showTooltip('secondBatchAmount')"
                          @blur="hideTooltip">
                        </el-input-number>
                      </el-form-item>
                    </el-col>
                  </template>
                </transition>
              </template>
            </transition>
            
            <!-- 非信用证业务专用字段组 -->
            <transition name="slide-fade" mode="out-in">
              <template v-if="quotaInfo.type === 'NON_LC'">
                <el-col :span="24">
                  <el-divider content-position="left">非信用证业务信息</el-divider>
                </el-col>
                <el-col :span="24" :md="12">
                  <el-form-item label="出口商品名称" prop="exportProductNameNonLC">
                    <el-input 
                      v-model="quotaInfo.exportProductNameNonLC" 
                      placeholder="请输入出口商品名称" 
                      maxlength="100"
                      show-word-limit
                      @focus="showTooltip('exportProductNameNonLC')"
                      @blur="hideTooltip">
                    </el-input>
                  </el-form-item>
                </el-col>
                <el-col :span="24" :md="12">
                  <el-form-item label="是否需要翻译件" prop="needTranslation">
                    <el-radio-group 
                      v-model="quotaInfo.needTranslation"
                      @focus="showTooltip('needTranslation')"
                      @blur="hideTooltip">
                      <el-radio label="no">不需要</el-radio>
                      <el-radio label="yes">需要</el-radio>
                    </el-radio-group>
                  </el-form-item>
                </el-col>
              </template>
            </transition>
            
            <!-- 历史交易信息折叠面板 -->
            <el-col :span="24">
              <el-collapse v-model="activeCollapse">
                <el-collapse-item name="history">
                  <template slot="title">
                    <i class="header-icon el-icon-info"></i>
                    历史交易信息
                  </template>
                  <el-row :gutter="20">
                    <el-col :span="24" :md="12">
                      <el-form-item label="是否存在历史交易" prop="hasHistory">
                        <el-radio-group 
                          v-model="quotaInfo.hasHistory" 
                          @change="handleHistoryChange"
                          @focus="showTooltip('hasHistory')"
                          @blur="hideTooltip">
                          <el-radio label="no">否</el-radio>
                          <el-radio label="yes">是</el-radio>
                        </el-radio-group>
                      </el-form-item>
                    </el-col>
                    
                    <transition name="slide-fade" mode="out-in">
                      <template v-if="quotaInfo.hasHistory === 'yes'">
                        <el-col :span="24" :md="12">
                          <el-form-item label="最早成交年份" prop="earliestYear">
                            <el-input 
                              v-model="quotaInfo.earliestYear" 
                              placeholder="请输入最早成交年份 如:2019" 
                              maxlength="4"
                              @focus="showTooltip('earliestYear')"
                              @blur="hideTooltip">
                            </el-input>
                          </el-form-item>
                        </el-col>
                        
                        <el-col :span="24">
                          <el-form-item label="交易金额组" required>
                            <el-row :gutter="20">
                              <el-col :span="24" :md="8">
                                <el-form-item prop="lcAmount">
                                  <el-input 
                                    v-model="formattedLcAmount" 
                                    placeholder="L/C交易金额"
                                    @focus="showTooltip('lcAmount')"
                                    @blur="hideTooltip">
                                    <template slot="prepend">L/C</template>
                                  </el-input>
                                </el-form-item>
                              </el-col>
                              <el-col :span="24" :md="8">
                                <el-form-item prop="dpAmount">
                                  <el-input 
                                    v-model="formattedDpAmount" 
                                    placeholder="D/P交易金额"
                                    @focus="showTooltip('dpAmount')"
                                    @blur="hideTooltip">
                                    <template slot="prepend">D/P</template>
                                  </el-input>
                                </el-form-item>
                              </el-col>
                              <el-col :span="24" :md="8">
                                <el-form-item prop="daOaAmount">
                                  <el-input 
                                    v-model="formattedDaOaAmount" 
                                    placeholder="D/A&OA交易金额"
                                    @focus="showTooltip('daOaAmount')"
                                    @blur="hideTooltip">
                                    <template slot="prepend">D/A&OA</template>
                                  </el-input>
                                </el-form-item>
                              </el-col>
                            </el-row>
                          </el-form-item>
                        </el-col>
                        
                        <transition name="slide-fade" mode="out-in">
                          <template v-if="quotaInfo.type === 'credit'">
                            <el-col :span="24" :md="12">
                              <el-form-item label="银行付款表现" prop="bankPayment">
                                <el-select 
                                  v-model="quotaInfo.bankPayment" 
                                  placeholder="请选择银行付款表现" 
                                  style="width: 100%;"
                                  @focus="showTooltip('bankPayment')"
                                  @blur="hideTooltip">
                                  <el-option label="及时" value="timely"></el-option>
                                  <el-option label="尚可" value="okay"></el-option>
                                  <el-option label="较慢" value="slow"></el-option>
                                </el-select>
                              </el-form-item>
                            </el-col>
                          </template>
                        </transition>
                        
                        <el-col :span="24" :md="12">
                          <el-form-item label="买方付款表现" prop="buyerPayment">
                            <el-select 
                              v-model="quotaInfo.buyerPayment" 
                              placeholder="请选择买方付款表现" 
                              style="width: 100%;"
                              @focus="showTooltip('buyerPayment')"
                              @blur="hideTooltip">
                              <el-option label="及时" value="timely"></el-option>
                              <el-option label="不及时" value="untimely"></el-option>
                            </el-select>
                          </el-form-item>
                        </el-col>
                        
                        <el-col :span="24" :md="12">
                          <el-form-item label="是否有拖欠历史" prop="hasArrears">
                            <el-radio-group 
                              v-model="quotaInfo.hasArrears" 
                              @change="handleArrearsChange"
                              @focus="showTooltip('hasArrears')"
                              @blur="hideTooltip">
                              <el-radio label="no">无</el-radio>
                              <el-radio label="yes">有</el-radio>
                            </el-radio-group>
                          </el-form-item>
                        </el-col>
                        
                        <transition name="slide-fade" mode="out-in">
                          <template v-if="quotaInfo.hasArrears === 'yes'">
                            <el-col :span="24" :md="12">
                              <el-form-item label="拖欠金额" prop="arrearsAmount">
                                <el-input 
                                  v-model="formattedArrearsAmount" 
                                  placeholder="请输入拖欠金额"
                                  @focus="showTooltip('arrearsAmount')"
                                  @blur="hideTooltip">
                                </el-input>
                              </el-form-item>
                            </el-col>
                            <el-col :span="24" :md="12">
                              <el-form-item label="拖欠天数" prop="arrearsDays">
                                <el-input 
                                  v-model="quotaInfo.arrearsDays" 
                                  placeholder="请输入拖欠天数"
                                  @focus="showTooltip('arrearsDays')"
                                  @blur="hideTooltip">
                                </el-input>
                              </el-form-item>
                            </el-col>
                          </template>
                        </transition>
                      </template>
                    </transition>
                  </el-row>
                </el-collapse-item>
              </el-collapse>
            </el-col>
          </el-row>
        </el-form>
      </el-card>
    </div>

    <!-- 底部：操作按钮区域 -->
    <el-card class="bottom-card">
      <div class="button-group">
        <el-button 
          type="primary" 
          @click="submitForm"
          :loading="submitLoading"
          :disabled="formStatus === 'submitting'">
          {{ submitLoading ? '提交中...' : '提交申请' }}
        </el-button>
        <el-button 
          @click="saveAsDraft"
          :loading="draftLoading"
          :disabled="formStatus === 'saving'">
          {{ draftLoading ? '保存中...' : '保存草稿' }}
        </el-button>
        <el-button 
          @click="resetForm"
          plain>
          重置
        </el-button>
        <el-button 
          type="text" 
          @click="cancel">
          返回
        </el-button>
      </div>
    </el-card>

    <!-- 字段提示信息弹窗 
    <el-dialog :visible.sync="tooltipVisible" :title="currentTooltip.title" width="400px">
      <p>{{ currentTooltip.content }}</p>
      <span slot="footer" class="dialog-footer">
        <el-button @click="tooltipVisible = false">关闭</el-button>
      </span>
    </el-dialog>
    -->
  </div>
</template>

<script>
export default {
  name: 'QuotaInfo',
  data() {
    // 自定义校验规则
    const validateAmount = (rule, value, callback) => {
      if (value && !/^\d+$/.test(value)) {
        callback(new Error('金额必须为整数'));
      } else {
        callback();
      }
    };

    const validateTransactionAmount = (rule, value, callback) => {
      if (this.quotaInfo.hasHistory === 'yes' && 
          !this.quotaInfo.lcAmount && 
          !this.quotaInfo.dpAmount && 
          !this.quotaInfo.daOaAmount) {
        callback(new Error('历史交易存在时，请至少填写一项交易金额'));
      } else {
        callback();
      }
    };

    const validateProvince = (rule, value, callback) => {
      if (this.buyerInfo.country === 'China' && !value) {
        callback(new Error('中国买家必须选择省份'));
      } else {
        callback();
      }
    };

    // 添加年份校验规则
    const validateYear = (rule, value, callback) => {
      if (value && !/^\d{4}$/.test(value)) {
        callback(new Error('年份格式不正确，请输入四位数字年份'));
      } else {
        callback();
      }
    };

    return {
      operationType: 'new',
      activeCollapse: [], // 控制折叠面板展开
      tooltipVisible: false,
      currentTooltip: {
        title: '',
        content: ''
      },
      buyerInfo: {
        name: '',
        code: '',
        contact: '',
        phone: '',
        address: '',
        country: '',
        registrationNumber: '',
        province: ''
      },
      quotaInfo: {
        type: '',
        currency: 'CNY',
        amount: 0,
        startDate: '',
        endDate: '',
        reason: '',
        // 信用证相关字段
        lcNumber: '',
        isRevocable: '',
        issuingBankName: '',
        issuingBankCountry: '',
        issuingBankSwift: '',
        issuingBankAddress: '',
        exportProductName: '',
        isPartialShipment: '',
        firstBatchAmount: 0,
        secondBatchAmount: 0,
        // 非信用证业务相关字段
        exportProductNameNonLC: '',
        needTranslation: '0',
        // 历史交易信息字段
        hasHistory: '0',
        earliestYear: '',
        lcAmount: '',
        dpAmount: '',
        daOaAmount: '',
        bankPayment: '',
        buyerPayment: '',
        hasArrears: '0',
        arrearsAmount: '',
        arrearsDays: ''
      },
      buyerRules: {
        name: [
          { required: true, message: '请输入买方名称', trigger: 'blur' },
          { max: 600, message: '长度不能超过600个字符', trigger: 'blur' }
        ],
        country: [
          { required: true, message: '请选择买方国家/地区', trigger: 'change' }
        ],
        registrationNumber: [
          { required: true, message: '请输入买方注册号', trigger: 'blur' },
          { max: 100, message: '长度不能超过100个字符', trigger: 'blur' }
        ],
        address: [
          { required: true, message: '请输入买方地址', trigger: 'blur' },
          { max: 300, message: '长度不能超过300个字符', trigger: 'blur' }
        ],
        province: [
          { validator: validateProvince, trigger: 'change' }
        ]
      },
      quotaRules: {
        type: [
          { required: true, message: '请选择限额类型', trigger: 'change' }
        ],
        currency: [
          { required: true, message: '请选择币种', trigger: 'change' }
        ],
        amount: [
          { required: true, message: '请输入申请金额', trigger: 'blur' },
          { validator: validateAmount, trigger: 'blur' }
        ],
        startDate: [
          { required: true, message: '请选择生效日期', trigger: 'change' }
        ],
        endDate: [
          { required: true, message: '请选择失效日期', trigger: 'change' }
        ],
        reason: [
          { required: true, message: '请输入申请理由', trigger: 'blur' }
        ],
        lcNumber: [
          { required: true, message: '请输入信用证号', trigger: 'blur' },
          { max: 50, message: '长度不能超过50个字符', trigger: 'blur' }
        ],
        isRevocable: [
          { required: true, message: '请选择是否循环使用', trigger: 'change' }
        ],
        issuingBankName: [
          { required: true, message: '请输入开证行名称', trigger: 'blur' },
          { max: 150, message: '长度不能超过150个字符', trigger: 'blur' }
        ],
        issuingBankCountry: [
          { required: true, message: '请选择开证行国家/地区', trigger: 'change' }
        ],
        issuingBankSwift: [
          { required: true, message: '请输入开证行SWIFT', trigger: 'blur' },
          { max: 50, message: '长度不能超过50个字符', trigger: 'blur' }
        ],
        issuingBankAddress: [
          { required: true, message: '请输入开证行地址', trigger: 'blur' },
          { max: 200, message: '长度不能超过200个字符', trigger: 'blur' }
        ],
        exportProductName: [
          { required: true, message: '请输入出口商品名称', trigger: 'blur' },
          { max: 100, message: '长度不能超过100个字符', trigger: 'blur' }
        ],
        isPartialShipment: [
          { required: true, message: '请选择是否分批', trigger: 'change' }
        ],
        firstBatchAmount: [
          { required: true, message: '请输入第一批金额', trigger: 'blur' },
          { validator: validateAmount, trigger: 'blur' }
        ],
        secondBatchAmount: [
          { required: true, message: '请输入第二批金额', trigger: 'blur' },
          { validator: validateAmount, trigger: 'blur' }
        ],
        exportProductNameNonLC: [
          { required: true, message: '请输入出口商品名称', trigger: 'blur' },
          { max: 100, message: '长度不能超过100个字符', trigger: 'blur' }
        ],
        needTranslation: [
          { required: true, message: '请选择是否需要翻译件', trigger: 'change' }
        ],
        hasHistory: [
          { required: true, message: '请选择是否存在历史交易', trigger: 'change' }
        ],
        earliestYear: [
          { required: true, message: '请输入最早成交年份', trigger: 'blur' },
          { validator: validateYear, trigger: 'blur' }
        ],
        lcAmount: [
          { validator: validateTransactionAmount, trigger: 'blur' },
          { validator: validateAmount, trigger: 'blur' }
        ],
        dpAmount: [
          { validator: validateAmount, trigger: 'blur' }
        ],
        daOaAmount: [
          { validator: validateAmount, trigger: 'blur' }
        ],
        bankPayment: [
          { required: true, message: '请选择银行付款表现', trigger: 'change' }
        ],
        buyerPayment: [
          { required: true, message: '请选择买方付款表现', trigger: 'change' }
        ],
        hasArrears: [
          { required: true, message: '请选择是否有拖欠历史', trigger: 'change' }
        ],
        arrearsAmount: [
          { required: true, message: '请输入拖欠金额', trigger: 'blur' },
          { validator: validateAmount, trigger: 'blur' }
        ],
        arrearsDays: [
          { required: true, message: '请输入拖欠天数', trigger: 'blur' },
          { pattern: /^\d+$/, message: '天数必须为整数', trigger: 'blur' }
        ]
      },
      formStatus: 'initial', // initial, validating, submitting, success, failed
      submitLoading: false,
      draftLoading: false
    }
  },
  computed: {
    showProvinceField() {
      return this.buyerInfo.country === 'China';
    },
    // 判断交易金额组是否至少填写一项
    isTransactionAmountRequired() {
      return !(this.quotaInfo.lcAmount || this.quotaInfo.dpAmount || this.quotaInfo.daOaAmount);
    },
    // 格式化金额显示（千分位）
    formattedLcAmount: {
      get() {
        return this.formatAmount(this.quotaInfo.lcAmount);
      },
      set(value) {
        this.quotaInfo.lcAmount = this.parseAmount(value);
      }
    },
    formattedDpAmount: {
      get() {
        return this.formatAmount(this.quotaInfo.dpAmount);
      },
      set(value) {
        this.quotaInfo.dpAmount = this.parseAmount(value);
      }
    },
    formattedDaOaAmount: {
      get() {
        return this.formatAmount(this.quotaInfo.daOaAmount);
      },
      set(value) {
        this.quotaInfo.daOaAmount = this.parseAmount(value);
      }
    },
    formattedArrearsAmount: {
      get() {
        return this.formatAmount(this.quotaInfo.arrearsAmount);
      },
      set(value) {
        this.quotaInfo.arrearsAmount = this.parseAmount(value);
      }
    }
  },
  methods: {
    formatAmount(value) {
      if (!value) return '';
      // 移除所有非数字和小数点字符
      const cleanValue = value.toString().replace(/[^\d.]/g, '');
      // 分离整数和小数部分
      const parts = cleanValue.split('.');
      // 格式化整数部分为千分位
      parts[0] = parts[0].replace(/\B(?=(\d{3})+(?!\d))/g, ',');
      // 重新组合
      return parts.join('.');
    },
    parseAmount(value) {
      if (!value) return '';
      // 移除所有逗号
      return value.toString().replace(/,/g, '');
    },
    handleCountryChange() {
      // 当国家改变时，清空省份选择
      this.buyerInfo.province = '';
    },
    // 处理是否分批变化
    handlePartialShipmentChange() {
      // 当切换分批选项时，清空批次金额
      this.quotaInfo.firstBatchAmount = 0;
      this.quotaInfo.secondBatchAmount = 0;
    },
    // 处理历史交易变化
    handleHistoryChange(value) {
      if (value === 'yes') {
        // 展开折叠面板
        if (!this.activeCollapse.includes('history')) {
          this.activeCollapse.push('history');
        }
      } else {
        // 清空历史交易相关字段
        this.quotaInfo.earliestYear = '';
        this.quotaInfo.lcAmount = '';
        this.quotaInfo.dpAmount = '';
        this.quotaInfo.daOaAmount = '';
        this.quotaInfo.bankPayment = '';
        this.quotaInfo.buyerPayment = '';
        this.quotaInfo.hasArrears = 'no';
        this.quotaInfo.arrearsAmount = '';
        this.quotaInfo.arrearsDays = '';
      }
    },
    // 处理拖欠历史变化
    handleArrearsChange(value) {
      if (value === 'no') {
        // 清空拖欠详情
        this.quotaInfo.arrearsAmount = '';
        this.quotaInfo.arrearsDays = '';
      }
    },
    showTooltip(field) {
      const tooltips = {
        name: { title: '买方名称', content: '请输入买方的完整公司名称，需使用英文大写' },
        code: { title: '买方代码', content: '请输入买方在系统中的唯一识别代码' },
        country: { title: '买方国家/地区', content: '请选择买方所在的国家或地区' },
        registrationNumber: { title: '买方注册号', content: '请输入买方在当地的工商注册号码' },
        address: { title: '买方地址', content: '请输入买方的详细地址，需使用英文大写' },
        province: { title: '买方所属省份', content: '请选择买方所在的省份' },
        phone: { title: '联系电话', content: '请输入买方的有效联系电话' },
        contact: { title: '联系人', content: '请输入买方的联系人姓名' },
        quotaType: { title: '限额类型', content: '请选择申请的限额类型' },
        currency: { title: '币种', content: '请选择申请金额的币种' },
        amount: { title: '申请金额', content: '请输入申请的限额金额' },
        startDate: { title: '生效日期', content: '请选择限额的生效日期' },
        endDate: { title: '失效日期', content: '请选择限额的失效日期' },
        reason: { title: '申请理由', content: '请详细说明申请限额的理由' },
        lcNumber: { title: '信用证号', content: '请输入信用证的编号' },
        isRevocable: { title: '是否循环使用', content: '请选择信用证是否可以循环使用' },
        issuingBankName: { title: '开证行名称', content: '请输入开证银行的完整名称，需使用英文大写' },
        issuingBankCountry: { title: '开证行国家/地区', content: '请选择开证银行所在的国家或地区' },
        issuingBankSwift: { title: '开证行SWIFT', content: '请输入开证银行的SWIFT代码' },
        issuingBankAddress: { title: '开证行地址', content: '请输入开证银行的详细地址' },
        exportProductName: { title: '出口商品名称', content: '请输入将要出口的商品名称' },
        isPartialShipment: { title: '是否分批', content: '请选择是否需要分批装运' },
        firstBatchAmount: { title: '第一批金额', content: '请输入第一批装运的金额' },
        secondBatchAmount: { title: '第二批金额', content: '请输入第二批装运的金额' },
        exportProductNameNonLC: { title: '出口商品名称', content: '请输入将要出口的商品名称' },
        needTranslation: { title: '是否需要翻译件', content: '请选择是否需要提供文件翻译件' },
        hasHistory: { title: '是否存在历史交易', content: '请选择与该买方是否存在历史交易记录' },
        earliestYear: { title: '最早成交年份', content: '请输入与该买方最早的交易年份' },
        lcAmount: { title: 'L/C交易金额', content: '请输入信用证方式交易的累计金额' },
        dpAmount: { title: 'D/P交易金额', content: '请输入付款交单方式交易的累计金额' },
        daOaAmount: { title: 'D/A&OA交易金额', content: '请输入承兑交单和赊账方式交易的累计金额' },
        bankPayment: { title: '银行付款表现', content: '请选择开证行的付款表现' },
        buyerPayment: { title: '买方付款表现', content: '请选择买方的付款表现' },
        hasArrears: { title: '是否有拖欠历史', content: '请选择买方是否存在拖欠款项的历史' },
        arrearsAmount: { title: '拖欠金额', content: '请输入买方拖欠的金额' },
        arrearsDays: { title: '拖欠天数', content: '请输入买方拖欠的天数' }
      };
      
      if (tooltips[field]) {
        this.currentTooltip = tooltips[field];
        this.tooltipVisible = true;
      }
    },
    hideTooltip() {
      // 延迟隐藏，避免点击提示框时立即消失
      setTimeout(() => {
        this.tooltipVisible = false;
      }, 200);
    },
    async submitForm() {
      this.formStatus = 'validating';
      
      try {
        // 验证两个表单
        await Promise.all([
          this.$refs.buyerForm.validate(),
          this.$refs.quotaForm.validate()
        ]);
        
        this.formStatus = 'submitting';
        this.submitLoading = true;
        
        // 格式化数据以符合要求
        const formattedData = {
          buyerInfo: {
            ...this.buyerInfo,
            // 确保金额为整数
            registrationNumber: parseInt(this.buyerInfo.registrationNumber)
          },
          quotaInfo: {
            ...this.quotaInfo,
            // 格式化数据
            type: this.quotaInfo.type === 'credit' ? 'LC' : this.quotaInfo.type,
            // 日期格式化为 YYYY-MM-DD
            startDate: this.quotaInfo.startDate ? this.$moment(this.quotaInfo.startDate).format('YYYY-MM-DD') : '',
            endDate: this.quotaInfo.endDate ? this.$moment(this.quotaInfo.endDate).format('YYYY-MM-DD') : '',
            // 布尔值转换为字符串 0/1
            isRevocable: this.quotaInfo.isRevocable === 'revocable' ? '1' : '0',
            isPartialShipment: this.quotaInfo.isPartialShipment === 'yes' ? '1' : '0',
            needTranslation: this.quotaInfo.needTranslation === 'yes' ? '1' : '0',
            hasHistory: this.quotaInfo.hasHistory === 'yes' ? '1' : '0',
            hasArrears: this.quotaInfo.hasArrears === 'yes' ? '1' : '0',
            // 金额转换为整数
            amount: this.quotaInfo.amount ? parseInt(this.quotaInfo.amount) : 0,
            firstBatchAmount: this.quotaInfo.firstBatchAmount ? parseInt(this.quotaInfo.firstBatchAmount) : 0,
            secondBatchAmount: this.quotaInfo.secondBatchAmount ? parseInt(this.quotaInfo.secondBatchAmount) : 0,
            lcAmount: this.quotaInfo.lcAmount ? parseInt(this.parseAmount(this.quotaInfo.lcAmount)) : '',
            dpAmount: this.quotaInfo.dpAmount ? parseInt(this.parseAmount(this.quotaInfo.dpAmount)) : '',
            daOaAmount: this.quotaInfo.daOaAmount ? parseInt(this.parseAmount(this.quotaInfo.daOaAmount)) : '',
            arrearsAmount: this.quotaInfo.arrearsAmount ? parseInt(this.parseAmount(this.quotaInfo.arrearsAmount)) : '',
            arrearsDays: this.quotaInfo.arrearsDays ? parseInt(this.quotaInfo.arrearsDays) : ''
          }
        };
        
        // 模拟异步提交过程
        await new Promise(resolve => setTimeout(resolve, 2000));
        
        this.formStatus = 'success';
        this.submitLoading = false;
        this.$message.success('申请已提交');
        console.log('Formatted Data:', formattedData);
      } catch (error) {
        this.formStatus = 'failed';
        this.submitLoading = false;
        this.$message.error('请检查表单填写是否正确');
      }
    },
    async saveAsDraft() {
      this.draftLoading = true;
      
      try {
        // 模拟保存草稿的异步过程
        await new Promise(resolve => setTimeout(resolve, 1000));
        
        // 保存到本地存储
        const draftData = {
          buyerInfo: this.buyerInfo,
          quotaInfo: this.quotaInfo,
          timestamp: new Date().toISOString()
        };
        
        localStorage.setItem('quotaApplicationDraft', JSON.stringify(draftData));
        this.draftLoading = false;
        this.$message.success('草稿已保存');
      } catch (error) {
        this.draftLoading = false;
        this.$message.error('保存草稿失败');
      }
    },
    resetForm() {
      this.$confirm('确定要重置表单吗？这将清除所有已填写的内容', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.$refs.buyerForm.resetFields();
        this.$refs.quotaForm.resetFields();
        this.activeCollapse = [];
        this.formStatus = 'initial';
        this.$message.success('表单已重置');
      }).catch(() => {
        // 用户取消重置
      });
    },
    cancel() {
      this.$router.go(-1);
    },
    loadDraft() {
      const savedDraft = localStorage.getItem('quotaApplicationDraft');
      if (savedDraft) {
        try {
          const draftData = JSON.parse(savedDraft);
          this.buyerInfo = draftData.buyerInfo;
          this.quotaInfo = draftData.quotaInfo;
          this.$message.info('已加载上次保存的草稿');
        } catch (error) {
          this.$message.error('加载草稿失败');
        }
      }
    }
  },
  mounted() {
    // 页面加载时尝试加载草稿
    this.loadDraft();
  }
}
</script>

<style lang="scss" scoped>
.quota-info-container {
  padding: 20px;
  background-color: #f5f7fa;
  min-height: calc(100vh - 84px);

  .top-card {
    margin-bottom: 20px;

    .header-section {
      display: flex;
      justify-content: space-between;
      align-items: center;
      flex-wrap: wrap;
      gap: 15px;

      .page-title {
        margin: 0;
        color: #303133;
      }

      .operation-selector {
        display: flex;
        align-items: center;
      }
    }
  }

  .content-wrapper {
    display: flex;
    flex-wrap: wrap;
    gap: 20px;
    margin-bottom: 20px;

    .info-card {
      flex: 1;
      min-width: 300px;

      .card-header {
        font-weight: bold;
        color: #606266;
      }

      .info-form {
        margin-top: 20px;
        
        // 为必填项添加红色星号
        ::v-deep .el-form-item.is-required .el-form-item__label:before {
          content: '*';
          color: #f56c6c;
          margin-right: 4px;
        }
      }
    }

    .buyer-card {
      flex: 1;
    }

    .quota-card {
      flex: 1;
    }
  }

  .bottom-card {
    .button-group {
      display: flex;
      justify-content: center;
      gap: 20px;
      padding: 20px 0;
      flex-wrap: wrap;
    }
  }

  // 淡入淡出动画
  .fade-enter-active, .fade-leave-active {
    transition: opacity 0.3s;
  }
  .fade-enter, .fade-leave-to {
    opacity: 0;
  }

  // 滑动淡入淡出动画
  .slide-fade-enter-active, .slide-fade-leave-active {
    transition: all 0.3s ease;
  }
  .slide-fade-enter, .slide-fade-leave-to {
    transform: translateX(10px);
    opacity: 0;
  }

  // 移动端适配
  @media (max-width: 768px) {
    padding: 10px;

    .content-wrapper {
      flex-direction: column;

      .info-card {
        min-width: 100%;
      }
    }

    .top-card {
      .header-section {
        flex-direction: column;
        align-items: flex-start;
      }
    }
    
    .button-group {
      flex-direction: column;
      gap: 10px;
    }
  }
}