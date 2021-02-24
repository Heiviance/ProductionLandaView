<template>
  <section>
    <!--表格1 + 查询条件1-->
    <el-row :gutter="0" class="flex-item">
      <el-col :span="spancol1" v-show="!isexpand">
        <el-card shadow="never">
      <div>
        <el-table
          size="mini"
          style="width: 100%"
          highlight-current-row
          v-loading="listLoading"
          :data="dataset1"
          :height="calhight"
          @current-change="selectCurrentRow"
          @selection-change="selsChange"
          @row-dblclick="handelRowDblClick"
        >
          <el-table-column type="index" width="35"> </el-table-column>
          <el-table-column
            prop="旧版本"
            label="旧版本"
            width="130px"
            show-overflow-tooltip
          >
          </el-table-column>
          <el-table-column
            prop="新版本"
            label="新版本"
            width="130px"
            show-overflow-tooltip
          >
          </el-table-column>
                
          <el-table-column
            prop="下单日期"
            label="下单日期"
            width="100px"
            show-overflow-tooltip
          >
          </el-table-column>
          <el-table-column
            prop="截止日期"
            label="截止日期"
            width="135px"
            show-overflow-tooltip
          >
          </el-table-column>
        </el-table>
      </div>
        </el-card>
      </el-col>
      <el-col :span="spancol2">
        <el-card shadow="never">
    <el-form ref="form2" :model="form2" label-width="80px">
      <el-row :gutter="0">
        <el-col :span="8">
          <el-form-item label="旧版本">
            <el-input v-model="oldbb" clearable></el-input>
          </el-form-item>
        </el-col>
        <el-col :span="8" >
          <el-form-item label="新版本">
            <el-input v-model="newbb" clearable></el-input>
          </el-form-item>
        </el-col>
        <el-col :span="8">
          <el-form-item label="订单编号">
            <el-input v-model="item1"></el-input>
          </el-form-item>
        </el-col>
        <el-col :span="8">
          <el-form-item label="调整情况">
            <el-select v-model="itemlist2" placeholder="调整情况">
              <el-option
                v-for="item in opsitemlist2"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              ></el-option>
            </el-select>
          </el-form-item>
        </el-col>
        <el-col :span="8">
          <el-form-item label="调整方案">
            <el-select v-model="itemlist3" placeholder="调整方案">
              <el-option
                v-for="item in opsitemlist3"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              ></el-option>
            </el-select>
          </el-form-item>
        </el-col>
        <el-col :span="8">
          <el-form-item label="线体">
            <el-select v-model="itemlist4" placeholder="线体">
              <el-option
                v-for="item in opsitemlist4"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              ></el-option>
            </el-select>
          </el-form-item>
        </el-col>
        <el-col :span="2" class="buttonbg">
            <el-button style="margin-left: 8px" type="primary" @click="handleGet2" >查询</el-button>
        </el-col>
<!--         <el-col :span="2">
          <el-form-item label=" " label-width="10px">
            <a @click="lab1event">{{ lab_click1 }}</a>
            <i :class="iconchange1"></i>
          </el-form-item>
        </el-col> -->
      </el-row>
    </el-form>
        </el-card>
      </el-col>
    </el-row>

    <!--表格2-->
    <el-card>
      <div slot="header">
        <span >总数: <a> {{ total2 }}</a>条</span>
      </div>
      <div>
        <el-table
          size="mini"
          style="width: 100%"
          highlight-current-row
          v-loading="listLoading2"
          :data="dataset2"
          :height="calhight2"
        >
          <el-table-column type="index" width="35"> </el-table-column>
          <el-table-column
            prop="id"
            label="id"
            width="35px"
            sortable  show-overflow-tooltip
          >
          </el-table-column>
          <el-table-column
            prop="调整状态"
            label="状态"
            width="80px"
            show-overflow-tooltip
          >
            <template slot-scope="scope">
              <el-tag
                :type="scope.row.调整状态 == 0 ? 'info' : 'success'"
                disable-transitions
                >{{ scope.row.调整状态==0?"未调整":"已调整" }}</el-tag
              >
            </template>
          </el-table-column>
          <el-table-column
            prop="调整情况"
            label="调整情况"
            width="150px"
            show-overflow-tooltip
          >
          </el-table-column>
          <el-table-column
            prop="压缩机型号"
            label="压缩机型号"
            width="130px"
            show-overflow-tooltip
          >
            <template slot-scope="scope">
              {{scope.row.新压缩机型号 == null ? scope.row.旧压缩机型号 : scope.row.新压缩机型号 }} 
            </template>
          </el-table-column>
          <el-table-column
            prop="订单编号"
            label="订单编号"
            width="130px"
            show-overflow-tooltip
          >
            <template slot-scope="scope">
              {{scope.row.新原始订单 == null ? scope.row.旧原始订单 : scope.row.新原始订单 }} 
            </template>
          </el-table-column>              
          <el-table-column
            prop="ERP号"
            label="ERP号"
            width="135px"
            show-overflow-tooltip
          >
          </el-table-column>
          <el-table-column
            prop="首下订单数量"
            label="下单数"
            width="60px"
            show-overflow-tooltip
          >
          </el-table-column>
        <el-table-column
            prop="旧订单编号"
            label="旧订单编号"
            width="135px"
            show-overflow-tooltip 
          >
            <template slot-scope="scope">
              <div style="color:#2579d3;font-weight:bold">
              {{scope.row.旧订单编号 }} 
              </div>
            </template>
          </el-table-column>
        <el-table-column
            prop="旧生产线"
            label="旧生产线"
            width="135px"
            show-overflow-tooltip
          >
          </el-table-column>
          <el-table-column
            prop="旧最小生产日期"
            label="旧生产日期"
            width="100px"
            show-overflow-tooltip
          >
          </el-table-column>
          <el-table-column
            prop="旧数量"
            label="旧数量"
            width="60px"
            show-overflow-tooltip
          >
          </el-table-column> 
          <el-table-column
            prop="旧装配数量"
            label="旧装配"
            width="60px"
            show-overflow-tooltip
          >
          </el-table-column> 
          <el-table-column
            prop="旧进度更新状态"
            label="旧状态"
            width="150px"
            show-overflow-tooltip
          >
          </el-table-column>

        <el-table-column
            prop="新订单编号"
            label="新订单编号"
            width="135px"
            show-overflow-tooltip 
          >
            <template slot-scope="scope">
              <div style="color:#2579d3;font-weight:bold">
              {{scope.row.新订单编号 }} 
              </div>
            </template>
          </el-table-column>
          <el-table-column
            prop="新生产线"
            label="新生产线"
            width="135px"
            show-overflow-tooltip
          >
          </el-table-column>
          <el-table-column
            prop="新最小生产日期"
            label="新生产日期"
            width="100px"
            show-overflow-tooltip
          >
          </el-table-column>
          <el-table-column
            prop="新数量"
            label="新数量"
            width="60px"
            show-overflow-tooltip
          >
          </el-table-column>    
          <el-table-column
            prop="新装配数量"
            label="新装配"
            width="60px"
            show-overflow-tooltip
          >
          </el-table-column> 
          <el-table-column
            prop="新进度更新状态"
            label="新状态"
            width="150px"
            show-overflow-tooltip
          >
          </el-table-column>

        </el-table>
        <el-pagination
          layout="prev, pager, next"
          @current-change="handleCurrentChange"
          :current-page="page2"
          :page-size="pagesize2"
          :total="total2"
          style="float: right"
        >
        </el-pagination>
      </div>
    </el-card>

  </section>
</template>

<script>
import {
  getAPSVersionPage,getVersionAnalysisPage
  } from "../../api/api";
import util from "../../../util/date";
//import { getButtonList } from "../../promissionRouter";
//import Toolbar from "../../components/Toolbar";
import Buttonbar from "../../components/Buttonbar";

export default {
  components: {  Buttonbar },
  data() {
    return {
      //search
      form1: {
        item1: null,
      },
      //search2
      form2: {
        item1: null,
      },
      newbb:"",
      oldbb:"",
      item1: "",
      item5: "排除",
      itemlist2: "",
      opsitemlist2: [
        { value: null, label: "不区分" },          
        { value: "已调整", label: "已调整" },
        { value: "未调整", label: "未调整" },
      ],
      itemlist3: "",
      opsitemlist3: [ 
        { value: null, label: "全部" },         
        { value: "04.", label: "时间调整" },
        { value: "06.", label: "数量调整" },
        { value: "07.", label: "订单卸载" },
        { value: "03.", label: "订单新增" },
        { value: "03.线体变化", label: "线体调整" },
        { value: "01.", label: "订单完结" },
        { value: "02.", label: "部分完结" },
      ],
      itemlist4: "",
      opsitemlist4: [
        { value: null, label: "全部" },
        { value: "珠海A线", label: "珠海A线" },
        { value: "珠海B线", label: "珠海B线" },
        { value: "珠海C线", label: "珠海C线" },
        { value: "珠海D线", label: "珠海D线" },
        { value: "珠海E线", label: "珠海E线" },
        { value: "珠海F线", label: "珠海F线" },
        { value: "珠海G线", label: "珠海G线" },
        { value: "珠海H线", label: "珠海H线" }, 
      ],
      
      lab_click1: "展开",
      isexpand1: false,
      iconchange1: "el-icon-arrow-down",

      //查询table1
      dataset1: [],
      data1:{},//一条数据
      total: 0,
      page: 1,
      pagesize: 15,
      listLoading: false,


      //查询table2
      dataset2: [],
      data2:{},//一条数据
      total2: 0,
      page2: 1,
      pagesize2: 15,
      listLoading2: false,

      //编辑
      order:"",
      radio1: "冲减", //投补选项
      radiolist1: [
        { id: 1, label: "冲减", value: "冲减" },
        { id: 2, label: "调整", value: "调整,新订单号" },
        { id: 3, label: "其他", value: "" },
      ],
      isselected: false,
      sels: [], //列表选中列

      pinlei: "",
      reviewList: [{ value: "批量" }, { value: "小批" }, { value: "样机" }],
      addVisible: false,
      spancol1: 10,
      spancol2: 14,
      isexpand: false,
      emails: [],
      opsemail: [],

      //buttonList: [],
      currentRow: null,


      //编辑界面数据
      editForm: {
        id: 0,
        tznum: 0,
        date: "",
        productid: "",
        bz: "冲减",
        llr: null,
        llrid: null,
      },
      editFormVisible: false, //编辑界面是否显示
      editLoading: false,
      editFormRules: {
        LinkUrl: [
          { required: true, message: "请输入接口地址", trigger: "blur" },
        ],
      },

      //新增界面数据
      addForm: {
        客户料号: "",
        // CreateBy: "",
        // CreateId: "",
        // LinkUrl: "",
        // Name: "",
        // Enabled: "",
      },
      addFormVisible: false, //新增界面是否显示
      addLoading: false,
      addFormRules: {
        LinkUrl: [
          { required: true, message: "请输入接口地址", trigger: "blur" },
        ],
      },    

    };
  },
  computed: {
    calhight: function () {
      return (window.innerHeight /3)-100;
    },
    calhight2: function () {
      return (window.innerHeight /3)+100;
    },
  },
  methods: {
    //收缩展示1
    lab1event() {
      this.isexpand1 = !this.isexpand1;
      if (this.isexpand1) {
        (this.lab_click1 = "收起"), (this.iconchange1 = "el-icon-arrow-up");
      } else {
        (this.lab_click1 = "展开"), (this.iconchange1 = "el-icon-arrow-down");
      }
    },
    //收缩展示2
    tableexpand() {
      this.isexpand = !this.isexpand;
      if (this.isexpand) {
        this.spancol1 = 0;
        this.spancol2 = 24;
      } else {
        this.spancol1 = 12;
        this.spancol2 = 12;
      }
    },

    selectCurrentRow(val) {
      this.currentRow = val;
      this.newbb=this.currentRow.新版本;
      this.oldbb=this.currentRow.旧版本;
      this.page2=1;
      this.isselected = true;
    },
    radio1change() {this.editForm.bz=this.radio1;},
    selsChange: function (sels) {this.sels = sels;},

    callFunction(item) {
      this.page = 1;
      this[item.Func].apply(this, item);
    },
    handleCurrentChange(val) {
      this.page2 = val;
      this.handleGet2();
    },
    handelRowDblClick(row, column) {},

    //时间显示转换
    formatCreateTime: function (row, column) {
      return !row.需求审核时间e || row.需求审核时间 == ""
        ? ""
        : util.formatDate.format(new Date(row.需求审核时间), "yyyy-MM-dd");
    },

    //获取bb数据
    handleGet() {
      let para = {
        pagenum: this.page,
        pagesize: 10,
      };
      getAPSVersionPage(para).then((res) => {
        this.dataset1 = res.data.response.data;
        this.total = res.data.response.dataCount;
      });
    },
    //获取bb数据
    handleGet2() {
      let para = {
        newbb: this.newbb, //this.currentRow.下单序列号,
        oldbb: this.oldbb,
        item1: this.item1,
        item2: this.itemlist2,
        item3: this.itemlist3,
        item4: this.itemlist4,
        pagenum: this.page2,
        pagesize: 15,
      };
      getVersionAnalysisPage(para).then((res) => {
        this.dataset2 = res.data.response.data;
        this.total2 = res.data.response.dataCount;
      });
    },

    //显示编辑界面



  },
  mounted() {
    // let routers = window.localStorage.router
    //   ? JSON.parse(window.localStorage.router)
    //   : [];
    // this.buttonList = getButtonList(this.$route.path, routers);
    //this.lrr = JSON.parse(window.localStorage.user).uID;   
    this.handleGet(); 
  },
};
</script>

<style scoped>
a {
  color: #58bf7e;
}
a:hover {
  cursor: pointer;
}
.liststyle1{
  background-color: #2579d3;
}
</style>
