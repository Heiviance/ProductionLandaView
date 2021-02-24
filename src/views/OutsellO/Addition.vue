<template>
  <section>
    <!--查询条件-->
    <el-row :gutter="0">
      <el-col :span="6" class="toolbar">
        <el-input clearable
          v-model="customerCode"
          placeholder="订单编号"
          style="width: 200px"
        ></el-input>
      </el-col>
      <el-col :span="18" class="buttonbg">
        <buttonbar
          :buttonList="buttonList"
          @callFunction="callFunction"
        ></buttonbar>
      </el-col>
    </el-row>
    <!--查询结果-->
    <el-row :gutter="0" class="flex-item">
      <el-col :span="spancol1" v-show="!isexpand">
        <el-card shadow="never">
          <div slot="header">
            <span>订单信息</span>
          </div>
          <div>
            <el-form
              label-width="90px"
              ref="addForm"
              size="mini"
              :model="addForm"
              :rules="addFormRules"
            >
              <el-row :gutter="0">
                <el-col :span="7">
                  <el-form-item label="月份" prop="月份" label-width="70px">
                    <el-input v-model="addForm.月份" disabled></el-input>
                  </el-form-item>
                </el-col>
                <el-col :span="8">
                  <el-form-item label="要求日期" prop="要求完成日期" required>
                    <el-date-picker
                      v-model="addForm.要求完成日期"
                      type="date"
                      align="right"
                      format="yyyy-MM-dd"
                      value-format="yyyy-MM-dd"
                      style="width: 125px"
                    ></el-date-picker>
                  </el-form-item>
                </el-col>
                <el-col :span="8">
                  <el-form-item
                    label="下单数"
                    prop="首下订单数量"
                    label-width="70px"
                    required
                  >
                    <el-input
                      v-model="addForm.首下订单数量"
                    ></el-input>
                  </el-form-item>
                </el-col>
              </el-row>
              <el-row :gutter="0">
                <el-col :span="7">
                  <el-form-item label="批次" prop="批次" label-width="70px">
                    <el-input
                      v-model="addForm.批次"
                      auto-complete="off"
                    ></el-input>
                  </el-form-item>
                </el-col>
                <el-col :span="8">
                  <el-form-item label="客户料号" prop="客户料号">
                    <el-input v-model="addForm.客户料号" disabled></el-input>
                  </el-form-item>
                </el-col>
                <el-col :span="8">
                  <el-form-item label="包装要求" prop="包装要求">
                    <el-input v-model="addForm.包装要求" disabled></el-input>
                  </el-form-item>
                </el-col>
              </el-row>
              <el-row :gutter="10">
                <el-col :span="8">
                  <el-form-item label="生产基地" prop="生产基地" required>
                    <el-select v-model="addForm.生产基地" placeholder="请选择">
                      <el-option
                        v-for="item in basesList"
                        :key="item.value"
                        :label="item.label"
                        :value="item.value"
                      ></el-option>
                    </el-select>
                  </el-form-item>
                </el-col>
                <el-col :span="8">
                  <el-form-item label="销售类别" prop="销售类别" required>
                    <el-input v-model="addForm.销售类别" disabled></el-input>
                  </el-form-item>
                </el-col>
              </el-row>
              <el-row :gutter="0">
                <el-col :span="23">
                  <el-form-item label="调整状态" prop="订单调整状态" label-width="70px">
                    <el-input v-model="addForm.订单调整状态"></el-input>
                  </el-form-item>
                </el-col>
              </el-row>

              <el-row :gutter="0">
                <el-col :span="23">
                  <el-form-item label="备注" prop="备注" label-width="70px">
                    <el-input v-model="addForm.备注"></el-input>
                  </el-form-item>
                </el-col>
              </el-row>

              <el-row :gutter="0">
                <el-col :span="8">
                  <el-form-item label="编码" prop="凌达编码" label-width="70px">
                    <el-input v-model="addForm.凌达编码" disabled></el-input>
                  </el-form-item>
                </el-col>
                <el-col :span="5">
                  <el-form-item label="单元码" prop="单元码" label-width="70px">
                    <el-input v-model="addForm.单元码" disabled></el-input>
                  </el-form-item>
                </el-col>
              </el-row>

              <el-row :gutter="0">
                <el-col :span="8">
                  <el-form-item
                    label="型号"
                    prop="压缩机型号"
                    label-width="70px"
                  >
                    <el-input v-model="addForm.压缩机型号" disabled></el-input>
                  </el-form-item>
                </el-col>
                <el-col :span="5">
                  <el-form-item label="频类" prop="频类" label-width="60px">
                    <el-input v-model="addForm.频类" disabled></el-input>
                  </el-form-item>
                </el-col>
                <el-col :span="5">
                  <el-form-item label="系列" prop="系列" label-width="60px">
                    <el-input v-model="addForm.系列" disabled></el-input>
                  </el-form-item>
                </el-col>
                <el-col :span="5">
                  <el-form-item label="缸数" prop="缸数" label-width="60px">
                    <el-input v-model="addForm.缸数" disabled></el-input>
                  </el-form-item>
                </el-col>
              </el-row>

              <el-row :gutter="0">
                <el-col :span="8">
                  <el-form-item label="安装方式" prop="结构类型">
                    <el-input v-model="addForm.安装方式" disabled></el-input>
                  </el-form-item>
                </el-col>
                <el-col :span="5">
                  <el-form-item label="铭牌" prop="铭牌" label-width="60px">
                    <el-input v-model="addForm.铭牌" disabled></el-input>
                  </el-form-item>
                </el-col>
                <el-col :span="5">
                  <el-form-item label="状态" prop="订单数量" label-width="60px">
                    <el-input v-model="addForm.使用状态" disabled></el-input>
                  </el-form-item>
                </el-col>
                <el-col :span="5">
                  <el-form-item label="冷媒" prop="制冷剂" label-width="60px">
                    <el-input v-model="addForm.冷媒" disabled></el-input>
                  </el-form-item>
                </el-col>
              </el-row>

              <el-row :gutter="0">
                <el-col :span="8">
                  <el-form-item label="产品阶段" prop="压缩机阶段" >
                    <el-input v-model="addForm.压缩机阶段" disabled></el-input>
                  </el-form-item>
                </el-col>
                <el-col :span="8">
                  <el-form-item label="订单编号" prop="订单编号">
                    <el-input v-model="addForm.订单编号" disabled></el-input>
                  </el-form-item>
                </el-col>
              </el-row>
              <el-row :gutter="0">
                <el-col :span="4">
                  <el-form-item label="" prop="审核主评" label-width="2px">
                    <span>审核主评:</span><br />
                    <el-input v-model="addForm.审核主评" disabled></el-input>
                  </el-form-item>
                </el-col>
                <el-col :span="4">
                  <el-form-item label="" prop="技术主评" label-width="2px">
                    <span>技术主评:</span><br />
                    <el-input v-model="addForm.技术主评" disabled></el-input>
                  </el-form-item>
                </el-col>
                <el-col :span="4">
                  <el-form-item label="" prop="工艺主评" label-width="2px">
                    <span>工艺主评:</span><br />
                    <el-input v-model="addForm.工艺主评" disabled></el-input>
                  </el-form-item>
                </el-col>
                <el-col :span="4">
                  <el-form-item label=" " prop="质控主评" label-width="1px">
                    <span>质控主评:</span><br />
                    <el-input v-model="addForm.质控主评" disabled></el-input>
                  </el-form-item>
                </el-col>
                <el-col :span="4">
                  <el-form-item label="" prop="生产主评" label-width="1px">
                    <span>生产主评:</span><br />
                    <el-input v-model="addForm.生产主评" disabled></el-input>
                  </el-form-item>
                </el-col>
                <el-col :span="4">
                  <el-form-item label="" prop="采购主评" label-width="1px">
                    <span>采购主评:</span><br />
                    <el-input v-model="addForm.采购主评" disabled></el-input>
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
            <!-- v-show="addVisible" -->
            <span>其他参数</span>
          </div>
          <div>
            <el-card>
              <div>
                <el-row>
                  <el-col :span="24">
                    <div>补单类型</div>
                    <div style="margin-top: 10px;margin-bottom: 10px">
                      <el-radio-group v-model="radio1" size="mini">
                        <el-radio
                          v-for="item in radiolist1"
                          :key="item.id"
                          :label="item.value"
                        >
                          {{ item.label }}
                        </el-radio>
                      </el-radio-group>
                    </div>
                  </el-col>
                </el-row>
                <el-row>
                  <el-col :span="24">
                    <div>特批选择</div>
                    <div style="margin-top: 10px;margin-bottom: 10px">
                      <el-radio-group v-model="radio2" size="mini">
                        <el-radio
                          v-for="item in radiolist2"
                          :key="item.id"
                          :label="item.value"
                        >
                          {{ item.label }}
                        </el-radio>
                      </el-radio-group>
                    </div>
                  </el-col>
                </el-row>
              </div>
            </el-card>
          </div>
        </el-card>
      </el-col>
    </el-row>

    <!--查询条件-->
    <el-row :gutter="0">
      <el-col :span="24">
        <el-card>
          <div>
            <!--列表-->
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

            <!--工具条style="float: right"-->
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
  addCusOrderAddition,
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
      //
      //ischeck1:true,
      radio1: "-FX", //投补选项
      radiolist1: [
        { id: 1, label: "下线返修", value: "-FX" },
        { id: 2, label: "整机补投", value: "-ZB" },
        { id: 3, label: "终检补投", value: "-JB" },
      ],
      radio2: true, //投补选项
      radiolist2: [
        { id: 1, label: "是", value: true },
        { id: 2, label: "否", value: false},
      ],
      reviewList: [{ value: "批量" }, { value: "小批" }, { value: "样机" }],
      addVisible: false,
      spancol1: 12,
      spancol2: 12,
      isexpand: false,
      emails: [],
      opsemail: [],
      customerCode: "", //订单编号

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
      data1: [],
      data2: {},
      filters: {
        LinkUrl: "",
      },

      dataset: [],
      selecteddata: {},

      //detailnum: "",
      productname: "",

      //progress: "",
      lrr: "",

      curmonth: "",
      statusList: [
        { LinkUrl: "激活", value: true },
        { LinkUrl: "禁用", value: false },
      ], //"合肥凌达", "郑州凌达", "武汉凌达", "重庆凌达", ""
      basesList: [
        { label: "珠海凌达", value: "珠海凌达" },
        { label: "合肥凌达", value: "合肥凌达" },
        { label: "郑州凌达", value: "郑州凌达" },
        { label: "武汉凌达", value: "武汉凌达" },
        { label: "重庆凌达", value: "重庆凌达" },
        { label: "", value: "" },
      ],
      total: 0,
      page: 1,
      pagesize: 15,
      listLoading: false,
      sels: [], //列表选中列

      addDialogFormVisible: false,
      editFormVisible: false, //编辑界面是否显示
      editLoading: false,
      editFormRules: {
        LinkUrl: [
          { required: true, message: "请输入接口地址", trigger: "blur" },
        ],
      },
      addFormVisible: false, //新增界面是否显示
      addLoading: false,
      addFormRules: {
        LinkUrl: [
          { required: true, message: "请输入接口地址", trigger: "blur" },
        ],
      },
      //新增界面数据

      addForm: {
        下单日期: "",
        月份: "",
        要求完成日期: "",
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
      return window.innerHeight / 3;
    },
  },
  methods: {
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
      //this.addForm = val;
      //console.log("row:");
      //console.log(this.currentRow);
      //console.log(this.calhight);
    },
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

    //获取订单
    handleGet() {
      let para = {
        ordernum: this.customerCode,
      };
      getOrder(para).then((res) => {
        console.log(res.data.response);
        if (res.data.response != null) {
          this.addForm = Object.assign({}, res.data.response);
        } else {
          this.$message({
            message: "无此订单编号，请重新检查",
            type: "error",
          });
          this.$refs["addForm"].resetFields();
        }
      });
    },

    //新增提交
    addSubmit: function () {
      let _this = this;
      this.$refs.addForm.validate((valid) => {
        if (valid) {
          this.$confirm("确认提交吗？", "提示", {}).then(() => {
            this.addLoading = true;
            let para = Object.assign({}, this.addForm);
            let user = JSON.parse(window.localStorage.user);
            let cusorder = this.addForm.订单编号 + this.radio1;
            if (user && user.uID > 0) {
              para.员工号 = user.uID;
              para.员工 = user.uRealName;
              para.业务员 = user.uRealName;
              para.订单编号 = cusorder;
              para.APS读取状态=null;
              para.订单状态1=null;
              if(this.radio2){para.APS订单特批 ="特批"}else{para.APS订单特批 =null}
              addCusOrderAddition(para).then((res) => {
                if (util.isEmt.format(res)) {
                  this.addLoading = false;
                  return;
                }
                if (res.data.success) {
                  this.addLoading = false;
                  this.$message({
                    message: res.data.msg,
                    type: "success",
                  });
                  this.$refs["addForm"].resetFields();
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
                  this.addLoading = false;
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
          });
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
</style>
