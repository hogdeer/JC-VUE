<template>
  <div class="add-sea-form">
    <!-- 表单标题 -->
    <el-card class="box-card">
      <div slot="header" class="clearfix">
        <span>上传单票下单模板</span>
      </div>
      <el-row :gutter="20">
        <el-col :span="8">
          <el-button type="primary">选择文件</el-button>
          <el-button type="primary">单票导入模板</el-button>
          <el-button type="primary">批量导入模板(新)</el-button>
        </el-col>
      </el-row>
    </el-card>

    <!-- 渠道选择 -->
    <el-card class="box-card">
      <div slot="header" class="clearfix">
        <span>渠道选择</span>
      </div>
      <el-form ref="form" :model="form" label-width="150px">

        <el-row :gutter="50">
          <el-col :span="12">
            <!-- <el-row>
              <el-col >
                <el-form-item label="渠道">
                  <el-select v-model="form.channel" placeholder="请选择">
                    <el-option label="选项一" value="1"></el-option>
                    <el-option label="选项二" value="2"></el-option>
                  </el-select>
                </el-form-item>
              </el-col>
            </el-row> -->

            <el-row>
              <el-col >
                <el-form-item label="交货地">
                  <el-select v-model="form.deliveryPlace" placeholder="请选择" style="width: 100%;">
                    <el-option label="深圳" value="1"></el-option>
                    <el-option label="广州" value="2"></el-option>
                    <el-option label="上海" value="3"></el-option>
                     <el-option label="宁波" value="4"></el-option>
                     <el-option label="厦门" value="5"></el-option>
                  </el-select>
                </el-form-item>
              </el-col>
            </el-row>

          <el-row >
            <el-col >
              <el-form-item label="目的国家">
                <el-select v-model="form.destinationCountry" placeholder="请选择" style="width: 100%;">
                  <el-option label="美国" value="1"></el-option>
                  <el-option label="加拿大" value="2"></el-option>
                  <el-option label="英国" value="3"></el-option>
                  <el-option label="德国" value="4"></el-option>
                  <el-option label="法国" value="5"></el-option>
                </el-select>
              </el-form-item>
            </el-col>
        </el-row>
        <el-row >
          <el-col >
            <el-form-item label="目的仓库类型">
              <el-radio-group v-model="form.warehouseType">
                <el-radio label="FBA">FBA</el-radio>
                <el-radio label="电商平台仓">电商平台仓 
                  <el-tooltip class="item" effect="light" content="沃尔玛/Cdiscount/Wayfair/Allegro/菜鸟海外仓/京东仓等" placement="top-start">
                    <i class="el-icon-question" style="color: #E6A23C;"></i>
                  </el-tooltip>
                </el-radio>
                <el-radio label="私人地址">私人地址</el-radio>
                <el-radio label="第三方仓">第三方仓</el-radio>
                <el-radio label="九方头程仓">九方头程仓</el-radio>
                <el-radio label="九方云仓">九方云仓</el-radio>
              </el-radio-group>
            </el-form-item>
          </el-col>
        </el-row>
        <el-row >
          <el-col :span="8" >
            <el-form-item label="实重(KG)" >
              <el-input v-model="form.actualWeight"></el-input>
            </el-form-item>
          </el-col>
          
        <!-- </el-row>
        <el-row :gutter="20"> -->
          <el-col :span="8">
            <el-form-item label="箱数" label-width="80px">
              <el-input v-model="form.boxCount"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item label="体积(CBM)" label-width="120px">
              <el-input v-model="form.volume"></el-input>
            </el-form-item>
          </el-col>
        </el-row>

        <div v-if="form.warehouseType === '电商平台仓'">
        <el-row  >
          <el-col >
            <el-form-item label="仓库公司地址">
              <el-input v-model="form.deliveryAddress"></el-input>
            </el-form-item>
          </el-col>
        </el-row>
        </div>

        <el-row >
          <el-col >
            <el-form-item label="派送地址">
              <el-input v-model="form.deliveryAddress"></el-input>
            </el-form-item>
          </el-col>
        </el-row>

        <div v-if="form.warehouseType === '私人地址'">
        <el-row >
          <el-col >
            <el-form-item label="谷歌地址搜索:">
            <el-input v-model="addressForm.googleAddress" placeholder="请输入地址"></el-input>
          </el-form-item>
        </el-col>
      </el-row>

      <!-- 常用派送地址 和 保存为常用派送地址 -->
      <el-row >
        <el-col >
          <el-form-item>
            <el-button type="primary" @click="selectCommonAddress">常用派送地址</el-button>
            <el-button type="primary" @click="saveAsCommonAddress">保存为常用派送地址</el-button>
          </el-form-item>
        </el-col>
      </el-row>

      <!-- 公司名/个人 -->
      <el-row >
        <el-col >
          <el-form-item label="公司名/个人:" prop="companyOrPersonal">
            <el-input v-model="addressForm.companyOrPersonal" placeholder="请输入公司名或个人名"></el-input>
          </el-form-item>
        </el-col>
      </el-row>

      <!-- 联系人 -->
      <el-row >
        <el-col >
      <el-form-item label="联系人:" prop="contactPerson">
        <el-input v-model="addressForm.contactPerson" placeholder="请输入联系人"></el-input>
      </el-form-item>
    </el-col>
  </el-row>
  <el-row >
    <el-col >
      <!-- 联系电话 -->
      <el-form-item label="联系电话:" prop="contactPhone">
        <el-input v-model="addressForm.contactPhone" placeholder="请输入联系电话"></el-input>
      </el-form-item>
    </el-col>
  </el-row>
  <el-row >
    <el-col >

      <!-- 邮箱 -->
      <el-form-item label="邮箱:" prop="email">
        <el-input v-model="addressForm.email" placeholder="请输入邮箱"></el-input>
      </el-form-item>
    </el-col>
  </el-row>
  <el-row >
    <el-col >
      <!-- 州/省 -->
      <el-form-item label="州/省:" prop="stateProvince">
        <el-input v-model="addressForm.stateProvince" placeholder="请输入州或省"></el-input>
      </el-form-item>
    </el-col>
  </el-row>
  <el-row >
    <el-col >
      <!-- 城市 -->
      <el-form-item label="城市:" prop="city">
        <el-input v-model="addressForm.city" placeholder="请输入城市"></el-input>
      </el-form-item>
    </el-col>
  </el-row>
  <el-row >
    <el-col >
      <!-- 邮编 -->
      <el-form-item label="邮编:" prop="postalCode">
        <el-input v-model="addressForm.postalCode" placeholder="请输入邮编"></el-input>
      </el-form-item>
    </el-col>
  </el-row>
  <el-row >
    <el-col >
      <!-- 详细地址1 -->
      <el-form-item label="详细地址1:" prop="detailAddress1">
        <el-input v-model="addressForm.detailAddress1" placeholder="请输入详细地址1"></el-input>
      </el-form-item>
    </el-col>
  </el-row>
  <el-row >
    <el-col >
      <!-- 详细地址2 -->
      <el-form-item label="详细地址2:" prop="detailAddress2">
        <el-input v-model="addressForm.detailAddress2" placeholder="请输入详细地址2（非必填）"></el-input>
      </el-form-item>
    </el-col>
  </el-row>
</div>

        <el-row v-if="form.warehouseType !== 'FBA' && form.warehouseType !== '九方云仓' && form.warehouseType !== '九方头程仓'">
          <el-col >
            <el-form-item label="入仓号/入仓预约链接">
              <el-input v-model="form.deliveryAddress"></el-input>
            </el-form-item>
          </el-col>
        </el-row>

        <el-row v-if="form.warehouseType !== 'FBA' && form.warehouseType !== '九方云仓' && form.warehouseType !== '九方头程仓'">
          <el-col >
            <el-form-item label="派送文件">
              <el-upload
                class="upload-demo"
                action="https://jsonplaceholder.typicode.com/posts/"
                :on-preview="handlePreview"
                :on-remove="handleRemove"
                :before-remove="beforeRemove"
                multiple
                :limit="3"
                :on-exceed="handleExceed"
                :file-list="form.fileList">
                <el-button size="small" type="primary">点击上传</el-button>
                <!-- <div slot="tip" class="el-upload__tip">只能上传jpg/png文件，且不超过500kb</div> -->
              </el-upload>
          </el-form-item>
          </el-col>
        </el-row>

        <el-row >
          <el-col >
            <el-form-item label="渠道能力">
              <el-checkbox-group v-model="form.channelCapabilities">
                <el-checkbox label="普货（无任何电池）"></el-checkbox>
                <el-checkbox label="内置锂离子电池"></el-checkbox>
                <el-checkbox label="配置锂离子电池"></el-checkbox>
                <el-checkbox label="内置纽扣电池"></el-checkbox>
                <el-checkbox label="小磁性（普货须加磁检费）"></el-checkbox>

                <el-checkbox label="内置或配套干电池"></el-checkbox>
                <el-checkbox label="少量液体"></el-checkbox>
                <el-checkbox label="小磁性（普货渠道加磁检费） "></el-checkbox>
                <el-checkbox label="其他（粉末/膏状）"></el-checkbox>
                <el-checkbox label="电子烟"></el-checkbox>
                <el-checkbox label="纯干电池"></el-checkbox>
                <el-checkbox label="刀具"></el-checkbox>
                <el-checkbox label="配套纽扣电池"></el-checkbox>
                <el-checkbox label="带电(产品有空运运输鉴定报告)"></el-checkbox>

                <el-checkbox label="粉末/液体/膏体（有空运运输鉴定报告）"></el-checkbox>
                <el-checkbox label="刀具（有备案）"></el-checkbox>
                <!-- 其他渠道能力选项 -->
              </el-checkbox-group>
            </el-form-item>
          </el-col>
        </el-row>
        <el-row >
          <el-col >
            <el-form-item label="是否包税">
              <el-radio-group v-model="form.isTaxIncluded">
                <el-radio label="是">是</el-radio>
                <el-radio label="否">否</el-radio>
              </el-radio-group>
            </el-form-item>
          </el-col>
        </el-row>
        <el-row >
          <el-col >
            <el-form-item label="交货条款">
              <el-radio-group v-model="form.deliveryTerms">
                <el-radio label="DDP">DDP</el-radio>
                <el-radio label="DDU">DDU</el-radio>
              </el-radio-group>
            </el-form-item>
          </el-col>
        </el-row>
        <el-row >
          <el-col >
            <el-form-item label="是否报关">
              <el-radio-group v-model="form.isCustomsDeclaration">
                <el-radio label="是">是</el-radio>
                <el-radio label="否">否</el-radio>
              </el-radio-group>
            </el-form-item>
          </el-col>
        </el-row>
        <el-row >
          <el-col >
            <el-form-item label="渠道">
              <el-select v-model="form.channel" placeholder="请选择">
                <el-option label="选项一" value="1"></el-option>
                <el-option label="选项二" value="2"></el-option>
              </el-select>
            </el-form-item>
          </el-col>
        </el-row>
        <el-row >
          <el-col >
            <el-form-item label="国内分区">
              <el-select v-model="form.domesticArea" placeholder="请选择">
                <el-option label="选项一" value="1"></el-option>
                <el-option label="选项二" value="2"></el-option>
              </el-select>
            </el-form-item>
          </el-col>
        </el-row>
        <el-row >
          <el-col >
            <el-form-item label="预计计费量">
              <el-input v-model="form.expectedBillingVolume"></el-input>
            </el-form-item>
          </el-col>
        </el-row>
        <el-row >
          <el-col :span="12">
            <el-form-item label="交货方式">
              <el-radio-group v-model="form.deliveryMethod">
                <el-radio label="九方提货">九方提货</el-radio>
                <el-radio label="客户自送">客户自送</el-radio>
              </el-radio-group>
            </el-form-item>
          </el-col>
     
          <el-col :span="12">
            <el-form-item label="购买保险">
              <el-radio-group v-model="form.isInsurancePurchased">
                <el-radio label="是">是</el-radio>
                <el-radio label="否">否</el-radio>
              </el-radio-group>
            </el-form-item>
          </el-col>
        </el-row>
        <el-row >
          <el-col >
            <el-form-item label="入仓时间">
              <el-date-picker v-model="form.warehouseEntryTime" type="date" placeholder="选择日期"></el-date-picker>
            </el-form-item>
          </el-col>
        </el-row>
        <el-row >
          <el-col >
            <el-form-item label="备注">
              <el-input type="textarea" v-model="form.remarks"></el-input>
            </el-form-item>
          </el-col>
        </el-row>


          </el-col>

          <el-col :span="12">
             <!-- 发件人 -->
             <el-row>
              <el-col :span="24">
                <el-form-item label="参考号" label-width="150px">
                  <el-input v-model="form.referenceNumber"></el-input>
                </el-form-item>
              </el-col>
            </el-row>

            <div>
              <el-row>
                <el-col :span="24">
                  <el-form-item label="发件人公司(中)" label-width="150px">
                    <el-input v-model="form.senderCompanyZh"></el-input>
                  </el-form-item>
                </el-col>
              </el-row>
              <el-row>
                <el-col :span="24">
                  <el-form-item label="发件人公司(英)" label-width="150px">
                    <el-input v-model="form.senderCompanyEn"></el-input>
                  </el-form-item>
                </el-col>
              </el-row>
              <!-- <el-row>
                <el-col :span="24">
                  <el-form-item label="发件人公司(中)" label-width="150px">
                    <el-input v-model="form.senderCompanyZh"></el-input>
                  </el-form-item>
                </el-col>
              </el-row>-->
              <el-row> 
                <el-col :span="24">
                  <el-form-item label="发件人英文地址" label-width="150px">
                  <el-input v-model="form.senderAddressEn"></el-input>
                </el-form-item>
                </el-col>
              </el-row>
            </div>

          </el-col>
        </el-row>





       

      </el-form>
    </el-card>

    <!-- 清关发票 -->
    <el-card class="box-card">
      <div slot="header" class="clearfix">
        <span>清关发票  (请注意采购单价币种与申报币种不一致，采购单价为我司投保和理赔的实际依据，请谨慎填写) <label style="color: red;">采购单价默认均为RMB</label></span>
      </div>
      <div>
        
      </div>
      <!-- 清关发票相关表单内容 -->
      <!-- <vxe-table
      border
      stripe
      height="400"
      :loading="loading"
      :column-config="{resizable: true}"
      :row-config="{isHover: true}"
      :checkbox-config="{labelField: 'id', highlight: true, range: true}"
      :data="tableData">
      <vxe-column type="seq" width="70"></vxe-column>
      <vxe-column type="checkbox" title="ID" width="140"></vxe-column>
      <vxe-column field="name" title="Name" sortable></vxe-column>
      <vxe-column field="sex" title="Sex" :filters="sexOptions" :filter-multiple="false" :formatter="formatterSex"></vxe-column>
      <vxe-column field="age" title="Age" :filters="ageOptions" :filter-method="filterAgeMethod" sortable></vxe-column>
      <vxe-column field="address" title="Address" show-overflow></vxe-column>
    </vxe-table> -->


    <vxe-table
      border
      show-overflow
      show-footer
      keep-source
      height="600"
      ref="tableRef"
      :print-config="printConfig"
      :export-config="exportConfig"
      :column-config="columnConfig"
      :edit-config="editConfig"
      :data="tableData"
      @edit-closed="editClosedEvent">
      <vxe-column field="seq" type="seq" width="60"></vxe-column>
      <vxe-column field="summary" title="摘要" min-width="120" :edit-render="{ name: 'VxeInput' }"></vxe-column>
      <vxe-column field="subject" title="会计科目" min-width="180" :edit-render="{ name: 'VxeInput' }"></vxe-column>
      <vxe-colgroup field="debtorAmount" title="借方金额">
        <vxe-column field="debtorObj.p9" title="亿" width="60" align="center" :edit-render="{ name: 'VxeNumberInput', autoSelect: true, props:  { type: 'integer', max: 9, controls: false, maxLength: 1, align: 'center' } }"></vxe-column>
        <vxe-column field="debtorObj.p8" title="千" width="60" align="center" :edit-render="{ name: 'VxeNumberInput', autoSelect: true, props:  { type: 'integer', max: 9, controls: false, maxLength: 1, align: 'center' } }"></vxe-column>
        <vxe-column field="debtorObj.p7" title="百" width="60" align="center" :edit-render="{ name: 'VxeNumberInput', autoSelect: true, props:  { type: 'integer', max: 9, controls: false, maxLength: 1, align: 'center' } }"></vxe-column>
        <vxe-column field="debtorObj.p6" title="十" width="60" align="center" :edit-render="{ name: 'VxeNumberInput', autoSelect: true, props:  { type: 'integer', max: 9, controls: false, maxLength: 1, align: 'center' } }"></vxe-column>
        <vxe-column field="debtorObj.p5" title="万" width="60" align="center" :edit-render="{ name: 'VxeNumberInput', autoSelect: true, props:  { type: 'integer', max: 9, controls: false, maxLength: 1, align: 'center' } }"></vxe-column>
        <vxe-column field="debtorObj.p4" title="千" width="60" align="center" :edit-render="{ name: 'VxeNumberInput', autoSelect: true, props:  { type: 'integer', max: 9, controls: false, maxLength: 1, align: 'center' } }"></vxe-column>
        <vxe-column field="debtorObj.p3" title="百" width="60" align="center" :edit-render="{ name: 'VxeNumberInput', autoSelect: true, props:  { type: 'integer', max: 9, controls: false, maxLength: 1, align: 'center' } }"></vxe-column>
        <vxe-column field="debtorObj.p2" title="十" width="60" align="center" :edit-render="{ name: 'VxeNumberInput', autoSelect: true, props:  { type: 'integer', max: 9, controls: false, maxLength: 1, align: 'center' } }"></vxe-column>
        <vxe-column field="debtorObj.p1" title="元" width="60" align="center" :edit-render="{ name: 'VxeNumberInput', autoSelect: true, props:  { type: 'integer', max: 9, controls: false, maxLength: 1, align: 'center' } }"></vxe-column>
        <vxe-column field="debtorObj.m1" title="角" width="60" align="center" :edit-render="{ name: 'VxeNumberInput', autoSelect: true, props:  { type: 'integer', max: 9, controls: false, maxLength: 1, align: 'center' } }"></vxe-column>
        <vxe-column field="debtorObj.m2" title="分" width="60" align="center" :edit-render="{ name: 'VxeNumberInput', autoSelect: true, props:  { type: 'integer', max: 9, controls: false, maxLength: 1, align: 'center' } }"></vxe-column>
      </vxe-colgroup>
      <vxe-column field="x1" title="√" width="40"></vxe-column>
      <vxe-colgroup field="creditAmount" title="贷方金额">
        <vxe-column field="creditObj.p9" title="亿" width="60" align="center" :edit-render="{ name: 'VxeNumberInput', autoSelect: true, props:  { type: 'integer', max: 9, controls: false, maxLength: 1, align: 'center' } }"></vxe-column>
        <vxe-column field="creditObj.p8" title="千" width="60" align="center" :edit-render="{ name: 'VxeNumberInput', autoSelect: true, props:  { type: 'integer', max: 9, controls: false, maxLength: 1, align: 'center' } }"></vxe-column>
        <vxe-column field="creditObj.p7" title="百" width="60" align="center" :edit-render="{ name: 'VxeNumberInput', autoSelect: true, props:  { type: 'integer', max: 9, controls: false, maxLength: 1, align: 'center' } }"></vxe-column>
        <vxe-column field="creditObj.p6" title="十" width="60" align="center" :edit-render="{ name: 'VxeNumberInput', autoSelect: true, props:  { type: 'integer', max: 9, controls: false, maxLength: 1, align: 'center' } }"></vxe-column>
        <vxe-column field="creditObj.p5" title="万" width="60" align="center" :edit-render="{ name: 'VxeNumberInput', autoSelect: true, props:  { type: 'integer', max: 9, controls: false, maxLength: 1, align: 'center' } }"></vxe-column>
        <vxe-column field="creditObj.p4" title="千" width="60" align="center" :edit-render="{ name: 'VxeNumberInput', autoSelect: true, props:  { type: 'integer', max: 9, controls: false, maxLength: 1, align: 'center' } }"></vxe-column>
        <vxe-column field="creditObj.p3" title="百" width="60" align="center" :edit-render="{ name: 'VxeNumberInput', autoSelect: true, props:  { type: 'integer', max: 9, controls: false, maxLength: 1, align: 'center' } }"></vxe-column>
        <vxe-column field="creditObj.p2" title="十" width="60" align="center" :edit-render="{ name: 'VxeNumberInput', autoSelect: true, props:  { type: 'integer', max: 9, controls: false, maxLength: 1, align: 'center' } }"></vxe-column>
        <vxe-column field="creditObj.p1" title="元" width="60" align="center" :edit-render="{ name: 'VxeNumberInput', autoSelect: true, props:  { type: 'integer', max: 9, controls: false, maxLength: 1, align: 'center' } }"></vxe-column>
        <vxe-column field="creditObj.m1" title="角" width="60" align="center" :edit-render="{ name: 'VxeNumberInput', autoSelect: true, props:  { type: 'integer', max: 9, controls: false, maxLength: 1, align: 'center' } }"></vxe-column>
        <vxe-column field="creditObj.m2" title="分" width="60" align="center" :edit-render="{ name: 'VxeNumberInput', autoSelect: true, props: { type: 'integer', max: 9, controls: false, maxLength: 1, align: 'center' } }"></vxe-column>
      </vxe-colgroup>
      <vxe-column field="x2" title="√" width="40"></vxe-column>
      <vxe-column field="active" title="操作" width="100" fixed="right">
        <template #default="{ row }">
          <vxe-button mode="text" status="primary" icon="vxe-icon-add" @click="insertRow(row)"></vxe-button>
          <vxe-button mode="text" status="error" icon="vxe-icon-delete" @click="removeRow(row)"></vxe-button>
        </template>
      </vxe-column>
    </vxe-table>




    </el-card>

    <!-- 提交按钮 -->
    <el-button type="primary" @click="handleSubmit">提交订单</el-button>
  </div>
</template>

<script>
import Vue from 'vue'
import { VxeUI } from 'vxe-table'
import XEUtils from 'xe-utils'
export default {
  data() {
    const formData = {
      certNO: '',
      certDate: '',
      fileNumber: 1
    }
    const footerRow = {
      seq: '合计',
      debtorObj: {},
      creditObj: {}
    }
    const footerData = [
      footerRow
    ]

    const tableData = []
    const printConfig = {
      beforePrintMethod: ({ html }) => {
        const topEl = this.$refs.topElemRef
        const topHtml = topEl ? topEl.outerHTML : ''
        const bottomEl = this.$refs.bottomElemRef
        const bottomHtml = bottomEl ? bottomEl.outerHTML : ''
        return `${topHtml}${html}${bottomHtml}`
      }
    }
    const exportConfig = {}
    const columnConfig = {
      resizable: true
    }
    const editConfig = {
      mode: 'cell',
      trigger: 'click',
      showStatus: true
    }
    return {
      tableData,
     formData,
      footerRow,
      footerData,
      printConfig,
      exportConfig,
      columnConfig,
      editConfig,
      addressForm: {
        googleAddress: '',
        companyOrPersonal: '',
        contactPerson: '',
        contactPhone: '',
        email: '',
        stateProvince: '',
        city: '',
        postalCode: '',
        detailAddress1: '',
        detailAddress2: ''
      },

      form: {
        fileList: [],
        deliveryPlace: '',
        referenceNumber: '',
        destinationCountry: '',
        senderCompanyZh: '',
        warehouseType: '',
        senderCompanyEn: '',
        actualWeight: '',
        senderAddressEn: '',
        boxCount: '',
        volume: '',
        deliveryAddress: '',
        channelCapabilities: [],
        isTaxIncluded: '否',
        deliveryTerms: 'DDP',
        isCustomsDeclaration: '否',
        channel: '',
        domesticArea: '',
        expectedBillingVolume: '',
        deliveryMethod: '九方提货',
        isInsurancePurchased: '否',
        warehouseEntryTime: '',
        remarks: ''
      }
    }



    // return {
    //   loading: false,
    //   tableData,
    //   sexOptions,
    //   ageOptions,
    //   formatterSex,
    //   filterAgeMethod,
    //   form: {
    //     deliveryPlace: '',
    //     destinationCountry: '',
    //     // 其他表单字段...
    //   }
    // };
  },
  mounted () {
    this.loading = true
    setTimeout(() => {
      this.tableData = [
        { id: 10001, name: 'Test1', role: 'Develop', sex: '0', age: 28, address: 'test abc' },
        { id: 10002, name: 'Test2', role: 'Test', sex: '1', age: 22, address: 'Guangzhou' },
        { id: 10003, name: 'Test3', role: 'PM', sex: '0', age: 32, address: 'Shanghai' },
        { id: 10004, name: 'Test4', role: 'Designer', sex: '1', age: 23, address: 'test abc' },
        { id: 10005, name: 'Test5', role: 'Develop', sex: '1', age: 30, address: 'Shanghai' },
        { id: 10006, name: 'Test6', role: 'Designer', sex: '1', age: 21, address: 'test abc' },
        { id: 10007, name: 'Test7', role: 'Test', sex: '0', age: 29, address: 'test abc' },
        { id: 10008, name: 'Test8', role: 'Develop', sex: '0', age: 35, address: 'test abc' },
        { id: 10009, name: 'Test9', role: 'Test', sex: '1', age: 21, address: 'test abc' },
        { id: 10010, name: 'Test10', role: 'Develop', sex: '0', age: 28, address: 'test abc' },
        { id: 10011, name: 'Test11', role: 'Test', sex: '0', age: 29, address: 'test abc' },
        { id: 10012, name: 'Test12', role: 'Develop', sex: '1', age: 27, address: 'test abc' },
        { id: 10013, name: 'Test13', role: 'Test', sex: '0', age: 24, address: 'test abc' },
        { id: 10014, name: 'Test14', role: 'Develop', sex: '1', age: 34, address: 'test abc' },
        { id: 10015, name: 'Test15', role: 'Test', sex: '1', age: 21, address: 'test abc' },
        { id: 10016, name: 'Test16', role: 'Develop', sex: '0', age: 20, address: 'test abc' },
        { id: 10017, name: 'Test17', role: 'Test', sex: '1', age: 31, address: 'test abc' },
        { id: 10018, name: 'Test18', role: 'Develop', sex: '0', age: 32, address: 'test abc' },
        { id: 10019, name: 'Test19', role: 'Test', sex: '1', age: 37, address: 'test abc' },
        { id: 10020, name: 'Test20', role: 'Develop', sex: '1', age: 41, address: 'test abc' }
      ]
      this.loading = false
    }, 500)
  },
  methods: {
    selectCommonAddress() {
      // 处理选择常用地址逻辑
    },
    saveAsCommonAddress() {
      // 处理保存为常用地址逻辑
    },

    handleRemove(file, fileList) {
        console.log(file, fileList);
      },
      handlePreview(file) {
        console.log(file);
      },
      handleExceed(files, fileList) {
        this.$message.warning(`当前限制选择 3 个文件，本次选择了 ${files.length} 个文件，共选择了 ${files.length + fileList.length} 个文件`);
      },
      beforeRemove(file, fileList) {
        return this.$confirm(`确定移除 ${ file.name }？`);
      },

    handleSubmit() {
      console.log('Form submitted:', this.form);
      // 处理表单提交逻辑
    },
    handleSpitAmount (amount, isUnit) {
      const str = XEUtils.toValueString(XEUtils.toNumber(amount) || '')
      const [pStr, mStr] = `${isUnit ? '￥' : ''}${str}`.split('.')
      const restObj = {
        p9: '',
        p8: '',
        p7: '',
        p6: '',
        p5: '',
        p4: '',
        p3: '',
        p2: '',
        p1: '',
        m1: '',
        m2: ''
      }
      if (pStr) {
        pStr.split('').reverse().forEach((val, i) => {
          restObj[`p${i + 1}`] = `${val || 0}`
        })
      }
      if (mStr) {
        mStr.split('').forEach((val, i) => {
          restObj[`m${i + 1}`] = `${val || 0}`
        })
      }
      return restObj
    },
    handleJoinAmount (obj) {
      const strArr = [obj.p9 || 0, obj.p8 || 0, obj.p7 || 0, obj.p6 || 0, obj.p5 || 0, obj.p4 || 0, obj.p3 || 0, obj.p2 || 0, obj.p1 || 0, '.', obj.m2 || 0, obj.m1 || 0]
      return XEUtils.toNumber(strArr.join(''))
    },
    handleData (list) {
      return list.map(item => {
        return {
          ...item,
          debtorObj: this.handleSpitAmount(item.debtorAmount, false),
          creditObj: this.handleSpitAmount(item.creditAmount, false)
        }
      })
    },
    loadList (list) {
      this.tableData = this.handleData(list)
      this.$nextTick(() => {
        this.updateFooter()
      })
    },
    updateFooter () {
      this.$nextTick(() => {
        const $table = this.$refs.tableRef
        if ($table) {
          const fullData = $table.getFullData()
          let countDebtorAmount = 0
          let countCreditAmount = 0
          fullData.forEach(row => {
            row.debtorAmount = this.handleJoinAmount(row.debtorObj)
            row.creditAmount = this.handleJoinAmount(row.creditObj)
            countDebtorAmount += row.debtorAmount
            countCreditAmount += row.creditAmount
          })
          this.footerRow.debtorObj = this.handleSpitAmount(countDebtorAmount, true)
          this.footerRow.creditObj = this.handleSpitAmount(countCreditAmount, true)
        }
      })
    },
    editClosedEvent () {
      this.updateFooter()
    },
    async addEvent () {
      const $table = this.$refs.tableRef
      if ($table) {
        const record = {}
        await $table.insertAt(record, -1)
        this.updateFooter()
      }
    },
    saveEvent () {
      const $table = this.$refs.tableRef
      if ($table) {
        const fullData = $table.getFullData()
        const rest = fullData.map(item => {
          return {
            id: item.id,
            summary: item.summary,
            subject: item.subject,
            debtorAmount: this.handleJoinAmount(item.debtorObj),
            creditAmount: this.handleJoinAmount(item.creditObj)
          }
        })
        VxeUI.modal.message({
          content: '保存成功',
          status: 'success'
        })
        this.loadList(rest)
        console.log(rest)
      }
    },
    async insertRow (row) {
      const $table = this.$refs.tableRef
      if ($table) {
        const record = {}
        await $table.insertAt(record, row)
        this.updateFooter()
      }
    },
    async removeRow (row) {
      const $table = this.$refs.tableRef
      if ($table) {
        await $table.remove(row)
        this.updateFooter()
      }
    }
  },
  mounted () {
    const $table = this.$refs.tableRef
    // const $toolbar = this.$refs.toolbarRef
    // if ($table && $toolbar) {
    //   $table.connect($toolbar)
    // }
  },
  created () {
    const list = [
      { id: 10001, summary: '购买办公用品', subject: '办公费', debtorAmount: 120000, creditAmount: 0 },
      { id: 10002, summary: '购买办公用品', subject: '库存现金', debtorAmount: 0, creditAmount: 120000 }
    ]
    this.loadList(list)
  }
  
};
</script>

<style scoped>
.add-sea-form {
  padding: 20px;
}

.box-card {
  margin-bottom: 20px;
}
/* .el-form-item--medium .el-form-item__content {
  width: 100%;
} */

</style>
