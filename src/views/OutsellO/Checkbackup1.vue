<template>
  <section>
    <!--工具条-->
    <el-row :gutter="0">
      <el-col :span="16" class="toolbar">
        <el-input
          v-model="detailnum"
          placeholder="外销编号"
          style="width: 200px"
        ></el-input>
        <el-input
          v-model="productname"
          placeholder="产品型号"
          style="width: 150px"
        ></el-input>
        <el-input
          v-model="lrr"
          placeholder="业务员工号"
          style="width: 150px"
        ></el-input>
        <el-select v-model="flag" placeholder="订单分类" style="width: 100px">
          <el-option
            v-for="item in opsflag"
            :key="item.value"
            :label="item.label"
            :value="item.value"
          ></el-option>
        </el-select>
      </el-col>
      <el-col :span="8">
        <buttonbar
          :buttonList="buttonList"
          @callFunction="callFunction"
        ></buttonbar>
      </el-col>
    </el-row>

    <!-- 
备注: null
外包装: "内销非一体化包装"
客户物料代码: null
客户编码: "CPCH107"
技术文件: "TZD2016-2330"
数量: "1"
项目阶段: "批量" -->

    <!--列表-->
    <el-table
      :data="dataset"
      highlight-current-row
      size="mini"
      v-loading="listLoading"
      style="width: 100%"
      @current-change="selectCurrentRow"
      @selection-change="selsChange"
      @row-dblclick="rowdblclick"
    >
      <el-table-column type="selection" width="50"> </el-table-column>
      <el-table-column type="index" width="35"> </el-table-column>
      <el-table-column prop="下单序列号" label="外销编号" width="100" show-overflow-tooltip>
      </el-table-column>
      <el-table-column prop="录入人" label="录入人" width="80" show-overflow-tooltip>
      </el-table-column>
      <el-table-column prop="客户名称" label="客户名称" width="150" show-overflow-tooltip>
      </el-table-column>
      <el-table-column prop="确认凌达代码" label="凌达代码" width="120">
      </el-table-column>
      <el-table-column prop="型号" label="型号" width="130"> </el-table-column>
      <el-table-column prop="数量" label="数量" width="50"> </el-table-column>
      <el-table-column prop="剩余发货数量" label="发货余数" width="50">
      </el-table-column>

      <el-table-column prop="到货日期" label="到货日期" width="100">
      </el-table-column>
      <el-table-column prop="客户化特殊需求" label="客户化特殊需求" width="200"
      show-overflow-tooltip>
        <!--                 <template slot-scope="scope">
                <span v-html="scope.row.客户化特殊需求.substring(0,10)"></span>
                </template>  -->
      </el-table-column>
      <el-table-column prop="铭牌" label="铭牌" width="80"> </el-table-column>
      <el-table-column prop="认证" label="认证" width="80"> </el-table-column>
      <el-table-column prop="外包装" label="包装" width="100" show-overflow-tooltip>
      </el-table-column>
      <el-table-column prop="压缩机类别" label="压缩机类别" width="80">
      </el-table-column>
      <el-table-column prop="技术文件" label="技术文件" width="100">
      </el-table-column>
      <el-table-column
        prop="需求审核时间"
        label="需核时间"
        :formatter="formatCreateTime"
        width="200"
        sortable
      >
      </el-table-column>
    </el-table>

    <!--工具条style="float: right"-->
    <div>
      <el-pagination
        layout="prev, pager, next"
        @current-change="handleCurrentChange"
        :page-size="pagesize"
        :total="total" style="float:right;"
      >
      </el-pagination>
    </div>
    <el-divider content-position="left"></el-divider>
    <!--编辑界面-->

    <!--新增界面-->
    <el-dialog 
      width="90%"
      title="新增"
      top="8vh"
      v-model="addFormVisible"
      :visible.sync="addFormVisible"
      :close-on-click-modal="false"
    >
      <el-form
        label-width="90px"
        ref="addForm"
        size="mini"
        :model="addForm"
        :rules="addFormRules"
      >
        <el-divider content-position="left">1.下单信息</el-divider>
        <el-row :gutter="0">
          <el-col :span="5">
            <el-form-item label="下单日期" prop="下单日期">
              <el-input v-model="addForm.下单日期" ></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="月份" prop="月份" label-width="60px">
              <el-input v-model="addForm.月份" auto-complete="off"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="5">
            <el-form-item label="要求日期" prop="要求完成日期">
              <el-input  v-model="addForm.要求完成日期" ></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="4">
            <el-form-item label="库存数量" prop="库存数量">
              <el-input v-model="exForm.库存" disabled></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="4">
            <el-form-item label="订单编号" prop="订单编号">
              <el-input v-model="addForm.订单编号" disabled></el-input>
            </el-form-item>
          </el-col>
        </el-row>

        <el-row :gutter="0">
          <el-col :span="5">
            <el-form-item label="客户料号" prop="客户料号">
              <el-input  v-model="addForm.客户料号"   ></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="客户" prop="客户" label-width="60px">
              <el-input v-model="addForm.客户" auto-complete="off"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="5">
            <el-form-item label="订单类型" prop="订单数量">
              <el-input v-model="addForm.订单数量" ></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="4">
            <el-form-item label="订单数量" prop="订单数量">
              <el-input
                v-model="addForm.订单数量"
                auto-complete="off"
              ></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="4">
            <el-form-item label="下单数量" prop="下单数量">
              <el-input v-model="addForm.首下订单数量" @blur="getCOTotal(addForm.首下订单数量)"></el-input>
            </el-form-item>
          </el-col>
        </el-row>

        <el-row :gutter="0">
          <el-col :span="6">
            <el-form-item label="包装要求" prop="包装要求">
              <el-input
                v-model="addForm.包装要求"
                auto-complete="off"
              ></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="批次" prop="批次">
              <el-input v-model="addForm.批次" auto-complete="off"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="销售类别" prop="销售类别">
              <el-select v-model="addForm.销售类别" placeholder="请选择">
                <el-option label="外销内销" value="外销内销"></el-option>
                <el-option label="外销出口" value="外销出口"></el-option>
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="生产基地" prop="生产基地">
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
        </el-row>

        <el-form-item label="备注" prop="备注">
          <el-input v-model="addForm.备注" auto-complete="off"></el-input>
        </el-form-item>

        <el-divider content-position="left">2.压缩机信息</el-divider>
        <el-row :gutter="0">
          <el-col :span="5">
            <el-form-item label="编码" prop="凌达编码" label-width="60px">
              <el-input  v-model="addForm.凌达编码" ></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="4">
            <el-form-item label="单元码" prop="单元码">
              <el-input v-model="addForm.单元码" auto-complete="off"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="4">
            <el-form-item label="使用状态" prop="订单数量">
              <el-input
                v-model="addForm.使用状态"
                auto-complete="off"
              ></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="4">
            <el-form-item label="铭牌" prop="铭牌">
              <el-input v-model="addForm.铭牌" ></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="4">
            <el-form-item label="安装方式" prop="结构类型">
              <el-input v-model="Compressordata.结构类型" disabled></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="3">
            <el-form-item label="冷媒" prop="制冷剂" label-width="60px">
              <el-input v-model="Compressordata.制冷剂" disabled></el-input>
            </el-form-item>
          </el-col>
        </el-row>

        <el-row :gutter="0">
          <el-col :span="5">
            <el-form-item label="型号" prop="压缩机型号" label-width="60px">
              <el-input
                v-model="addForm.压缩机型号"
                auto-complete="off"
              ></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="4">
            <el-form-item label="阶段" prop="压缩机阶段">
              <el-input
                v-model="addForm.压缩机阶段"
                auto-complete="off"
              ></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="3">
            <el-form-item label="频类" prop="频类" label-width="60px">
              <el-input v-model="addForm.频类" auto-complete="off"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="3">
            <el-form-item label="系列" prop="系列" label-width="60px">
              <el-input v-model="Compressordata.系列" disabled></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="3">
            <el-form-item label="缸数" prop="缸数" label-width="60px">
              <el-input v-model="Compressordata.缸数" disabled></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="生产基地" prop="生产基地">
              <el-input v-model="exForm.生产基地" disabled></el-input>
            </el-form-item>
          </el-col>
        </el-row>
        <el-divider content-position="left">3.评审信息</el-divider>
        <el-row :gutter="0">
          <el-col :span="10">
            <el-radio-group
              v-model="addForm.压缩机阶段"
              @change="jieduanChange"
            >
              <el-radio label="批量">批量</el-radio>
              <el-radio label="小批">小批</el-radio>
              <el-radio label="样机">样机</el-radio>
            </el-radio-group>
          </el-col>
        </el-row>
        <el-row :gutter="0">
          <el-col :span="4">
            <el-form-item label="员工号" prop="审核主评C">
              <el-input
                v-model="RevPeodata.审核主评C"
              ></el-input>
            </el-form-item>
            <el-form-item label="审核主评" prop="审核主评">
              <el-input
                v-model="RevPeodata.审核主评" disabled
              ></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="4">
            <el-form-item label="员工号" prop="技术主评C">
              <el-input
                v-model="addForm.技术主评C" disabled
              ></el-input>
            </el-form-item>
            <el-form-item label="技术主评" prop="技术主评">
              <el-input
                v-model="addForm.技术主评" disabled
              ></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="4">
            <el-form-item label="员工号" prop="工艺主评C">
              <el-input
                v-model="RevPeodata.工艺主评C" 
              ></el-input>
            </el-form-item>
            <el-form-item label="工艺主评" prop="工艺主评">
              <el-input
                v-model="RevPeodata.工艺主评" disabled
              ></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="4">
            <el-form-item label="员工号" prop="质控主评C">
              <el-input
                v-model="RevPeodata.质控主评C" 
              ></el-input>
            </el-form-item>
            <el-form-item label="质控主评" prop="质控主评">
              <el-input
                v-model="RevPeodata.质控主评" disabled
                auto-complete="off"
              ></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="4">
            <el-form-item label="员工号" prop="生产主评C">
              <el-input
                v-model="RevPeodata.生产主评C" 
              ></el-input>
            </el-form-item>
            <el-form-item label="生产主评" prop="生产主评">
              <el-input
                v-model="RevPeodata.生产主评" disabled
              ></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="4">
            <el-form-item label="员工号" prop="采购主评C">
              <el-input
                v-model="RevPeodata.采购主评C" 
              ></el-input>
            </el-form-item>
            <el-form-item label="采购主评" prop="采购主评">
              <el-input
                v-model="RevPeodata.采购主评" disabled
              ></el-input>
            </el-form-item>
          </el-col>
        </el-row>
        <el-divider content-position="left">4.统计信息</el-divider>
        <el-row :gutter="0">
          <el-col :span="4">
                <el-form-item label="排产合计" prop="排产合计" >
              <el-input  v-model="data2.排产数量" ></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="4">
                <el-form-item label="排产余量" prop="排产余量" >
              <el-input  v-model="data2.排产余量" ></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="4">
                <el-form-item label="排产实需" prop="排产实需" >
              <el-input  v-model="data2.排产实需" ></el-input>
            </el-form-item>
          </el-col>
        </el-row>
        <el-row :gutter="0">
          <el-col :span="4">
                <el-form-item label="需求合计" prop="需求合计">
              <el-input  v-model="data2.需求数量" ></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="4">
                <el-form-item label="库存合计" prop="库存合计">
              <el-input  v-model="data2.库存数量" ></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="4">
                <el-form-item label="未发货合计" prop="未发货合计">
              <el-input  v-model="data2.未发货数量" ></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="4">
                <el-form-item label="余量合计" prop="余量合计" >
              <el-input  v-model="data2.库存余量" ></el-input>
            </el-form-item>
          </el-col>
        </el-row>

        <el-table
          :data="data1" size="mini" border
          style="width: 100%">
          <el-table-column
            prop="外销系统编号"
            label="外销编号"
            width="130px" show-overflow-tooltip>
          </el-table-column>
          <el-table-column
            prop="凌达编码"
            label="凌达编码"
            width="130px" show-overflow-tooltip>
          </el-table-column>
          <el-table-column
            prop="客户"
            label="客户"
            width="130px" show-overflow-tooltip>
          </el-table-column>
          <el-table-column
            prop="需求日期"
            label="需求日期"
            width="130px" show-overflow-tooltip>
          </el-table-column>
          <el-table-column
            prop="排产数量"
            label="排产数量"
            width="80px" show-overflow-tooltip>
          </el-table-column>
          <el-table-column
            prop="需求数量"
            label="需求数量"
            width="80px" show-overflow-tooltip>
          </el-table-column>
          <el-table-column
            prop="库存数量"
            label="库存数量"
            width="80px" show-overflow-tooltip>
          </el-table-column>
          <el-table-column
            prop="未发货数量"
            label="未发货数量"
            width="100px" show-overflow-tooltip>
          </el-table-column>
          <el-table-column
            prop="库存余量"
            label="库存余量"
            width="80px" show-overflow-tooltip>
          </el-table-column>

        </el-table>

      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click.native="addFormVisible = false">取消</el-button>
        <el-button
          type="primary"
          @click.native="addSubmit"
          :loading="addLoading"
          >提交</el-button
        >
      </div>
    </el-dialog>
  </section>
</template>

<script>
import util from "../../../util/date";
import { getOutsellOListPage, editOutsellO } from "../../api/api";

import {
  addCusOrder,
  checkCusOrder,
  getGenDDnum,
  getKucun,
  getCusOrderTotal,
  getCusOrderListPage,
  getProductionProportion,
  getProdBase,
  getCompressor,
  addRevPeople,
  getRevPeople,
  editRevPeople,
} from "../../api/api";

import { getButtonList } from "../../promissionRouter";
import Toolbar from "../../components/Toolbar";
import Buttonbar from "../../components/Buttonbar";

export default {
  components: { Toolbar, Buttonbar },
  data() {
    return {
      //1.
      date_xd: "", //下单日期：[getdate]
      month_list: [], //月份：[2020-11 X 6]*
      date_request: "", //要求完成日期：["到货日期"]-2days
      count_stock: 0, //库存库存：🔴 *
      ddnum: "", //订单编号：[@销售类别change]*

      //2.
      customor_id: "", //客户料号：["客户编码"]
      customor_name: "", //客户：Cells["客户名称"]
      ordertype: "", //订单类型[null]*
      count_order: 0, //订单数量：["数量"]*
      count_xd: 0, //下单数量：=["数量"]

      //
      order: {},

      //包装要求：Cells["外包装"]  //批次[null]  //销售类别：select["外销内销", "外销出口"]*    //生产基地：select["珠海凌达", "合肥凌达", "郑州凌达", "武汉凌达", "重庆凌达", "" ]*
      //备注：【生产相关信息】：Cells["备注"]+【技术文件】：Cells["技术文件"]+【客户化特殊需求】：Cells["客户化特殊需求"]

      //2.
      //凌达编码：["确认凌达代码"]  //单元码：["单元码"]         //使用状态：[null]   //铭牌：["铭牌"]     2 🔴 *
      //压缩机型号：["型号"]        //压缩机阶段：["项目阶段"]           4🔴 *

      //3.技术|员工号：textBox10.Text = Cells["代码审核人ID"]
      //3.技术|主评名：textBox4.Text = Cells["代码审核人"]

      buttonList: [],
      currentRow: null,
      data1:[],
      data2:{},
      filters: {
        LinkUrl: "",
      },

      detailnum: "",
      lrr: "",
      productname: "",
      flag: "OK",
      dataset: [],
      selecteddata: {},
      opsflag: [
        { value: "OK", label: "待处理" },
        { value: "NG", label: "已处理" },
        { value: "", label: "不区分" },
      ],

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
      pagesize: 10,
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
      //编辑界面数据
      editForm: {
        Id: 0,
        CreateBy: "",
        LinkUrl: "",
        Name: "",
        Enabled: false,
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
        结构类型:"",
        制冷剂:"",
      },
      RevPeodata: {
        Id: 1,
        审核主评: "",
        审核主评C: "",
        工艺主评: "",
        工艺主评C: "",
        质控主评: "",
        质控主评C: "",
        生产主评: "",
        生产主评C: "",
        采购主评: "",
        采购主评C: "",
        技术主评: "",
        技术主评C: "",
        评审组: "",
        员工号: "",
        默认: "",
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
  methods: {
    selectCurrentRow(val) {
      this.currentRow = val;
      //this.addForm = val;
      console.log("row:");
      console.log(this.currentRow);
    },
    selsChange: function (sels) {
      this.sels = sels;
      console.log("sels:");
      console.log(this.sels);
    },
    jieduanChange: function (val) {
      this.getRevPeo(val);
      console.log(val);
    },
    rowdblclick(row, column) {},
    callFunction(item) {
      console.log(item);
      // this.filters = {
      //     name: item.search
      // };
      this.page=1;
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
      this.getOutsellOs();
    },
    //获取订单列表
    getOutsellOs() {
      let para = {
        pagenum: this.page,
        pagesize: this.pagesize,
        detailnum: this.detailnum,
        lrr: this.lrr,
        productname: this.productname,
        flag: this.flag,
      };
      this.listLoading = true;
      console.log(para);

      //NProgress.start();
      getOutsellOListPage(para).then((res) => {
        this.total = res.data.response.dataCount;
        this.dataset = res.data.response.data;
        this.listLoading = false;
        console.log(this.dataset);
        //NProgress.done();
      });
    },
    //新增
    AddOutsellOs() {},

    //获取
    //   checkCusOrder,
    //   getGenDDnum,
    getCOTotal(val){
      let para = {
        productid:this.currentRow.确认凌达代码,
        customername: this.currentRow.客户编码,
        xdnum:val,//this.addForm.首下订单数量,
      };
      console.log(para);
      getCusOrderTotal(para).then((res) => {
        this.data1 = res.data.response.CountList;//table
        this.data2 = res.data.response.CountTotal; //汇总
        
      });
      
    },
    //   getCusOrderListPage,
    //   getProductionProportion,
    getKC() {
      let para = {
        detailnum: this.addForm.外销系统编号,
      };
      console.log(para);
      getKucun(para).then((res) => {
        this.exForm.库存 = res.data.response; //data;
      });
    },
    getPB() {
      let para = {
        productid: this.addForm.凌达编码,
      };
      console.log(para);
      getProdBase(para).then((res) => {
        this.exForm.生产基地 = res.data.response; //data;
        console.log(this.exForm);
      });
    },
    getComp() {
      let para = {
        productname: this.currentRow.型号,
      };
      //let d1=new Object();
      console.log(para);
      getCompressor(para).then((res) => {
         this.Compressordata= res.data.response[0]; //data;
        
        console.log(this.Compressordata);
        console.log(this.addForm);
      });
    },
    //  addRevPeople ,
    // getRevPeople ,
    //  editRevPeople ,
    addRevPeo(val) {
      let user = JSON.parse(window.localStorage.user);
      let para = {
        id: user.uID,
        group: val,
      };
      console.log(para);
      addRevPeople(para).then((res) => {
        this.RevPeodata = res.data.response; //data;
        console.log(this.RevPeodata);
      });
    },
    editRevPeo(val) {
      let user = JSON.parse(window.localStorage.user);
      let para = {
        id: user.uID,
        group: val,
      };
      console.log(para);
      editRevPeople(para).then((res) => {
        if( res.data.response!=null){
        this.RevPeodata = res.data.response;} //data;
        console.log(this.RevPeodata);
      });
    },

    getRevPeo(val) {
      let user = JSON.parse(window.localStorage.user);
      let para = {
        id: user.uID,
        group: val,
      };
      console.log(para);
      getRevPeople(para).then((res) => {
         if( res.data.response!=null){
        this.RevPeodata = res.data.response; }//data;
        console.log(this.RevPeodata);
        //this.addForm=
        this.addForm.审核主评=this.RevPeodata.审核主评;
        this.addForm.审核主评C= this.RevPeodata.审核主评C;
        this.addForm.工艺主评= this.RevPeodata.工艺主评;
        this.addForm.工艺主评C= this.RevPeodata.工艺主评C;
        this.addForm.质控主评= this.RevPeodata.质控主评;
        this.addForm.质控主评C= this.RevPeodata.质控主评C;
        this.addForm.生产主评= this.RevPeodata.生产主评;
        this.addForm.生产主评C= this.RevPeodata.生产主评C;
        this.addForm.采购主评= this.RevPeodata.采购主评;
        this.addForm.采购主评C= this.RevPeodata.采购主评C;

     
      });
    },
    getComp() {
      let para = {
        productname: this.currentRow.型号,
      };
      console.log(para);
      getCompressor(para).then((res) => {
        this.Compressordata = res.data.response; //data;
        console.log(this.Compressordata);
      });
    },

    //显示编辑界面
    handleEdit() {
      let row = this.currentRow;
      if (!row) {
        this.$message({
          message: "请选择要编辑的一行数据！",
          type: "error",
        });

        return;
      }
      // this.editFormVisible = true;
      // this.editForm = Object.assign({}, row);
      console.log(row.bID);
      this.$router.replace(`/Blog/Detail/${row.bID}`);
    },
    //显示新增界面object
    handleAdd() {
      this.Compressordata={};
      this.getCOTotal(this.currentRow.数量);
      this.getComp();
      let date1 = new Date(this.currentRow.到货日期);
      date1 = new Date(date1.setHours(date1.getHours() - 48));
      this.addFormVisible = true;
      this.addForm = {
        下单日期: "",
        月份: "",
        要求完成日期: util.formatDate.format(date1, "yyyy-MM-dd"),
        订单编号: "",

        客户料号: this.currentRow.客户编码,
        客户: this.currentRow.客户名称,
        订单类型: "", //null
        订单数量: this.currentRow.数量,
        首下订单数量: this.currentRow.数量 ,

        包装要求: this.currentRow.外包装,
        批次: "", //null
        销售类别: "", //select["外销内销", "外销出口"]*
        生产基地: "", //select["珠海凌达", "合肥凌达", "郑州凌达", "武汉凌达", "重庆凌达", "" ]
        备注: "",

        凌达编码: this.currentRow.确认凌达代码,
        单元码: this.currentRow.单元码,
        使用状态: "", //null
        铭牌: this.currentRow.铭牌,
        安装方式: this.Compressordata.结构类型,//4
        冷媒: this.Compressordata.制冷剂,//3

        压缩机型号: this.currentRow.型号,
        压缩机阶段: this.currentRow.项目阶段,
        频类: "",
        系列: this.Compressordata.系列,//2
        缸数: this.Compressordata.缸数,//1

        外销系统编号: this.currentRow.下单序列号,
        技术主评: this.currentRow.代码审核人,
        技术主评C: this.currentRow.代码审核人ID,

        // 审核主评: "",
        // 审核主评C: "",
        // 工艺主评: "",
        // 工艺主评C: "",
        // 质控主评: "",
        // 质控主评C: "",
        // 生产主评: "",
        // 生产主评C: "",
        // 采购主评: "",
        // 采购主评C: "",

        // 审核主评: this.RevPeodata.审核主评,
        // 审核主评C: this.RevPeodata.审核主评C,
        // 工艺主评: this.RevPeodata.工艺主评,
        // 工艺主评C: this.RevPeodata.工艺主评C,
        // 质控主评: this.RevPeodata.质控主评,
        // 质控主评C: this.RevPeodata.质控主评C,
        // 生产主评: this.RevPeodata.生产主评,
        // 生产主评C: this.RevPeodata.生产主评C,
        // 采购主评: this.RevPeodata.采购主评,
        // 采购主评C: this.RevPeodata.采购主评C,

        评审组: "",
        员工号: "",
        默认: "",


      };

      console.log("1. add form");
      console.log(this.addForm);
      this.getKC();
      this.getPB();
      this.getRevPeo(this.currentRow.项目阶段);
    },
    //编辑

    //新增
    addSubmit: function () {
      let _this = this;
      this.$refs.addForm.validate((valid) => {
        if (valid) {
          this.$confirm("确认提交吗？", "提示", {}).then(() => {
            this.addLoading = true;
            let user = JSON.parse(window.localStorage.user);
            if (user && user.uID > 0) {
              para.CreateId = user.uID;
              para.CreateBy = user.uRealName;
            } else {
              this.$message({
                message: "用户信息为空，先登录",
                type: "error",
              });
              _this.$router.replace(
                _this.$route.query.redirect ? _this.$route.query.redirect : "/"
              );
            }

            let para = Object.assign({}, this.addForm);
            let para2 = {
              detailnum: this.currentRow.下单序列号,
              pcremark: user.uRealName,
            };
            para.CreateTime = util.formatDate.format(new Date(), "yyyy-MM-dd");
            para.ModifyTime = para.CreateTime;
            para.IsDeleted = false;

            addCusOrder(para).then((res) => {
              if (util.isEmt.format(res)) {
                this.addLoading = false;
                return;
              }
              if (res.data.success) {
                this.addLoading = false;
                //NProgress.done();
                this.$message({
                  message: res.data.msg,
                  type: "success",
                });
                this.$refs["addForm"].resetFields();
                this.addFormVisible = false;
                this.getOutsellOs();
              } else {
                this.$message({
                  message: res.data.msg,
                  type: "error",
                });
              }
            });

            editOutsellO(para2).then((res) => {
              if (util.isEmt.format(res)) {
                this.addLoading = false;
                return;
              }
              if (res.data.success) {
                this.addLoading = false;
                //NProgress.done();
                this.$message({
                  message: res.data.msg,
                  type: "success",
                });
                // this.addFormVisible = false;
                // this.getOutsellOs();
              } else {
                this.$message({
                  message: res.data.msg,
                  type: "error",
                });
              }
            });
          });
        }//提交
      });
    },

    //批量删除
  },
  mounted() {
           //this.lrr=JSON.parse(window.localStorage.user).uRealName;
    
    let user = JSON.parse(window.localStorage.user);
    //this.lrr = user.uID;
    this.getOutsellOs();

    let routers = window.localStorage.router
      ? JSON.parse(window.localStorage.router)
      : [];
    this.buttonList = getButtonList(this.$route.path, routers);
  },
};
</script>

<style scoped>
.bg1{
  background-color: cornflowerblue;
  box-shadow: 1px 1px 3px rgba(0,0,0,0);
}
.bg2{
  background-color: rgb(237, 200, 100);
}
.bg3{
  background-color: rgb(237, 155, 100);
}
.el-dialog__header{
  background-color: rgb(237, 155, 100);
}
.el-dialog__body{
  color: black;
  background-color:  rgb(238, 214, 198);
}
.el-divider--horizontal{
  color:black;
  font: bolder;
  
}
</style>
