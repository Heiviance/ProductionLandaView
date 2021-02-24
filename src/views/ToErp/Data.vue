<template>
  <section>
    <!--工具条-->
    <el-row :gutter="0">
      <el-col :span="4">
      <!--   <el-input
          v-model="item1"
          clearable
          placeholder="线体"
          style="width: 200px"
        ></el-input> -->
        <!--         <el-input
          v-model="item2"ata
          placeholder="item2"
          style="width: 200px"
        ></el-input> -->
        <el-select v-model="itemlist1" placeholder="线体" style="width: 120px">
          <el-option
            v-for="item in opsitemlist1"
            :key="item.value"
            :label="item.label"
            :value="item.value"
          ></el-option>
        </el-select>
      </el-col>
      <el-col :span="20">
        <buttonbar
          :buttonList="buttonList"
          @callFunction="callFunction"
        ></buttonbar>
      </el-col>
    </el-row>

    <!--列表-->
    <el-table
      :data="dataset1"
      highlight-current-row
      size="mini"
      @current-change="selectCurrentRow"
      v-loading="listLoading1"
      style="width: 100%"
    >
      <el-table-column type="index" width="80"> </el-table-column>
      <el-table-column prop="CustomerCode" label="订单编号" width="">
      </el-table-column>
      <el-table-column
        prop="ProductionCode"
        label="生产编号"
        width="150"
        show-overflow-tooltip
      >
      </el-table-column>
      <el-table-column prop="item" label="凌达编码" width="150"  show-overflow-tooltip> </el-table-column>
      <el-table-column prop="ErpCode" label="ERP号" width=""> </el-table-column>
      <el-table-column prop="line" label="线体" width=""> </el-table-column>
      <el-table-column prop="quan" label="数量" width=""> </el-table-column>
      <el-table-column
        prop="prdt"
        label="排产时间"
        width="150"
        show-overflow-tooltip
      >
      </el-table-column>
      <el-table-column
        prop="lrdt"
        label="集成时间"
        width="150"
        show-overflow-tooltip
      >
     </el-table-column>

    </el-table>

    <!--工具条-->
    <el-col :span="24" class="toolbar">
      <el-pagination
        layout="prev, pager, next"
        @current-change="handleCurrentChange"
        :page-size="pagesize"
        :total="total"
        style="float: right"
      >
      </el-pagination>
    </el-col>
  </section>
</template>

<script>
import util from "../../../util/date";
import {
  getERPOrder1,
  getERPOrder2,
} from "../../api/api";

import { getButtonList } from "../../promissionRouter";
import Toolbar from "../../components/Toolbar";
import Buttonbar from "../../components/Buttonbar.vue";

export default {
  components: { Toolbar, Buttonbar },
  data() {
    return {
      //search item
      item1: "", //订单编号
      item2: "",
      //search list
      itemlist1: null, //状态
      opsitemlist1: [
        { value: null, label: "全部" },
        { value: "ZA", label: "珠海A线" },
        { value: "ZB", label: "珠海B线" },
        { value: "ZC", label: "珠海C线" },
        { value: "ZD", label: "珠海D线" },
        { value: "ZE", label: "珠海E线" },
        { value: "ZF", label: "珠海F线" },
        { value: "ZG", label: "珠海G线" },
        { value: "ZH", label: "珠海H线" }, 
      ],
      itemlist2: null,
      opsitemlist2: [],

      dataset1: [], //table1使用
      listLoading1: false,
      total: 0,
      page: 1,
      pagesize: 15,

      buttonList: [],

      currentRow: null,

      statusList: [
        { LinkUrl: "激活", value: true },
        { LinkUrl: "禁用", value: false },
      ],

      sels: [], //列表选中列

      editFormVisible: false, //编辑界面是否显示
      editLoading: false,
      editFormRules: {
        erpcode: [
          { required: true, message: "请输入ERP单号", trigger: "blur" },
        ],
      },
      editForm: {
        //编辑界面数据
        id: 0,
        erpcode: "",
      },

      // addFormVisible: false, //新增界面是否显示
      // addLoading: false,
      // addFormRules: {
      //     LinkUrl: [
      //         {required: true, message: '请输入接口地址', trigger: 'blur'}
      //     ],
      // },
      // addForm: {             //新增界面数据
      //     CreateBy: '',
      //     CreateId: '',
      //     LinkUrl: '',
      //     Name: '',
      //     Enabled: '',
      // }
    };
  },
  methods: {
    selectCurrentRow(val) {
      this.currentRow = val;
    },
    callFunction(item) {
      this[item.Func].apply(this, item);
    },
    //性别显示转换
    formatEnabled: function (row, column) {
      return row.Enabled ? "正常" : "未知";
    },
    formatCreateTime: function (row, column) {
      return !row.CreateTime || row.CreateTime == ""
        ? ""
        : util.formatDate.format(new Date(row.CreateTime), "yyyy-MM-dd");
    },
    handleCurrentChange(val) {
      this.page = val;
      this.getData1();
    },
    //获取数据2
    getData2() {
      this.listLoading1 = true;
      getERPOrder2().then((res) => {
        if (res.data.success) {
          this.dataset1 = res.data.response;
          this.listLoading1 = false;
          this.$message({
            message: res.data.msg,
            type: "success",
          });
        } else {
          this.listLoading1 = false;
          this.$message({
            message: res.data.msg,
            type: "error",
          });
        }
      });
    },
    getData1() {
      let para = {
        line: this.itemlist1,//线体
        page: this.page,
        intPageSize: this.pagesize,
      };
      this.listLoading1 = true;

      //NProgress.start();
      getERPOrder1(para).then((res) => {
        this.total = res.data.response.dataCount;
        this.dataset1 = res.data.response.data;
        this.listLoading1 = false;
        //NProgress.done();
      });
    },
    


  },

  mounted() {
    //console.log("页面打开成功！");
    this.getData2();

    let routers = window.localStorage.router
      ? JSON.parse(window.localStorage.router)
      : [];
    this.buttonList = getButtonList(this.$route.path, routers);
  },
};
</script>

<style scoped>
</style>
