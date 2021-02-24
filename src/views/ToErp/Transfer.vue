<template>
  <section>
    <!--查询对比条件等-->
    <el-row :gutter="0">
      <el-form :model="formbb" label-width="80px" size="mini">
        <el-col :span="4">
          <el-form-item label="截止日期" required>
            <el-date-picker
              :picker-options="expireTimeOption"
              v-model="bbDate"
              type="date"
              align="right"
              format="yyyy-MM-dd"
              value-format="yyyy-MM-dd"
              placeholder="下单开始"
              style="width: 130px"
            ></el-date-picker>
          </el-form-item>
        </el-col>
        <el-col :span="5">
          <el-form-item label="下单版本" required>
            <el-select v-model="bbnum" placeholder="版本号">
              <el-option
                v-for="(item, key) in opsbbnum"
                :key="key"
                :label="item"
                :value="item"
              ></el-option>
            </el-select>
          </el-form-item>
        </el-col>
      </el-form>
      <el-col :span="4">
        <el-button
          :disabled="isbbadd"
          @click="add_ERPBB"
          type="success"
          size="mini"
          >版本对比</el-button
        >
       <el-button @click="add_ToERP"  type="success" size="mini">转单</el-button>          
      </el-col>
      <el-col :span="4">
        <div class="demo-input-suffix">
          上一版本：
          <span class="comment1">{{ bbnumed }}</span>
        </div>
      </el-col>
      <el-col :span="4">
        <div class="demo-input-suffix">
          状态：
          <el-select
            size="mini"
            v-model="zt"
            placeholder="状态"
            style="width: 100px"
          >
            <el-option
              v-for="item in opszt"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            ></el-option>
          </el-select>
        </div>
      </el-col>

      <el-col :span="2">
        <el-button @click="get_BBAddStatistics" type="primary" size="mini"
          >查询</el-button
        >
        <el-button v-show="isrollback" @click="rollback_ERPBB" type="danger" size="mini"
          >回滚</el-button
        >
      </el-col>
    </el-row>

<!--     <el-row :gutter="10">
        <el-col :span="2" :offset="21">
    <el-button @click="add_ToERP"  type="success" size="mini">转单</el-button>            
        </el-col>
    </el-row> -->

    <!--表格{{total1}}条-->
    <el-row :gutter="0">
      <el-col :span="8">
        <el-card>
          <div slot="header">
            <span>最新版本_新增订单汇总：</span>
          </div>
          <div>
            <el-table
              :data="dataset_spe"
              style="width: 100%"
              size="mini"
              highlight-current-row
              v-loading="listLoading"
              @current-change="selectCurrentRow"
            >
              <el-table-column type="index" width="35"> </el-table-column>
              <el-table-column prop="生产线" label="生产线" width="100">
              </el-table-column>
              <el-table-column prop="生产日期" label="生产日期" width="100">
              </el-table-column>
              <el-table-column
                prop="数量"
                label="数量"
                width="70"
                show-overflow-tooltip
              >
              </el-table-column>
            </el-table>
          </div>
        </el-card>
      </el-col>
      <el-col :span="16">
        <el-card>
          <div slot="header">
            <span>最新版本_新增订单详情：</span>
          </div>
          <div>
            <el-table :data="dataset" style="width: 100%" size="mini">
              <el-table-column type="index" width="35"> </el-table-column>
              <el-table-column
                prop="ProductionCode"
                label="生产订单号"
                width="150"
                v-loading="listLoading2"
                show-overflow-tooltip
              >
              </el-table-column>
              <el-table-column prop="line" label="生产线" width="130">
              </el-table-column>
              <el-table-column prop="item" label="物料编码" width="150">
              </el-table-column>
              <el-table-column prop="quan" label="数量" width="60">
              </el-table-column>
              <el-table-column prop="prdt" label="排产时间" width="160">
              </el-table-column>
              <el-table-column prop="cwar" label="仓库" width="130">
              </el-table-column>
            </el-table>
          </div>
        </el-card>

        <!--工具条-->
        <div>
          <el-pagination
            layout="prev, pager, next"
            @current-change="handleCurrentChange"
            :page-size="pagesize"
            :total="total2"
            style="float: right"
          >
          </el-pagination>
        </div>
      </el-col>
    </el-row>
  </section>
</template>

<script>
import util from "../../../util/date";
import { getAllBBNum, getERPBBNum, getERPBBDate } from "../../api/api";
import {
  getTranERPOrder,
  addERPBB,
  getIsAddERPBB,
  updateERPBB,
  rollbackERPBB,
} from "../../api/api";
import { addToERP, updateToERP } from "../../api/api";
import { getBBAllStatistics, getBBAddStatistics } from "../../api/api";

import { getCusOrderListPage } from "../../api/api";
import { getButtonList } from "../../promissionRouter";
import Buttonbar from "../../components/Buttonbar";

export default {
  components: { Buttonbar },
  data() {
    return {
      buttonList: [],
      currentRow: null,
      filters: {
        LinkUrl: "",
      },
      //时间限定
      expireTimeOption: {
        disabledDate: (time) => {
          return time.getTime() < new Date(this.cbbDate) - 24 * 60 * 60 * 1000; // - 24 * 60 * 60 * 1000;
        },
      },
      //新增版本

      bbDate: "",
      cbbDate: "",
      bbnum: "",
      opsbbnum: [],
      bbnumed: "",
      //opsbbnumed: [],
      isbbadd: true,
      isrollback: false,
      //ERP订单查询
      qk: "",
      zt: null,
      line: "",
      page: 1,
      pagesize: 15,
      opszt: [
        { value: 0, label: "未处理" },
        { value: 1, label: "已处理" },
        { value: null, label: "不区分" },
      ],
      flag: null,
      formbb: null,
      form2: null,

      dataset: [],
      dataset_spe: [],
      dataset_all: [],

      total1: 0,
      total2: 0,

      listLoading: false,
      listLoading2: false,
      
      sels: [], //列表选中列

      //编辑界面数据

      //新增界面数据
    };
  },
  methods: {
    get_AllBBNum() {
      let para = {
        status: "1",
      };
      getAllBBNum(para).then((res) => {
        let d1 = res.data.response; //num;
        if (d1.length > 0) {
          this.bbnumed = d1[0];
        }
      });
    },
    get_ERPBBNum() {
      getERPBBNum().then((res) => {
        this.opsbbnum = res.data.response; //num;
        if (this.opsbbnum.length > 0) {
          this.isbbadd = false;
          this.bbnum = this.opsbbnum[0];
        }
      });
    },
    get_ERPBBDate() {
      getERPBBDate().then((res) => {
        this.bbDate = res.data.response; //data;
        this.cbbDate = res.data.response; //datac;
      });
    },

    selectCurrentRow(val) {
      this.currentRow = val;
      if (val != null) {
        this.get_ERPOrder();
        //console.log(this.currentRow);
      }
    },
    callFunction(item) {
      //console.log(item);
      this.page = 1;
      this[item.Func].apply(this, item);
    },
    handleCurrentChange(val) {
      this.page = val;
    },
    // formatCreateTime: function (row, column) {
    //     return (!row.需求审核时间e || row.需求审核时间 == '') ? '' : util.formatDate.format(new Date(row.需求审核时间), 'yyyy-MM-dd');
    // },
    get_IsAddERPBB() {
      getIsAddERPBB().then((res) => {
        this.isbbadd = !res.data.response;
        //console.log("jg" + res.data);
        //console.log(this.isbbadd);
      });
    },
    //新增版本数据
    add_ERPBB() {
      let para = {
        dateset: this.bbDate,
        v2: this.bbnum,
      };
      //console.log(para);
      addERPBB(para).then((res) => {
        if (res.data.success) {
          this.$message({
            message: res.data.msg,
            type: "success",
          });
          this.isbbadd = true;
          this.get_AllBBNum();
          this.get_BBAddStatistics();
        } else {
          this.$message({
            message: res.data.msg,
            type: "error",
          });
        }
      });
    },

    //更新版本数据代码 待添加
    // update_ERPBB() {
    //     updateERPBB().then((res) => {
    //         //console.log(res.data.response);
    //     });
    // },
    //回滚版本数据
    rollback_ERPBB() {
      rollbackERPBB().then((res) => {
        if (res.data.success) {
          this.$message({
            message: res.data.msg,
            type: "success",
          });
          this.isbbadd = false;
        } else {
          this.$message({
            message: res.data.msg,
            type: "error",
          });
        }
      });
    },
    //版本所有数据
    get_BBAllStatistics() {
      let para = {
        qk: "03",
        zt: this.zt,
      };
      getBBAllStatistics(para).then((res) => {
        this.dataset_all = res.data.response;
      });
    },
    //版本更新数据
    get_BBAddStatistics() {
      let para = {
        qk: "03",
        zt: this.zt,
      };
      this.listLoading=true;      
      getBBAddStatistics(para).then((res) => {
        this.listLoading=false;
        //this.total1=
        this.dataset_spe = res.data.response;
        console.log(this.dataset_spe);
        //this.total1=this.dataset_spe.
      });
    },

    //获取订单详情
    get_ERPOrder() {
      let para = {
        //date1:this.bbDate,
        bbnum: this.bbnumed,
        prdt: this.currentRow.生产日期,
        qk: "03", //this.qk,
        zt: this.zt,
        line: this.currentRow.生产线, //this.line,
        page: this.page,
        intPageSize: this.pagesize,
      };
      this.listLoading2 = true;
      getTranERPOrder(para).then((res) => {
        this.total2 = res.data.response.dataCount;
        this.dataset = res.data.response.data;
        this.listLoading2 = false;
        //console.log(this.dataset);
        //NProgress.done();
      });
    },
    //下单到ERP
    add_ToERP() {
      let para = {
        line: null,
      };
      //console.log(para);
      addToERP(para).then((res) => {
        this.zt=0;
        if (res.data.success) {
          this.$message({
            message: res.data.msg,
            type: "success",
          });
        } else {
          this.$message({
            message: res.data.msg,
            type: "error",
          });
        }
        this.get_BBAddStatistics();

      });
    },

    //删除
    //显示编辑界面
    //显示新增界面

    //编辑
    //新增
    //批量删除
  },
  mounted() {
    let routers = window.localStorage.router
      ? JSON.parse(window.localStorage.router)
      : [];
    let user = JSON.parse(window.localStorage.user);
    this.isrollback = (user.uID==1701474);
    this.buttonList = getButtonList(this.$route.path, routers);
    //this.get_IsAddERPBB();
    this.get_AllBBNum();
    this.get_ERPBBNum();
    this.get_ERPBBDate();
  },
};
</script>

<style scoped>
.comment1 {
  color: #606266;
  font-family: "Courier New", Courier, monospace;
  font-size: 14px;
  line-height: 28px;
}
.el-card >>> .el-card__body {
  padding: 5px;
}
.el-card >>> .el-card__header {
  font-size: 12px;
  line-height: 1;
  padding: 10px 20px;
}
</style>
