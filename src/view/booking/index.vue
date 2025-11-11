<template>
  <div class="app-container">
    <!-- 标签页 -->
    <el-tabs v-model="activeName" @tab-click="handleClick">
      <el-tab-pane label="全部(1)" name="A"></el-tab-pane>
      <el-tab-pane label="待确认(0)" name="B"></el-tab-pane>
      <el-tab-pane label="待交寄(0)" name="C"></el-tab-pane>
      <el-tab-pane label="待出库(0)" name="D"></el-tab-pane>
      <el-tab-pane label="待签收(0)" name="E"></el-tab-pane>
      <el-tab-pane label="已签收(1)" name="F"></el-tab-pane>
      <el-tab-pane label="问题件(0)" name="G"></el-tab-pane>
      <el-tab-pane label="已退货(0)" name="H"></el-tab-pane>
    </el-tabs>

    <!-- 筛选条件 -->
    <el-card class="box-card">
      <el-form :model="queryParams" label-width="80px">
        <el-row>
          <el-col :sm="4">
            <el-form-item label="渠道">
              <el-select v-model="queryParams.channel" placeholder="请选择渠道">
                <el-option label="渠道一" value="channel1"></el-option>
                <el-option label="渠道二" value="channel2"></el-option>
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :sm="4">
            <el-form-item label="运单号">
              <el-input v-model="queryParams.orderNo" placeholder="请输入运单号"></el-input>
            </el-form-item>
          </el-col>
          <el-col :sm="4">
            <el-form-item label="追踪号">
              <el-input v-model="queryParams.trackingNo" placeholder="请输入追踪号"></el-input>
            </el-form-item>
          </el-col>
          <el-col :sm="4">
            <el-form-item label="参考号">
              <el-input v-model="queryParams.referenceNo" placeholder="请输入参考号"></el-input>
            </el-form-item>
          </el-col>
          <el-col :sm="4">
            <el-form-item label="中文品名">
              <el-input v-model="queryParams.chineseProductName" placeholder="请输入中文品名"></el-input>
            </el-form-item>
          </el-col>
        </el-row>
        <el-row>
          <el-col :sm="4">
            <el-form-item label="目的国家">
              <el-select v-model="queryParams.destinationCountry" placeholder="请选择目的国家">
                <el-option label="美国" value="US"></el-option>
                <el-option label="加拿大" value="CA"></el-option>
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :sm="4">
            <el-form-item label="SKU">
              <el-input v-model="queryParams.sku" placeholder="请输入SKU"></el-input>
            </el-form-item>
          </el-col>
          <el-col :sm="4">
            <el-form-item label="仓库代码">
              <el-input v-model="queryParams.warehouseCode" placeholder="请输入仓库代码"></el-input>
            </el-form-item>
          </el-col>
          <el-col :sm="4">
            <el-form-item label="shipmentID">
              <el-input v-model="queryParams.shipmentID" placeholder="请输入shipmentID"></el-input>
            </el-form-item>
          </el-col>
          <el-col :sm="4">
            <el-form-item label="交货地">
              <el-select v-model="queryParams.deliveryPlace" placeholder="请选择交货地">
                <el-option label="交货地一" value="place1"></el-option>
                <el-option label="交货地二" value="place2"></el-option>
              </el-select>
            </el-form-item>
          </el-col>
        </el-row>

        <el-row>
          <el-col :sm="4" :offset="13">
            <el-form-item>
              <el-button type="primary" @click="handleQuery">查询</el-button>
              <el-button @click="handleReset">重置</el-button>
            </el-form-item>
          </el-col>
        </el-row>
      </el-form>
    </el-card>

    <div style="margin-top: 20px;"></div>

    <el-card class="box-card">
      <div>
        <!-- 批量操作按钮 -->
        <el-row>
          <el-col :span="12" :offset="13">
            <el-button-group style="margin-top: 20px; margin-bottom: 20px;">
              <el-button size="mini" type="primary">报关资料</el-button>
              <el-button size="mini" type="primary">合并清关</el-button>
              <el-button size="mini" type="primary">取消合并清关</el-button>
              <el-button size="mini" type="primary">批量交寄</el-button>
              <el-button size="mini" type="primary">批量打回待确认</el-button>
              <el-button size="mini" type="primary">合并报关</el-button>
              <el-button size="mini" type="primary">取消合并报关</el-button>
              <el-button size="mini">批量下载</el-button>
            </el-button-group>
          </el-col>
        </el-row>
      </div>

      <!-- 表格数据 -->
      <el-table :data="tableData" stripe style="width: 100%" size="mini">
        <el-table-column type="selection"></el-table-column>
        <el-table-column type="index" label="序号" fixed></el-table-column>
        <el-table-column label="运单号" fixed width="120">
          <el-table-column prop="waybillNo" label="参考号" width="120">
            <template slot-scope="scope">
              <div style="margin-left: 10px">{{ scope.row.waybillNo }}</div>
            </template>
          </el-table-column>
        </el-table-column>


        <el-table-column prop="no" label="询单号" width="120"></el-table-column>

        <el-table-column prop="value2" label="状态">
          <el-table-column prop="value3" label="入库仓" width="120">
            <template slot-scope="scope">
               <div style="margin-left: 10px" v-if="scope.row.value2">{{ scope.row.value2 }}</div>
               <div style="margin-left: 10px" v-if="scope.row.value3">{{ scope.row.value3 }}</div>
            </template>
          </el-table-column>
        </el-table-column>    


        <el-table-column prop="trackNumber" label="追踪号1">
          <el-table-column prop="trackNumber2" label="追踪号2" width="120">
            <template slot-scope="scope">
               <div style="margin-left: 10px" v-if="scope.row.trackingNo">
                <a  :href="'https://t.17track.net/zh-cn#nums='+scope.row.trackingNo" target="_blank" style="color: blue;">{{ scope.row.trackingNo }}</a>
              </div>
               <div style="margin-left: 10px" v-if="scope.row.trackingNo2">{{ scope.row.trackingNo2 }}</div>
            </template>
          </el-table-column>
        </el-table-column>
        <el-table-column prop="returnCountry" label="退货国家">
          <el-table-column prop="trackingNo2" label="派送地址" width="120">
             <template slot-scope="scope">
              <div style="margin-left: 10px" >{{ scope.row.value4 }}</div>
             </template>
          </el-table-column>
        </el-table-column>
        <el-table-column prop="inboundChannel" label="入库渠道">
          <el-table-column prop="trackingNo2" label="申报总额" width="120">
             <template slot-scope="scope">
              <div style="margin-left: 10px" >{{ scope.row.value5 }}</div>
              <div style="margin-left: 10px" >{{ scope.row.value6 }}</div>
             </template>
          </el-table-column>
        </el-table-column>
        <el-table-column prop="forecastWeight" label="预报实重">
          <el-table-column prop="forecastWeight" label="入库实重" width="100">
            <template slot-scope="scope">
              <div style="margin-left: 10px" >{{ scope.row.value7 }}</div>
              <div style="margin-left: 10px" >{{ scope.row.value8 }}</div>
             </template>
          </el-table-column>
        </el-table-column>
        <el-table-column prop="forecastVolume" label="预报体积">
          <el-table-column prop="forecastVolume" label="入库体积" width="100">
             <template slot-scope="scope">
              <div style="margin-left: 10px" >{{ scope.row.value9 }}</div>
              <div style="margin-left: 10px" >{{ scope.row.value10 }}</div>
             </template>
          </el-table-column>
        </el-table-column>
        <el-table-column prop="forecastFeeWeight" label="预报计费重">
          <el-table-column prop="forecastFeeWeight" label="入库计费重" width="100">
             <template slot-scope="scope">
              <div style="margin-left: 10px" >{{ scope.row.value11 }}</div>
              <div style="margin-left: 10px" >{{ scope.row.value12 }}</div>
             </template>
          </el-table-column>
        </el-table-column>
        <el-table-column prop="forecastPieceCount" label="预报件数">
          <el-table-column prop="forecastPieceCount" label="入库件数" width="100">
             <template slot-scope="scope">
              <div style="margin-left: 10px" >{{ scope.row.value13 }}</div>
              <div style="margin-left: 10px" >{{ scope.row.value14 }}</div>
             </template>
          </el-table-column>
        </el-table-column>
        <el-table-column prop="inboundTime" label="入库时间">
          <el-table-column prop="outboundTime" label="出库时间" width="160">
            <template slot-scope="scope">
              <div style="margin-left: 10px">{{ scope.row.value15 }}</div>
              <div style="margin-left: 10px">{{ scope.row.value16 }}</div>
            </template>
          </el-table-column>
        </el-table-column>
        <el-table-column prop="createTime" label="创建时间" width="160">
          <template slot-scope="scope">
            <div style="margin-left: 10px">{{ scope.row.value17 }}</div>
          </template>
        </el-table-column>
        <el-table-column prop="chineseProductName" label="中文品名">
          <el-table-column prop="channelCapacity" label="备注" width="160">
            <template slot-scope="scope">
              <div style="margin-left: 10px" >{{ scope.row.value18 }}</div>
            </template>
          </el-table-column>
        </el-table-column>

        <el-table-column prop="inboundTime" label="渠道能力">
          <el-table-column prop="outboundTime" label="国内分区" width="150">
            <template slot-scope="scope">
              <div style="margin-left: 10px" >{{ scope.row.value19 }}</div>
              <div style="margin-left: 10px" >{{ scope.row.value20 }}</div>
            </template>
          </el-table-column>
        </el-table-column>
        <el-table-column prop="inboundTime" label="海外仓运单" width="140">
            <template slot-scope="scope">
              <div style="margin-left: 10px" >{{ scope.row.value21 }}</div>
            </template>
        </el-table-column>
        <el-table-column label="操作" width="180" fixed="right">
          <template slot-scope="scope">
            <el-button
              size="mini"
              @click="handleEdit(scope.$index, scope.row)">编辑</el-button>
            <el-button
              size="mini"
              type="danger"
              @click="handleDelete(scope.$index, scope.row)">删除</el-button>
          </template>
        </el-table-column>
      </el-table>

      <!-- 分页 -->
      <pagination
        v-show="total > 0"
        :total="total"
        :page.sync="queryParams.page"
        :limit.sync="queryParams.limit"
        :page-sizes="[10, 20, 30, 40]"
        :page-size="10"
        layout="total, prev, pager, next, jumper, sizes"
        @pagination="getList"
      />
    </el-card>
  </div>
</template>

<script>
import Pagination from '@/components/Pagination' // secondary package based on el-pagination
import { valueAxis } from 'echarts/lib/theme/dark'

export default {
  name: 'OrderManagement',
  components: { Pagination },
  data() {
    return {
      activeName: 'A',
      queryParams: {
        channel: '',
        orderNo: '',
        trackingNo: '',
        referenceNo: '',
        chineseProductName: '',
        destinationCountry: '',
        sku: '',
        warehouseCode: '',
        shipmentID: '',
        deliveryPlace: '',
        page: 1,
        limit: 15 // 修改: 每页显示15条
      },
      tableData: [
    {
        id: 2203401, 
        waybillNo: "ZX24107338N",
        value: "ZX24107338N",
        trackingNo: "779205181132",
        trackingNo2:null,
        value2: "待锁单",
        value3: "深圳空运仓",
        value4: "美国",
        value5: "美国空派普货",
        value6: "16(USD)",
        value7: "11.8",  
        value8: "11.86",
        value9: "0.016",
        value10: "0.02",
        value11: "12",
        value12: "12",
        value13: "1",
        value14: "1",
        value15: "2024-10-12 13:21:48",
        value16: "2024-10-13 00:03:16",
        value17: "2024-10-10 14:50:37",
        value18: "名器",
        value19: "普货（无任何电池）",
        value20: "邮编4、5、6开头",
        value21: "SKEW0203232",
      
        
    }
],
      total: 30, // 修改: 总条数为30
      loading: false
    }
  },
  created() {
    this.getList()
  },
  methods: {
    handleSizeChange(val) {
      console.log(`每页 ${val} 条`)
    },
    handleCurrentChange(val) {
      console.log(`当前页: ${val}`)
    },
    handleClick(tab, event) {
      console.log(tab, event)
    },
    handleQuery() {
      this.queryParams.page = 1
      this.getList()
    },
    handleEdit(index, row) {
      console.log(index, row)
    },
    handleDelete(index, row) {
      console.log(index, row)
    },
    handleReset() {
      this.queryParams = {
        channel: '',
        orderNo: '',
        trackingNo: '',
        referenceNo: '',
        chineseProductName: '',
        destinationCountry: '',
        sku: '',
        warehouseCode: '',
        shipmentID: '',
        deliveryPlace: '',
        page: 1,
        limit: 20
      }
      this.getList()
    },
    getList() {
      this.loading = true
      // 假设使用 axios 请求数据
      // this.$axios.get('/api/orderManagement', {
      //   params: this.queryParams
      // }).then(response => {
      //   this.tableData = response.data.items
      //   this.total = response.data.total
      //   this.loading = false
      // })
    }
  }
}
</script>

<style scoped>
.app-container {
  padding: 30px;
}
</style>
