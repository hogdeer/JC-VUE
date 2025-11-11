
<template>
  <div class="bill-info">
    <!-- 顶部流程状态栏 -->
    <el-steps :active="4" align-center>
      <el-step title="出账" description="2021-08-21 16:12:00"></el-step>
      <el-step title="确认账单" description="2022-04-14 18:27:05"></el-step>
      <el-step title="开票" description="2022-04-14 18:27:30"></el-step>
      <el-step title="收款" description="2022-04-14 18:27:30"></el-step>
    </el-steps>

    <!-- 账单基本信息区域 -->
    <el-row :gutter="20" >
      <el-col :span="24">
        <el-card shadow="hover" class="info-card">
          <div slot="header" class="card-header">
            <span>账单基本信息</span>
            <el-button 
              type="primary" 
              size="small" 
              style="float: right;"
              @click="confirmBill">确认账单</el-button>
          </div>
          <el-table :data="billInfo" border>
            <el-table-column prop="content" label="账单内容"></el-table-column>
            <el-table-column prop="amount" label="账单总金额"></el-table-column>
            <el-table-column prop="actualAmount" label="实付金额"></el-table-column>
            <el-table-column prop="time" label="账单时间"></el-table-column>
            <el-table-column prop="remark" label="备注"></el-table-column>
          </el-table>
        </el-card>
      </el-col>
    </el-row>

    <!-- 账单详情区域 -->
    <el-row :gutter="20">
      <el-col :span="24">
        <el-card shadow="hover" class="detail-card">
          <div slot="header" class="card-header">
            <span>2021年8月份账单</span>
            <el-button type="primary" size="small" style="float: right;">导出核对的对账单</el-button>
          </div>
<el-tabs v-model="activeName" @tab-click="handleClick">
    <el-tab-pane label="创新险" name="first">
        <el-table 
            :data="insuranceDetails.slice((currentPage - 1) * pageSize, currentPage * pageSize)" 
            border
            style="width: 100%">
            <el-table-column prop="policyNo" label="保单号"></el-table-column>
            <el-table-column prop="originalOrderNo" label="原单号"></el-table-column>
            <el-table-column prop="waybillNo" label="提单号/运单号"></el-table-column>
            <el-table-column prop="insured" label="被保险人"></el-table-column>
            <el-table-column prop="insuranceDate" label="投保日期"></el-table-column>
            <el-table-column prop="departureDate" label="起运日期"></el-table-column>
            <el-table-column prop="currency" label="投保币种"></el-table-column>
            <el-table-column prop="insuranceAmount" label="投保金额"></el-table-column>
            <el-table-column prop="exchangeRate" label="投保汇率"></el-table-column>
            <el-table-column prop="feeType" label="费用类别"></el-table-column>
            <el-table-column prop="premium" label="保费(RMB)"></el-table-column>
          </el-table>
        <el-pagination
            @size-change="handleSizeChange"
            @current-change="handleCurrentChange"
            :current-page="currentPage"
            :page-sizes="[10, 20, 50, 100]"
            :page-size="pageSize"
            layout="total, sizes, prev, pager, next, jumper"
            :total="insuranceDetails.length">
        </el-pagination>
    </el-tab-pane>
    <el-tab-pane label="传统险" name="second">传统险</el-tab-pane>

  </el-tabs>


       
        </el-card>
      </el-col>
    </el-row>
  </div>
</template>

<script>
export default {
  data() {
    return {
      billInfo: [
        {
          content: '2021年8月份账单',
          amount: '￥2.27',
          paidAmount: '--',
          actualAmount: '￥2.27',
          time: '2021-08-21 16:12:00',
          invoiceNo: '--',
          expressNo: '--',
          remark: '--'
        }
      ],
      insuranceDetails: [
        {
          policyNo: 'SVB51002212000322644',
          originalOrderNo: 'PX07140958001',
          waybillNo: 'SDF',
          insured: '测试公司',
          insuranceDate: '2021-07-14',
          departureDate: '2021-07-14',
          currency: 'USD',
          insuranceAmount: '$123.34',
          exchangeRate: '6.4757',
          feeType: '签单保费',
          premium: '￥0.78'
        },{
          policyNo: 'SVB51002212000322644',
          originalOrderNo: 'PX07140958001',
          waybillNo: 'SDF',
          insured: '测试公司',
          insuranceDate: '2021-07-14',
          departureDate: '2021-07-14',
          currency: 'USD',
          insuranceAmount: '$123.34',
          exchangeRate: '6.4757',
          feeType: '签单保费',
          premium: '￥0.78'
        },{
          policyNo: 'SVB51002212000322644',
          originalOrderNo: 'PX07140958001',
          waybillNo: 'SDF',
          insured: '测试公司',
          insuranceDate: '2021-07-14',
          departureDate: '2021-07-14',
          currency: 'USD',
          insuranceAmount: '$123.34',
          exchangeRate: '6.4757',
          feeType: '签单保费',
          premium: '￥0.78'
        },{
          policyNo: 'SVB51002212000322644',
          originalOrderNo: 'PX07140958001',
          waybillNo: 'SDF',
          insured: '测试公司',
          insuranceDate: '2021-07-14',
          departureDate: '2021-07-14',
          currency: 'USD',
          insuranceAmount: '$123.34',
          exchangeRate: '6.4757',
          feeType: '签单保费',
          premium: '￥0.78'
        },{
          policyNo: 'SVB51002212000322644',
          originalOrderNo: 'PX07140958001',
          waybillNo: 'SDF',
          insured: '测试公司',
          insuranceDate: '2021-07-14',
          departureDate: '2021-07-14',
          currency: 'USD',
          insuranceAmount: '$123.34',
          exchangeRate: '6.4757',
          feeType: '签单保费',
          premium: '￥0.78'
        },{
          policyNo: 'SVB51002212000322644',
          originalOrderNo: 'PX07140958001',
          waybillNo: 'SDF',
          insured: '测试公司',
          insuranceDate: '2021-07-14',
          departureDate: '2021-07-14',
          currency: 'USD',
          insuranceAmount: '$123.34',
          exchangeRate: '6.4757',
          feeType: '签单保费',
          premium: '￥0.78'
        }
        // 其他测试数据...
      ],
      currentPage: 1,  // 当前页码
      pageSize: 10,    // 每页条数
      activeName: 'first' // 设置 tabs 默认选中第一个
    };
  },
  methods: {
    // 添加分页处理方法
    handleSizeChange(val) {
      this.pageSize = val;
      this.currentPage = 1;
    },
    handleCurrentChange(val) {
      this.currentPage = val;
    },
    confirmBill() {
      this.$confirm('您已核对过金额，并确认此账单吗？', '确认账单', {
        confirmButtonText: '确认',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.$message({
          type: 'success',
          message: '账单确认成功!'
        });
      }).catch(() => {
        this.$message({
          type: 'info',
          message: '已取消确认'
        });          
      });
    }
  }
};
</script>

<style scoped>
.bill-info {
  padding: 15px;
  background: #fff;
  border-radius: 8px;
  box-shadow: 0 2px 12px rgba(0, 0, 0, 0.08);
  max-height: calc(100vh - 120px);
  overflow-y: auto;
  font-size: 13px;
}

.card-header {
  font-size: 13px;
  font-weight: bold;
  color: #333;
  padding: 12px 20px;
}

.el-table >>> .el-table__cell {
  padding: 8px 0;
  font-size: 12px;
}

.el-card {
  margin-bottom: 15px;
  border-radius: 4px;
  border: 1px solid #ebeef5;
}

.el-tabs__item {
  padding: 0 20px;
}

.el-pagination {
  margin-top: 15px;
  padding-bottom: 5px;
}
</style>