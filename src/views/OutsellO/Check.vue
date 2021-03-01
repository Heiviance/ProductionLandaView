<template>
  <section>
    <!--查询条件-->
    <el-form ref="form1" :model="form1" label-width="80px">
     <el-row :gutter="0">
      <el-col :span="6" class="toolbar">
        <el-form-item label="外销编号">
        <el-input v-model="detailnum" placeholder="外销编号" clearable></el-input>
        </el-form-item>
      </el-col>
      <el-col :span="6">
        <el-form-item label="产品型号">
        <el-input v-model="productname"  placeholder="产品型号" clearable></el-input>
        </el-form-item>
        </el-col>
      <el-col :span="5">
        <el-form-item  label="订单分类">
        <el-select v-model="flag" placeholder="订单分类">
          <el-option
            v-for="item in opsflag"
            :key="item.value"
            :label="item.label"
            :value="item.value"
          ></el-option>
        </el-select>          
        </el-form-item></el-col>
      <el-col :span="7" class="buttonbg">
        <buttonbar
          :buttonList="buttonList"
          @callFunction="callFunction"
        ></buttonbar>
      </el-col>
     </el-row>
    </el-form>

    <!--表格-->
    <el-card>
      <div slot="header">
        <span >总数: <a> {{ total }}</a>条</span>
      </div>
      <div>
        <el-table
          :data="dataset"
          :height="calhight"
          highlight-current-row
          size="mini"
          v-loading="listLoading"
          style="width: 100%"
          @current-change="selectCurrentRow"
          @row-dblclick="rowdblclick"
        >
          <el-table-column type="index" width="35"> </el-table-column>
          <el-table-column
            prop="下单序列号"
            label="外销编号"
            width="170"
            show-overflow-tooltip
          >
          </el-table-column>
          <el-table-column
            prop="项目阶段"
            label="项目阶段"
            width="80"
          >
        <template slot-scope="scope">
          <el-tag
            :type="scope.row.项目阶段==null ?'danger' : 'success' "
            disable-transitions
          >{{scope.row.项目阶段==null ? "异常": scope.row.项目阶段}}</el-tag>
        </template>
          </el-table-column> 

          <el-table-column
            prop="录入人"
            label="录入人"
            width="80"
            show-overflow-tooltip
          >
          </el-table-column>
          <el-table-column
            prop="客户编码"
            label="客户编码"
            width="120"
            show-overflow-tooltip
          >
          </el-table-column>          
          <el-table-column
            prop="客户名称"
            label="客户名称"
            width="150"
            show-overflow-tooltip
          >
          </el-table-column>
          <el-table-column prop="确认凌达代码" label="凌达代码" width="130">
          </el-table-column>
          <el-table-column prop="型号" label="型号" width="150" show-overflow-tooltip>
          </el-table-column>
          <el-table-column prop="数量" label="数量" width="50">
          </el-table-column>
          <el-table-column prop="剩余发货数量" label="发货余数" width="80">
          </el-table-column>

          <el-table-column prop="到货日期" label="到货日期" width="100">
          </el-table-column>
          <el-table-column
            prop="客户化特殊需求"
            label="客户化特殊需求"
            width="130"
            show-overflow-tooltip
          >
          </el-table-column>
          <el-table-column prop="铭牌" label="铭牌" width="80">
          </el-table-column>
          <el-table-column prop="认证" label="认证" width="80">
          </el-table-column>
          <el-table-column
            prop="外包装"
            label="包装"
            width="100"
            show-overflow-tooltip
          >
          </el-table-column>
          <el-table-column prop="压缩机类别" label="压缩机类别" width="80" show-overflow-tooltip>
          </el-table-column>
          <el-table-column prop="技术文件" label="技术文件" width="100" show-overflow-tooltip>
          </el-table-column>
          <el-table-column
            prop="代码分配时间"
            label="分配时间"
            :formatter="formatCreateTime2"
            width="100">
          </el-table-column>          
          <el-table-column
            prop="需求审核时间"
            label="需核时间"
            :formatter="formatCreateTime"
            width="100">
          </el-table-column>

        </el-table>
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

    <!--编辑界面-->
    <el-row :gutter="0" class="flex-item">
      <el-col :span="spancol1" v-show="!isexpand">
        <el-card shadow="never">
          <div slot="header">
            <span>订单<a> {{ cobh }}</a>头信息</span>
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
                  <el-form-item
                    label="月份"
                    prop="月份"
                    size="mini"
                    label-width="70px"
                  >
                    <el-select v-model="curmonth">
                      <el-option
                        v-for="(item, key) in opsMonths"
                        :key="key"
                        :label="item"
                        :value="item"
                      ></el-option>
                    </el-select>
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
                  <el-form-item label="客户编码" prop="客户编码">
                    <el-input v-model="addForm.客户编码" disabled></el-input>
                  </el-form-item>
                </el-col>
                <el-col :span="8">
                  <el-form-item label="包装要求" prop="包装要求">
                    <el-input v-model="addForm.包装要求"></el-input>
                  </el-form-item>
                </el-col>
              </el-row>
              <el-row :gutter="10">
                <el-col :span="7">
                  <el-form-item
                    label="库存数"
                    prop="库存数量"
                    label-width="70px"
                  >
                    <el-input v-model="exForm.库存" disabled></el-input>
                  </el-form-item>
                </el-col>
                <!--     <el-col :span="5">
            <el-form-item label="订单数" prop="订单数量" label-width="70px">
              <el-input v-model="addForm.订单数量" disabled></el-input>
            </el-form-item>
          </el-col> -->
                <el-col :span="8">
                  <el-form-item
                    label="下单数"
                    prop="首下订单数量"
                    label-width="70px"
                    required
                  >
                    <el-input
                      v-model="addForm.首下订单数量"
                      @blur="OrderNumChange()"
                    ></el-input>
                  </el-form-item>
                </el-col>
                <el-col :span="8">
                  <el-form-item label="销售类别" prop="销售类别" required>
                    <el-select v-model="addForm.销售类别" placeholder="请选择">
                      <el-option label="外销内销" value="外销内销"></el-option>
                      <el-option label="外销出口" value="外销出口"></el-option>
                    </el-select>
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
                <el-col :span="10">
                  <el-form-item label="生产基地" prop="生产基地">
                    <el-input v-model="exForm.生产基地" disabled></el-input>
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
                    <el-input v-model="pinlei" disabled></el-input>
                  </el-form-item>
                </el-col>
                <el-col :span="5">
                  <el-form-item label="系列" prop="系列" label-width="60px">
                    <el-input v-model="Compressordata.系列"></el-input>
                  </el-form-item>
                </el-col>
                <el-col :span="5">
                  <el-form-item label="缸数" prop="缸数" label-width="60px">
                    <el-input v-model="Compressordata.缸数" disabled></el-input>
                  </el-form-item>
                </el-col>
              </el-row>

              <el-row :gutter="0">
                <el-col :span="8">
                  <el-form-item label="安装方式" prop="结构类型">
                    <el-input
                      v-model="Compressordata.结构类型"
                      disabled
                    ></el-input>
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
                    <el-input
                      v-model="Compressordata.制冷剂"
                      disabled
                    ></el-input>
                  </el-form-item>
                </el-col>
              </el-row>

              <el-row :gutter="0">
                <el-col :span="8">
                  <el-form-item label="产品阶段" prop="压缩机阶段" required>
                    <el-select
                      v-model="addForm.压缩机阶段"
                      placeholder="请选择"
                    >
                      <el-option
                        v-for="item in reviewList"
                        :key="item.value"
                        :label="item.value"
                        :value="item.value"
                        @change="jieduanChange"
                      ></el-option>
                    </el-select>
                  </el-form-item>
                </el-col>
                <el-col :span="8">
                  <el-form-item label="订单编号" prop="订单编号">
                    <el-input v-model="cobh" disabled></el-input>
                  </el-form-item>
                </el-col>
              </el-row>
              <el-row :gutter="0">
                <el-col :span="4">
                  <el-form-item label="" prop="审核主评" label-width="2px">
                    <span>审核主评:</span><br />
                    <el-input v-model="RevPeodata.审核主评" disabled></el-input>
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
                    <el-input v-model="RevPeodata.工艺主评" disabled></el-input>
                  </el-form-item>
                </el-col>
                <el-col :span="4">
                  <el-form-item label=" " prop="质控主评" label-width="1px">
                    <span>质控主评:</span><br />
                    <el-input v-model="RevPeodata.质控主评" disabled></el-input>
                  </el-form-item>
                </el-col>
                <el-col :span="4">
                  <el-form-item label="" prop="生产主评" label-width="1px">
                    <span>生产主评:</span><br />
                    <el-input v-model="RevPeodata.生产主评" disabled></el-input>
                  </el-form-item>
                </el-col>
                <el-col :span="4">
                  <el-form-item label="" prop="采购主评" label-width="1px">
                    <span>采购主评:</span><br />
                    <el-input v-model="RevPeodata.采购主评" disabled></el-input>
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

            <span>订单明细</span>
          </div>
          <div>
            <el-table
              :data="data1"
              :height="calhight2"
              size="mini"
              border
              style="width: 100%"
            >
              <el-table-column
                prop="订单编号"
                label="订单编号"
                width="100px"
                show-overflow-tooltip
              >
              </el-table-column>
              <el-table-column
                prop="APS读取状态"
                label="是否排产"
                width="70px"
                show-overflow-tooltip
              >
            <template slot-scope="scope">
              <el-tag
                :type="scope.row.APS读取状态 == null ? 'info' : 'success'"
                disable-transitions
                >{{ scope.row.APS读取状态 == null ? "未排产" : "已排产" }}
              </el-tag >
            </template>
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
                prop="评审进度"
                label="评审进度"
                width="75px"
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
                prop="包装要求"
                label="包装要求"
                width="135px"
                show-overflow-tooltip
              >
              </el-table-column>

            </el-table>

            <el-card v-show="addVisible">
              <div>
                <el-row :gutter="10">
                  <el-col :span="24">
                    <el-button @click.native="addFormVisible = false"
                      >取消</el-button
                    >
                    <el-button
                      type="primary"
                      @click.native="addSubmit"
                      :loading="addLoading"
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
  addCusOrder,
  getKucun,
  getOrderPage,
  getMonths,
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
      //search
      form1: {
        item1: null,
        item2: null,
      },
      pinlei: "",
      reviewList: [{ value: "批量" }, { value: "小批" }, { value: "样机" }],
      addVisible: false,
      spancol1: 12,
      spancol2: 12,
      isexpand: false,
      //1.
      date_xd: "", //下单日期：[getdate]
      month_list: [], //月份：[2020-11 X 6]*
      date_request: "", //要求完成日期：["到货日期"]-2days
      count_stock: 0, //库存库存：🔴 *
      ddnum: "", //订单编号：[@销售类别change]*

      //2.
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

      detailnum: "",
      lrr: "",
      productname: "",

      dataset: [],
      selecteddata: {},

      flag: "",
      opsflag: [
        { value: "OK", label: "待处理" },
        { value: "DONE", label: "已下单" },
        { value: "NG", label: "已取消" },
        { value: "", label: "不区分" },
      ],

      curmonth: "",
      opsMonths: [],
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

      isselected: false,
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
        系列:"",
        缸数:"",
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
  computed: {
    calhight: function () {
      return window.innerHeight / 3-50;
    },
    calhight2: function () {
      return window.innerHeight / 3;
    },

    cobh: function () {
      let s1 = "W";
      let s2 = "";
      let s3 = "";
      let s4 = "";
      if (this.addForm != null) {
        // if (this.addForm.客户.indexOf("格力") >= 0) {
        //   s1 = "G";
        // } else {
        //   s1 = "W";
        // }

        if (this.addForm.销售类别.indexOf("内销") >= 0) {
          s2 = "N";
        } else if (this.addForm.销售类别.indexOf("出口") >= 0) {
          s2 = "C";
        }

        if (
          this.addForm.压缩机阶段.indexOf("批量") >= 0 ||
          this.addForm.压缩机阶段.indexOf("量产") >= 0
        ) {
          s3 = "P";
        } else if (this.addForm.压缩机阶段.indexOf("小批") >= 0) {
          s3 = "X";
        } else if (
          this.addForm.压缩机阶段.indexOf("样机") >= 0 ||
          this.addForm.压缩机阶段.indexOf("方案评审中") >= 0
        ) {
          s3 = "Y";
        }
        s4 = this.curmonth.replace("-", "").substring(2, 6);
      }
      return `${s1}${s2}${s3}${s4}`;
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
      if(val!=null)
      { this.isselected = true;
      }
    },
    OrderNumChange() {},
    // selsChange: function (sels) {
    //   this.sels = sels;
    // },
    jieduanChange: function (val) {
      this.getRevPeo(val);
      //console.log(val);
    },
    rowdblclick(row, column) {},
    callFunction(item) {
      this.page = 1;
      this[item.Func].apply(this, item);
    },
    //性别显示转换
    formatbcontent: function (row, column) {
      return row.bcontent ? row.bcontent.substring(20) : "N/A";
    },
    formatCreateTime: function (row, column) {
      return !row.需求审核时间 || row.需求审核时间 == ""
        ? ""
        : util.formatDate.format(new Date(row.需求审核时间), "yyyy-MM-dd");
    },
    formatCreateTime2: function (row, column) {
      return !row.代码分配时间 || row.代码分配时间 == ""
        ? ""
        : util.formatDate.format(new Date(row.代码分配时间), "yyyy-MM-dd");
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
      getOutsellOListPage(para).then((res) => {
        this.total = res.data.response.dataCount;
        this.dataset = res.data.response.data;
        this.listLoading = false;
      });
    },

    //获取
    getOrder(gddnum) {
      let para = {
        ddnum: gddnum, //this.currentRow.下单序列号,
        pagenum: 1,
        pagesize: 10,
      };
      //console.log(para);
      getOrderPage(para).then((res) => {
        this.data1 = res.data.response.data; //table
        //console.log("明细");
      });
    },

    getKC() {
      let para = {
        detailnum: this.addForm.外销系统编号,
      };
      //console.log(para);
      getKucun(para).then((res) => {
        this.exForm.库存 = res.data.response; //data;
      });
    },
    getPB() {
      let para = {
        productid: this.addForm.凌达编码,
      };
      //console.log(para);
      getProdBase(para).then((res) => {
        this.exForm.生产基地 = res.data.response; //data;
        //console.log(this.exForm);
      });
    },
    getComp() {
      let para = {
        productname: this.currentRow.型号,
      };
      getCompressor(para).then((res) => {
        if( res.data.response!=null)
        {
          this.Compressordata = res.data.response; //data;
          this.pinlei = res.data.response.电源;
          if (this.pinlei.indexOf("变频") >= 0) {
            this.pinlei = "变频";
          } else {
            this.pinlei = "定频";
          }
        }
        else
        {
          this.$message({
              message: "请补全压缩机型号信息",
              type: "error",
              });
        }

        //console.log(this.Compressordata);
        //console.log(this.addForm);
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
      //console.log(para);
      addRevPeople(para).then((res) => {
        this.RevPeodata = res.data.response; //data;
        //console.log(this.RevPeodata);
      });
    },
    editRevPeo(val) {
      let user = JSON.parse(window.localStorage.user);
      let para = {
        id: user.uID,
        group: val,
      };
      //console.log(para);
      editRevPeople(para).then((res) => {
        if (res.data.response != null) {
          this.RevPeodata = res.data.response;
        } //data;
        //console.log(this.RevPeodata);
      });
    },

    getRevPeo(val) {
      let user = JSON.parse(window.localStorage.user);
      let para = {
        id: user.uID,
        group: val,
      };
      //console.log(para);
      getRevPeople(para).then((res) => {
        if (res.data.response != null) {
          this.RevPeodata = res.data.response;
        } //data;
        //console.log(this.RevPeodata);
        //this.addForm=
        this.addForm.审核主评 = this.RevPeodata.审核主评;
        this.addForm.审核主评C = this.RevPeodata.审核主评C;
        this.addForm.工艺主评 = this.RevPeodata.工艺主评;
        this.addForm.工艺主评C = this.RevPeodata.工艺主评C;
        this.addForm.质控主评 = this.RevPeodata.质控主评;
        this.addForm.质控主评C = this.RevPeodata.质控主评C;
        this.addForm.生产主评 = this.RevPeodata.生产主评;
        this.addForm.生产主评C = this.RevPeodata.生产主评C;
        this.addForm.采购主评 = this.RevPeodata.采购主评;
        this.addForm.采购主评C = this.RevPeodata.采购主评C;
      });
    },

    getM() {
      getMonths().then((res) => {
        this.opsMonths = res.data; //.response; //data;
        this.curmonth = this.opsMonths[0];
        //console.log(res.data);
      });
    },
    //显示编辑界面
    handleEdit() {
    },
    //显示新增界面object
    handleAdd() {
      //console.log("add:"+this.isselected);
      this.$refs["addForm"].clearValidate();      
      this.$refs["addForm"].resetFields();
      if(this.isselected && !util.isEmt.format(this.currentRow.项目阶段)){
        this.handleAddData();
        this.isselected=false;
      }
      else{
      this.isselected=false;
      let para2 = {
        pagenum: 1,
        pagesize: 10,
        detailnum: this.detailnum,
        flag: this.flag,
      };
      this.listLoading = true;
      getOutsellOListPage(para2).then((res) => {
        this.dataset = res.data.response.data; 
        this.total = res.data.response.dataCount;
        this.listLoading = false;
        if ((this.total == 1)) {
           this.currentRow=this.dataset[0];          
           if (util.isEmt.format(this.currentRow.项目阶段)) {
             this.$message({
              message: "无项目阶段信息，无法下单",
              type: "error",
              });
           }
           else{  this.handleAddData();}        
        }  
        else if (this.total > 1) {
          this.$message({
            message: "查询结果有多条订单，请选择一条进行新增",
            type: "error",
          });
        } 
        else {
          this.$message({
            message: "无此外销订单编号，请重新检查",
            type: "error",
          });
        }
      });

      }
    },
    handleAddData()
    {
      this.getComp();
      if(!util.isEmt.format(this.detailnum))
      {this.getOrder(this.detailnum); }
      else
      {this.getOrder(this.currentRow.下单序列号); }     
      let s1 = this.currentRow.备注;                    //备注
      let s2 = this.currentRow.技术文件;
      let s3 = this.currentRow.客户化特殊需求;
      if (s1 != " ") {
        s1 = "【生产相关信息】：" + s1 + ";";
      }
      if (s2 != " " || null) {
        s2 = "【技术文件】：" + s2 + ";";
      }
      if (s3 != " " || null) {
        s3 = "【客户特殊需求】：" + s3 + ";";
      }
      let date1 = new Date(this.currentRow.到货日期);   //要求完成日期
      date1 = new Date(date1.setHours(date1.getHours() - 48));
      this.addVisible = true;
      this.addForm = {
        下单日期: "",
        月份: "",
        要求完成日期: util.formatDate.format(date1, "yyyy-MM-dd"),
        订单编号: "",
        客户编码: this.currentRow.客户编码,
        客户料号: this.currentRow.客户物料代码,
        客户: this.currentRow.客户名称,
        订单类型: "", //null
        订单数量: this.currentRow.数量,
        首下订单数量: this.currentRow.数量,
        调整后订单数量: this.currentRow.数量,
        包装要求: this.currentRow.外包装,
        批次: "", //null
        销售类别: "", //select["外销内销", "外销出口"]*
        生产基地: "", //select["珠海凌达", "合肥凌达", "郑州凌达", "武汉凌达", "重庆凌达", "" ]
        备注: `${s1}${s2}${s3}`,
        凌达编码: this.currentRow.确认凌达代码,
        单元码: this.currentRow.单元码,
        使用状态: "", //null
        铭牌: this.currentRow.铭牌,
        压缩机型号: this.currentRow.型号,
        压缩机阶段: this.currentRow.项目阶段,
        频类: "",
        外销系统编号: this.currentRow.下单序列号,
        技术主评: this.currentRow.代码审核人,
        技术主评C: this.currentRow.代码审核人ID,
        评审组: "",
        员工号: "",
        默认: "",
      };
      this.getKC();
      this.getPB();
      this.getRevPeo(this.currentRow.项目阶段); 
    },
    //新增提交
    addSubmit: function () {
      let _this = this;
      this.$refs.addForm.validate((valid) => {
        if (valid) {
          this.$confirm("确认提交吗？", "提示", {}).then(() => {
            this.addLoading = true;
            let para = Object.assign({}, this.addForm);
            let p1 = this.currentRow.下单序列号;
            let user = JSON.parse(window.localStorage.user);
            if (user && user.uID > 0) {
              para.员工号 = user.uID;
              para.员工 = user.uRealName;
              para.业务员 = user.uRealName;
            } else {
              this.$message({
                message: "用户信息为空，先登录",
                type: "error",
              });
              _this.$router.replace(
                _this.$route.query.redirect ? _this.$route.query.redirect : "/"
              );
            }

            (para.安装方式 = this.Compressordata.结构类型), //4
              (para.冷媒 = this.Compressordata.制冷剂), //3
              (para.系列 = this.Compressordata.系列), //2
              (para.缸数 = this.Compressordata.缸数), //1
              (para.月份 = this.curmonth),
              (para.频类 = this.pinlei),
              (para.订单编号 = this.cobh),
              addCusOrder(para).then((res) => {
                if (util.isEmt.format(res)) {
                  //nullorempty
                  this.addLoading = false;
                  return;
                }
                if (res.data.success) {
                  this.addLoading = false;
                  this.addVisible = false;
                  this.latestcusorder = res.data.response;
                  //NProgress.done();
                  this.$message({
                    message: res.data.msg,
                    type: "success",
                  });
                  this.$refs["addForm"].resetFields();
                  this.Compressordata = {
                      结构类型: "",
                      制冷剂: "",
                      电源: "",
                      系列:"",
                      缸数:"",
                     },
                  this.data1 = [];
                  this.getOrder(p1);
                } else {
                  this.$message({
                    message: res.data.msg,
                    type: "error",
                  });
                  this.addLoading = false;
                  this.addVisible = false;
                }
              });
            let para3 = {
              ddnum: p1,
            };
            getOrder(para3).then((res) => {
              if (res.data.success) {
                let custorder = res.data.response.订单编号;
                let para2 = {
                  detailnum: p1,
                  pcremark: user.uRealName,
                  ordernum: custorder,
                };
                editOutsellO(para2).then((res) => {
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
                });
              } else {
                this.$message({
                  message: res.data.msg,
                  type: "error",
                });
              }
            });
          });
        } //提交
      });
    },

    //批量删除
  },
  mounted() {
    //this.lrr=JSON.parse(window.localStorage.user).uRealName;
    var vm = this;
    let nowDate = new Date();
    let year = nowDate.getFullYear();
    let month = nowDate.getMonth() + 1;
    let day = nowDate.getDate();
    let endtime;
    if (month < 10) {
      endtime = `${year}-0${month}-${day}`;
    } else {
      endtime = `${year}-${month}-${day}`;
    }

    let user = JSON.parse(window.localStorage.user);
    //this.lrr = user.uID;

    let routers = window.localStorage.router
      ? JSON.parse(window.localStorage.router)
      : [];
    this.buttonList = getButtonList(this.$route.path, routers);
    this.opsMonths = this.getM();
  },
}
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
.el-row >>> .el-col-4 {
  color: rgb(96, 98, 102);
  font-size: 14px;
  background-color: rgb(167, 207, 223);
}
.buttonbg {
  background-color: rgb(255, 255, 255);
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