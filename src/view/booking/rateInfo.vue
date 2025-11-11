<template>
  <div class="app-container">
    <el-card class="form-container" shadow="never">
      <!-- <div slot="header" class="clearfix">
        <span>费率信息编辑</span>
      </div> -->
      
      <el-form 
        ref="rateForm" 
        :model="rateForm" 
        :rules="rules" 
        label-width="120px" 
        class="rate-form"
      >
        <!-- 基础信息分组 -->
        <el-row>
          <el-col :span="24">
            <el-divider content-position="left">基础信息</el-divider>
          </el-col>
        </el-row>
        
        <el-row :gutter="20">
          <el-col :span="12">
            <el-form-item label="费率版本" prop="version">
              <el-select 
                v-model="rateForm.version" 
                placeholder="请选择费率版本"
                clearable
              >
                <el-option
                  v-for="item in versionOptions"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value">
                </el-option>
              </el-select>
            </el-form-item>
          </el-col>
          
          <el-col :span="12">
            <el-form-item label="折扣" prop="discount">
              <el-input 
                v-model="rateForm.discount" 
                placeholder="请输入折扣"
                type="number"
              >
                <template slot="append">%</template>
              </el-input>
            </el-form-item>
          </el-col>
        </el-row>
        
        <el-row :gutter="20">
          <el-col :span="12">
            <el-form-item label="最低保费" prop="minPremium">
              <el-input 
                v-model="rateForm.minPremium" 
                placeholder="请输入最低保费"
                type="number"
              >
                <template slot="append">元</template>
              </el-input>
            </el-form-item>
          </el-col>
          
          <el-col :span="12">
            <el-form-item label="最高赔付金额" prop="maxPayout">
              <el-input 
                v-model="rateForm.maxPayout" 
                placeholder="请输入最高赔付金额"
                type="number"
              >
                <template slot="append">元</template>
              </el-input>
            </el-form-item>
          </el-col>
        </el-row>
        
        <!-- 限额审批费用设置分组 -->
        <el-row>
          <el-col :span="24">
            <el-divider content-position="left">限额审批费用设置</el-divider>
          </el-col>
        </el-row>
        
        <el-row :gutter="20">
          <el-col :span="12">
            <el-form-item label="普通" prop="normalFee">
              <el-input 
                v-model="rateForm.normalFee" 
                placeholder="请输入普通审批费用"
                type="number"
              >
                <template slot="append">元</template>
              </el-input>
            </el-form-item>
          </el-col>
          
          <el-col :span="12">
            <el-form-item label="加急" prop="urgentFee">
              <el-input 
                v-model="rateForm.urgentFee" 
                placeholder="请输入加急审批费用"
                type="number"
              >
                <template slot="append">元</template>
              </el-input>
            </el-form-item>
          </el-col>
        </el-row>
        
        <!-- 操作按钮 -->
        <el-row>
          <el-col :span="24" class="button-container">
            <el-button type="primary" @click="submitForm">保存</el-button>
            <el-button @click="resetForm">重置</el-button>
          </el-col>
        </el-row>
      </el-form>
    </el-card>
  </div>
</template>

<script>
export default {
  name: 'RateInfo',
  data() {
    return {
      rateForm: {
        version: '',
        discount: '',
        minPremium: '',
        maxPayout: '',
        normalFee: '',
        urgentFee: ''
      },
      // 添加费率版本选项
      versionOptions: [
        { value: 'V1.0', label: 'V1.0' },
        { value: 'V2.0', label: 'V2.0' },
        { value: 'V3.0', label: 'V3.0' }
      ],
      rules: {
        version: [
          { required: true, message: '请选择费率版本', trigger: 'change' }
        ],
        discount: [
          { required: true, message: '请输入折扣', trigger: 'blur' }
        ],
        minPremium: [
          { required: true, message: '请输入最低保费', trigger: 'blur' }
        ],
        maxPayout: [
          { required: true, message: '请输入最高赔付金额', trigger: 'blur' }
        ],
        normalFee: [
          { required: true, message: '请输入普通审批费用', trigger: 'blur' }
        ],
        urgentFee: [
          { required: true, message: '请输入加急审批费用', trigger: 'blur' }
        ]
      }
    }
  },
  methods: {
    initForm(data) {
      // 初始化表单数据，这里可以根据实际业务需求进行调整
      this.rateForm = {
        version: data.version || '',
        discount: data.discount || '',
        minPremium: data.minPremium || '',
        maxPayout: data.maxPayout || '',
        normalFee: data.normalFee || '',
        urgentFee: data.urgentFee || ''
      }
    },
    submitForm() {
      this.$refs.rateForm.validate((valid) => {
        if (valid) {
          this.$message.success('提交成功');
        } else {
          this.$message.error('请完善必填信息');
          return false;
        }
      });
    },
    resetForm() {
      this.$refs.rateForm.resetFields();
    },
    cancel() {
      this.$confirm('确定要取消编辑吗？', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.$router.go(-1);
      }).catch(() => {
        // 取消操作
      });
    }
  }
}
</script>

<style scoped>
.app-container {
  padding: 0px;
  background-color: #f5f5f5;
}

.form-container {
  border-radius: 8px;
  box-shadow: 0 2px 12px 0 rgba(0, 0, 0, 0.1);
}

.form-container ::v-deep .el-card__header {
  background-color: #f9f9f9;
  border-bottom: 1px solid #ebeef5;
  font-weight: 600;
  font-size: 16px;
}

.rate-form {
  padding: 20px;
}

.button-container {
  text-align: center;
  margin-top: 30px;
}

.button-container .el-button {
  margin: 0 10px;
}

::v-deep .el-divider__text {
  font-size: 16px;
  font-weight: 500;
}

::v-deep .el-form-item__label {
  font-weight: 500;
}

::v-deep .el-form-item {
  margin-bottom: 22px;
}
</style>