<template>
  <section>
    <!--查询条件-->
    <el-form ref="form1" :model="form1" label-width="80px">
      <el-row :gutter="0">
        <el-col :span="6">
          <el-form-item label="订单编号">
            <el-input v-model="customerCode"></el-input>
          </el-form-item>
        </el-col>
        <el-col :span="6" v-show="isexpand1">
          <el-form-item label="产品型号">
            <el-input v-model="productname"></el-input>
          </el-form-item>
        </el-col>
        <el-col :span="6">
          <el-form-item label="员工号">
            <el-input v-model="lrr"></el-input>
          </el-form-item>
        </el-col>
        <el-col :span="6">
          <el-form-item label="评审结论">
            <el-select v-model="progress" placeholder="评审结论">
              <el-option
                v-for="item in opsprogress"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              ></el-option>
            </el-select>
          </el-form-item>
        </el-col>

        <el-col :span="6" v-show="isexpand1">
          <el-form-item label="外销编号">
            <el-input v-model="detailnum"></el-input>
          </el-form-item>
        </el-col>
        <el-col :span="2" class="buttonbg">
          <buttonbar
            style="margin-left: 8px"
            :buttonList="buttonList"
            @callFunction="callFunction"
          ></buttonbar>
        </el-col>
        <el-col :span="2">
          <el-form-item label=" " label-width="10px">
            <a @click="lab1event">{{ lab_click1 }}</a>
            <i :class="iconchange1"></i>
          </el-form-item>
        </el-col>
      </el-row>
    </el-form>

    <el-card>
      <div slot="header">
        <span >总数: 
          <a> {{ total }}</a>
          条</span>
      </div>
      <!--列表-->
      <div>
        <el-table
          size="mini"
          style="width: 100%"
          highlight-current-row
          v-loading="listLoading"
          :data="dataset"
          :height="calhight"
          @current-change="selectCurrentRow"
          @selection-change="selsChange"
          @row-dblclick="rowdblclick"
        >
          <el-table-column type="index" width="35"> </el-table-column>
          <el-table-column
            prop="订单编号"
            label="订单编号"
            width="130px"
            show-overflow-tooltip
          >
          </el-table-column>
          <el-table-column
            prop="压缩机型号"
            label="压缩机型号"
            width="130px"
            show-overflow-tooltip
          >
          </el-table-column>
                
          <el-table-column
            prop="下单日期"
            label="下单日期"
            width="135px"
            show-overflow-tooltip
          >
          </el-table-column>
          <el-table-column
            prop="月份"
            label="月份"
            width="100px"
            show-overflow-tooltip
          >
          </el-table-column>
          <el-table-column
            prop="要求完成日期"
            label="要求日期"
            width="135px"
            show-overflow-tooltip
          >
          </el-table-column>
          <el-table-column
            prop="客户"
            label="客户"
            width="135px"
            show-overflow-tooltip
          >
          </el-table-column>
          <el-table-column
            prop="订单数量"
            label="订单数"
            width="60px"
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
            prop="调整后订单数量"
            label="调整数"
            width="60px"
            show-overflow-tooltip
          >
          </el-table-column>
          <el-table-column
            prop="订单状态"
            label="订单状态"
            width="80px"
            show-overflow-tooltip
          >
          </el-table-column>
          <el-table-column
            prop="APS订单特批"
            label="是否特批"
            width="75px"
            show-overflow-tooltip
          >
            <template slot-scope="scope">
              <el-tag
                :type="scope.row.APS订单特批 == '特批' ? 'success' : 'info'"
                disable-transitions
                >{{ scope.row.APS订单特批 == "特批" ? "是" : "否" }}</el-tag
              >
            </template>
          </el-table-column>
          <el-table-column
            prop="评审进度"
            label="评审进度"
            width="75px"
            show-overflow-tooltip
          >
            <template slot-scope="scope">
              <el-tag
                :type="
                  scope.row.评审进度 == '通过'
                    ? 'success'
                    : scope.row.评审进度 == '不通过'
                    ? 'danger'
                    : 'info'
                "
                disable-transitions
                >{{
                  scope.row.评审进度 == "通过" ? "通过" : scope.row.评审进度
                }}</el-tag
              >
            </template>
          </el-table-column>
          <el-table-column
            prop="审核结论"
            label="审核结论"
            width="75px"
            show-overflow-tooltip
          >
            <template slot-scope="scope">
              <el-tag
                :type="scope.row.审核结论 == '不通过' ? 'danger' : 'info'"
                disable-transitions
                >{{ scope.row.审核结论 }}</el-tag
              >
            </template>
          </el-table-column>
          <el-table-column
            prop="技术结论"
            label="技术结论"
            width="75px"
            show-overflow-tooltip
          >
            <template slot-scope="scope">
              <el-tag
                :type="scope.row.技术结论 == '不通过' ? 'danger' : 'info'"
                disable-transitions
                >{{ scope.row.技术结论 }}</el-tag
              >
            </template>
          </el-table-column>
          <el-table-column
            prop="工艺结论"
            label="工艺结论"
            width="75px"
            show-overflow-tooltip
          >
            <template slot-scope="scope">
              <el-tag
                :type="scope.row.工艺结论 == '不通过' ? 'danger' : 'info'"
                disable-transitions
                >{{ scope.row.工艺结论 }}</el-tag
              >
            </template>
          </el-table-column>
          <el-table-column
            prop="质控结论"
            label="质控结论"
            width="75px"
            show-overflow-tooltip
          >
            <template slot-scope="scope">
              <el-tag
                :type="scope.row.质控结论 == '不通过' ? 'danger' : 'info'"
                disable-transitions
                >{{ scope.row.质控结论 }}</el-tag
              >
            </template>
          </el-table-column>
          <el-table-column
            prop="生产结论"
            label="生产结论"
            width="75px"
            show-overflow-tooltip
          >
            <template slot-scope="scope">
              <el-tag
                :type="scope.row.生产结论 == '不通过' ? 'danger' : 'info'"
                disable-transitions
                >{{ scope.row.生产结论 }}</el-tag
              >
            </template>
          </el-table-column>
          <el-table-column
            prop="采购结论"
            label="采购结论"
            width="75px"
            show-overflow-tooltip
          >
            <template slot-scope="scope">
              <el-tag
                :type="scope.row.采购结论 == '不通过' ? 'danger' : 'info'"
                disable-transitions
                >{{ scope.row.采购结论 }}</el-tag
              >
            </template>
          </el-table-column>
          <el-table-column
            prop="包装要求"
            label="包装要求"
            width="135px"
            show-overflow-tooltip
          >
          </el-table-column>
        </el-table>
        <!--工具条-->
        <el-pagination
          layout="prev, pager, next"
          @current-change="handleCurrentChange"
          :page-size="pagesize"
          :total="total"
          style="float: right"
        >
        </el-pagination>
      </div>
    </el-card>
    <el-row :gutter="0" class="flex-item" v-if="editFormVisible">
      <el-col :span="spancol1" v-show="!isexpand">
        <el-card shadow="never">
          <div slot="header">
            <span>订单<a> {{ order }}</a>信息</span>
          </div>
          <div>
            <el-form
              label-width="90px"
              ref="editForm"
              size="mini"
              :model="editForm"
              :rules="editFormRules"
            >
              <el-row :gutter="0">
                <el-col :span="8">
                  <el-form-item  label="凌达编码"
                    prop="凌达编码"
                    label-width="90px"
                  >
                    <el-input v-model="editForm.productid"></el-input>
                  </el-form-item>
                </el-col>
                <el-col :span="8">
                  <el-form-item label="单元码" prop="单元码" label-width="70px">
                    <el-input v-model="data1.单元码" disabled></el-input>
                  </el-form-item>
                </el-col>
                <el-col :span="8">
                  <el-form-item label="要求日期" prop="要求完成日期" >
                    <el-date-picker
                      v-model="editForm.date"
                      type="date"
                      align="right"
                      format="yyyy-MM-dd"
                      value-format="yyyy-MM-dd"
                       style="width: 130px"
                    ></el-date-picker>
                  </el-form-item>
                </el-col>
                </el-row>

                <el-row :gutter="0">
                <el-col :span="8">
                  <el-form-item
                    label="下单数"
                    prop="调整后订单数量"
                    label-width="90px"
                  >
                    <el-input
                      v-model="data1.调整后订单数量"
                      disabled
                    ></el-input>
                  </el-form-item>
                </el-col>
                <el-col :span="8">
                  <el-form-item label="减少数量" prop="调整数" label-width="90px">
                    <el-input v-model="editForm.tznum"></el-input>
                  </el-form-item>
                </el-col>
              </el-row>

              <el-row :gutter="0">
                <el-col :span="23">
                  <el-form-item label="订单备注" prop="备注" label-width="90px" >
                    <el-input v-model="data1.备注" ></el-input>
                  </el-form-item>
                </el-col>
              </el-row>

             <el-row :gutter="0">
                <el-col :span="23">
                  <el-form-item label="调整备注" prop="调整备注">
                      <el-radio-group v-model="radio1" size="mini" @change="radio1change">
                        <el-radio
                          v-for="item in radiolist1"
                          :key="item.id"
                          :label="item.value"
                        >
                          {{ item.label }}
                        </el-radio>
                      </el-radio-group>
                  </el-form-item>
                </el-col>
              </el-row>
              <el-row :gutter="0">
                <el-col :span="23">
                  <el-form-item label=" "  >
                    <el-input v-model="editForm.bz"></el-input>
                  </el-form-item>
                </el-col>
              </el-row>

              <el-row :gutter="0">
                <el-col :span="23">
                  <el-form-item label="邮件通知" prop="邮件通知">
                    <el-select v-model="emails" placeholder="" multiple>
                      <el-option
                        v-for="item in opsemail"
                        :key="item.value"
                        :label="item.label"
                        :value="item.value"
                      >
                      </el-option>
                    </el-select>
                  </el-form-item>
                </el-col>
              </el-row>

            </el-form>
          </div>
        </el-card>
      </el-col>
      <el-col :span="spancol2">
        <el-card shadow="never">
          <div slot="header">
            <span><i class="el-icon-arrow-left" @click="tableexpand"></i></span>

            <span>调整明细</span>
          </div>
          <div>
            <el-card v-show="editFormVisible">
              <div>
                <el-row :gutter="10">
                  <el-col :span="24">
                    <el-button @click.native="editFormVisible = false"
                      >取消</el-button
                    >
                    <el-button
                      type="primary"
                      @click.native="editSubmit"
                      :loading="editLoading"
                      >提交</el-button
                    >
                  </el-col>
                </el-row>
              </div>
            </el-card>
          </div>
        </el-card>
      </el-col>
    </el-row>

    <!--button-->
  </section>
</template>

<script>
import util from "../../../util/date";
import { getOutsellOListPage, editOutsellO } from "../../api/api";

import {
  getOrder,
  editCusOrder,
  addCusOrder,
  getKucun,
  getOrderPage,
  getCompressor,
} from "../../api/api";

import { getButtonList } from "../../promissionRouter";
import Toolbar from "../../components/Toolbar";
import Buttonbar from "../../components/Buttonbar";

export default {
  components: { Toolbar, Buttonbar },
  data() {
    return {
      //search
      form1: {
        item1: null,
        item2: null,
      },
      customerCode: "", //订单编号
      detailnum: "",
      productname: "",
      lrr: "",
      progress: "",
      opsprogress: [
        { value: "待评审", label: "待评审" },
        { value: "会议评审", label: "会议评审" },
        { value: "通过", label: "通过" },
        { value: "不通过", label: "不通过" },
        { value: "", label: "" },
      ],
      lab_click1: "展开",
      isexpand1: false,
      iconchange1: "el-icon-arrow-down",

      //查询table1
      dataset: [],
      data1:{},//一条数据
      total: 0,
      page: 1,
      pagesize: 15,
      listLoading: false,

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
      spancol1: 12,
      spancol2: 12,
      isexpand: false,
      emails: [],
      opsemail: [],

      //1.
      date_xd: "", //下单日期：[getdate]
      month_list: [], //月份：[2020-11 X 6]*
      date_request: "", //要求完成日期：["到货日期"]-2days
      count_stock: 0, //库存库存：🔴 *
      ddnum: "", //外销编号：[@销售类别change]*

      //2.
      customor_id: "", //客户料号：["客户编码"]
      customor_name: "", //客户：Cells["客户名称"]
      ordertype: "", //订单类型[null]*
      count_order: 0, //订单数量：["数量"]*
      count_xd: 0, //下单数量：=["数量"]

      //
      order: {},

      buttonList: [],
      currentRow: null,
      data2: {},
      filters: {
        LinkUrl: "",
      },

      curmonth: "",
      statusList: [
        { LinkUrl: "激活", value: true },
        { LinkUrl: "禁用", value: false },
      ], //"合肥凌达", "郑州凌达", "武汉凌达", "重庆凌达", ""

      addDialogFormVisible: false,
      editFormVisible: false, //编辑界面是否显示
      editLoading: false,
      editFormRules: {
        LinkUrl: [
          { required: true, message: "请输入接口地址", trigger: "blur" },
        ],
      },
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

      addFormVisible: false, //新增界面是否显示
      addLoading: false,
      addFormRules: {
        LinkUrl: [
          { required: true, message: "请输入接口地址", trigger: "blur" },
        ],
      },
      //新增界面数据

      exForm: {
        库存: 0,
        生产基地: "",
      },
      Compressordata: {
        结构类型: "",
        制冷剂: "",
        电源: "",
      },
      addForm: {
        下单日期: "",
        月份: "",
        要求完成日期: "",
        //🔴库存 *
        订单编号: "",

        客户料号: "",
        客户: "",
        订单类型: "",
        订单数量: "",
        首下订单数量: "",

        包装要求: "",
        批次: "",
        销售类别: "",
        生产基地: "",
        备注: "",
        //  下单依据: "",

        凌达编码: "",
        单元码: "",
        使用状态: "",
        铭牌: "",
        安装方式: "",
        冷媒: "",

        压缩机型号: "",
        压缩机阶段: "",
        频类: "",
        系列: "",
        缸数: "",

        附件: "",
        调整后订单数量: "",
        生产计划数量: "",
        生产完成数量: "",
        订单完成情况: "",
        可排数量: "",
        备料情况: "",
        已排数量: "",
        装配数量: "",
        员工号: "",
        外销系统编号: "",
        评审进度: "",
        评审时间: "",
        优先线体: "",
        备用线体: "",
        参与评审人员: "",

        审核主评: "",
        审核主评C: "",

        技术主评: "",
        技术主评C: "",

        工艺主评: "",
        工艺主评C: "",

        质控主评: "",
        质控主评C: "",

        生产主评: "",
        生产主评C: "",

        采购主评: "",
        采购主评C: "",

        入库数量: "",
        入库短缺数: "",
        订单状态: "",
        订单状态备注: "",

        员工: "",
        备料说明: "",
        会议评审备注: "",
        ERP完成数量: "",
        ERP活动数量: "",
        底板类型: "",
        APS读取状态: "",
        APS订单特批: "",
        订单调整状态: "",
        业务员: "",
        订单状态1: "",
        客户编码: "",
        // CreateBy: "",
        // CreateId: "",
        // LinkUrl: "",
        // Name: "",
        // Enabled: "",
      },
    };
  },
  computed: {
    calhight: function () {
      if (this.editFormVisible) {
        return window.innerHeight / 3;
      } else {
        return document.documentElement.clientHeight - 400;
      }
    },
  },
  methods: {
    lab1event() {
      this.isexpand1 = !this.isexpand1;
      if (this.isexpand1) {
        (this.lab_click1 = "收起"), (this.iconchange1 = "el-icon-arrow-up");
      } else {
        (this.lab_click1 = "展开"), (this.iconchange1 = "el-icon-arrow-down");
      }
    },
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
      this.isselected = true;
    },
    radio1change() {this.editForm.bz=this.radio1;},
    selsChange: function (sels) {
      this.sels = sels;
      //console.log("sels:");
      //console.log(this.sels);
    },
    jieduanChange: function (val) {
      this.getRevPeo(val);
      //console.log(val);
    },
    rowdblclick(row, column) {},
    callFunction(item) {
      //console.log(item);
      // this.filters = {
      //     name: item.search
      // };
      this.page = 1;
      this[item.Func].apply(this, item);
    },
    //性别显示转换
    formatbcontent: function (row, column) {
      return row.bcontent ? row.bcontent.substring(20) : "N/A";
    },
    formatCreateTime: function (row, column) {
      return !row.需求审核时间e || row.需求审核时间 == ""
        ? ""
        : util.formatDate.format(new Date(row.需求审核时间), "yyyy-MM-dd");
    },
    handleCurrentChange(val) {
      this.page = val;
      this.handleGet();
    },
    callFunction(item) {
      this.page = 1;
      this[item.Func].apply(this, item);
    },

    //获取getOrder
    handleGet() {
      let para = {
        ddnum: this.detailnum, //this.currentRow.下单序列号,
        ordernum: this.customerCode,
        reviewprocess: this.progress,
        lrr: this.lrr,
        pagenum: this.page,
        pagesize: 15,
      };
      getOrderPage(para).then((res) => {
        this.dataset = res.data.response.data;
        this.total = res.data.response.dataCount;
      });
    },

    //显示编辑界面
    handleEdit() {
      if(this.isselected){
        this.editFormVisible = true; //编辑可见
        this.data1=this.currentRow;
        this.order=this.data1.订单编号;
          this.editForm = {
            id:this.data1.ID,
            productid:this.data1.凌达编码,
            tznum: 0,
            date: this.data1.要求完成日期,
            bz: "冲减", };
      }
      else{
      let para = {
        ordernum: this.customerCode,
        lrr: this.lrr,
        pagenum: this.page,
        pagesize: 15,
      };
      getOrderPage(para).then((res) => {
        this.dataset = res.data.response.data;
        this.data1=this.dataset[0];
        this.total = res.data.response.dataCount;
        console.log(this.total);
        if ((this.total == 1)) {
          this.editFormVisible = true; //编辑可见
          this.order=this.data1.订单编号;
          this.editForm = {
            id: this.data1.ID,
            productid:this.data1.凌达编码,
            tznum: 0,
            date: this.data1.要求完成日期,
            bz: "冲减", };
        } else if (this.total > 1) {
          this.$message({
            message: "查询结果有多条订单，请使用其他方法调整",
            type: "error",
          });
        } else {
          this.$message({
            message: "无此订单编号，请重新检查",
            type: "error",
          });
        }
      });

      }
    },
    //新增提交
    editSubmit: function () {
      let _this = this;
      this.$refs.editForm.validate((valid) => {
        if (valid){
            this.editLoading = true;
            let para = Object.assign({}, this.editForm);
            let user = JSON.parse(window.localStorage.user);
            let cusorder = this.order;
            if (user !=null) {
              para.lrrid = user.uID;
              para.lrr = user.uRealName;

              editCusOrder(para).then((res) => {
                if (util.isEmt.format(res)) {
                  this.editLoading = false;
                  return;
                }
                if (res.data.success) {
                  this.editLoading = false;
                  this.$message({
                    message: res.data.msg,
                    type: "success",
                  });
                  this.$refs["editForm"].resetFields();
                  this.$refs["data1"].resetFields();
                  //获取订单page
                  let para = {
                    ordernum: cusorder,
                    pagenum: 1,
                    pagesize: 15,
                  };
                  getOrderPage(para).then((res) => {
                    this.dataset = res.data.response.data; //table
                    this.total = res.data.response.dataCount;
                  });
                } 
                else {
                  this.$message({
                    message: res.data.msg,
                    type: "error",
                  });
                  this.editLoading = false;
                }
              });
            } 
            else {
              this.$message({
                message: "用户信息为空，先登录",
                type: "error",
              });
              _this.$router.replace(
                _this.$route.query.redirect ? _this.$route.query.redirect : "/"
              );
            }


        } 
      });
    },


    getComp() {
      let para = {
        productname: this.currentRow.型号,
      };
      //let d1=new Object();
      //console.log(para);
      getCompressor(para).then((res) => {
        this.Compressordata = res.data.response; //data;
        this.pinlei = res.data.response.电源;
        if (this.pinlei.indexOf("变频") >= 0) {
          this.pinlei = "变频";
        } else {
          this.pinlei = "定频";
        }
      });
    },
  },
  mounted() {
    //this.lrr=JSON.parse(window.localStorage.user).uRealName;
    this.lrr = JSON.parse(window.localStorage.user).uID;
    let routers = window.localStorage.router
      ? JSON.parse(window.localStorage.router)
      : [];
    this.buttonList = getButtonList(this.$route.path, routers);
    console.log(this.buttonList);
  },
};
</script>

<style scoped>
.c1 {
  display: flex;
}
.f1 {
  flex: 0 0 200px;
}
.f2 {
  flex: 1;
}
.flex-item {
  flex: 1 !important;
  flex-basis: 0%;
  overflow: auto;
}
.bg1 {
  background-color: cornflowerblue;
  box-shadow: 1px 1px 3px rgba(0, 0, 0, 0);
}
.bg2 {
  background-color: rgb(237, 200, 100);
}
.bg3 {
  background-color: rgb(237, 155, 100);
}
.el-dialog__header {
  background-color: rgb(237, 155, 100);
}
.el-dialog__body {
  color: black;
  background-color: rgb(238, 214, 198);
}
.el-divider--horizontal {
  color: black;
  font: bolder;
}
.el-card >>> .el-card__body {
  padding: 5px;
}
.el-card >>> .el-card__header {
  font-size: 12px;
  line-height: 1;
  padding: 10px 20px;
}

.el-col >>> .el-form-item--mini {
  margin-bottom: 10px;
}
.el-el-form-item >>> .el-form-item__label {
  text-align: right;
  padding-right: 10px;
  vertical-align: middle;
}
span >>> .el-icon-arrow-left:hover {
  cursor: pointer;
}
a {
  color: #58bf7e;
}
a:hover {
  cursor: pointer;
}
</style>
