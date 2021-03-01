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

    <!--表格-->
    <el-card>
      <div slot="header">
        <span >总数: <a> {{ total }}</a>条</span>
      </div>
      <div>
        <el-table
          size="mini"
          style="width: 100%"
          highlight-current-row
          v-loading="listLoading"
          :data="dataset1"
          :height="calhight"
          @current-change="selectCurrentRow"
          @row-dblclick="handelRowDblClick"
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
                    <el-input v-model="editForm.productid" :disabled="isdisabled1"></el-input>
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
                  <el-tooltip class="item" effect="dark" content="原订单备注信息" placement="bottom-start">
                  <el-form-item label="订单备注" prop="备注" label-width="90px" >
                    <el-input v-model="editForm.bz" ></el-input>
                  </el-form-item>
    </el-tooltip>

                </el-col>
              </el-row>

             <el-row :gutter="0">
                <el-col :span="23">
                  <el-tooltip class="item" effect="dark" content="若发送邮件，此备注会随邮件一起发送" placement="bottom-start">
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
    </el-tooltip>

                </el-col>
              </el-row>
              <el-row :gutter="0">
                <el-col :span="23">
                  <el-form-item label=" "  >
                    <el-input v-model="editForm.tzbz"></el-input>
                  </el-form-item>
                </el-col>
              </el-row>

              <el-row :gutter="0">
                <el-col :span="23">
                  <el-form-item label="邮件通知" prop="邮件通知">
                    <el-select v-model="emails" placeholder="" multiple>
                      <el-option
                          v-for="item in opsemail"
                          :key="item.id"
                          :label="item.UserName"
                          :value="item.address"
                      >
                      </el-option>
                    </el-select>
                  </el-form-item>
                </el-col>
              </el-row>

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

            </el-form>
          </div>
        </el-card>
      </el-col>

      <el-col :span="spancol2">
        <el-card shadow="never">
          <div slot="header">
            <span><i class="el-icon-arrow-left" @click="tableexpand"></i></span>
            <!-- <span>调整明细</span> -->
          </div>
          <div>
            <el-card v-show="editFormVisible">
              <div>

              </div>
            </el-card>
          </div>
        </el-card>
      </el-col>
    </el-row>


  </section>
</template>

<script>
import {
  getOrder,
  editCusOrder,
  addCusOrder,
  getOrderPage,
  getEmailAddress,
  getSendMessagePage,
  } from "../../api/api";
import util from "../../../util/date";
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
      dataset1: [],
      data1:{},//一条数据
      total: 0,
      page: 1,
      pagesize: 15,
      listLoading: false,

      //编辑
      isdisabled1:true,
      order:"",
      radio1: "冲减", //投补选项
      radiolist1: [
        { id: 1, label: "冲减", value: "冲减" },
        { id: 2, label: "调整", value: "调整,新订单号" },
        { id: 3, label: "其他", value: "" },
      ],
      isselected: false,
      sels: [], //列表选中列

      emails: null,
      opsemail: [],

      isexpand: false,      
      spancol1: 12,
      spancol2: 12,
      buttonList: [],
      currentRow: null,


      //编辑界面数据
      editForm: {
        id: 0,
        tznum: 0,
        date: "",
        productid: "",
        bz: "冲减",
        llr: "",
        llrid: "",
        listmail:[],
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
      if (this.editFormVisible) {
        return (window.innerHeight /3)-100;
      } else {
        return document.documentElement.clientHeight - 400;
      }
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
      this.isselected = true;
    },
    radio1change() {this.editForm.tzbz=this.radio1;},
    callFunction(item) {
      this.page = 1;
      this[item.Func].apply(this, item);
    },
    handleCurrentChange(val) {
      this.page = val;
      this.handleGet();
    },
    handelRowDblClick(row, column) {},

    //时间显示转换
    formatCreateTime: function (row, column) {
      return !row.需求审核时间e || row.需求审核时间 == ""
        ? ""
        : util.formatDate.format(new Date(row.需求审核时间), "yyyy-MM-dd");
    },

    //获取数据
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
        this.dataset1 = res.data.response.data;
        this.total = res.data.response.dataCount;
      });
    }, 
    //获取邮件地址数据
    // get_EmailAddress() {
    //   let para = {
    //     item1: "生产订单调整", 
    //   };
    //   getEmailAddress(para).then((res) => {
    //     this.opsemail = res.data.response;
    //     console.log(this.opsemail);
    //   });
    // },

    get_EmailAddress() {
      let para = {
        item1: "生产订单调整", 
        pagenum:1,
        pagesize:10,
      };
      getSendMessagePage(para).then((res) => {
        this.opsemail = res.data.response.data;
        console.log(this.opsemail);
      });
    },
    
    //显示编辑界面
    handleEdit() {
      if(this.isselected){
        this.editFormVisible = true; //编辑可见
        this.data1=this.currentRow;
        if(this.data1.APS读取状态==null){this.isdisabled1=false;}else{this.isdisabled1=true;}
        this.order=this.data1.订单编号;
          this.editForm = {
            id:this.data1.ID,
            productid:this.data1.凌达编码,
            tznum: 0,
            listmail:this.emails,
            date: this.data1.要求完成日期,
            bz:this.data1.备注,
            tzbz: "冲减", };
      }
      else{
      let para = {
        ordernum: this.customerCode,
        lrr: this.lrr,
        pagenum: this.page,
        pagesize: 15,
      };
      getOrderPage(para).then((res) => {
        this.dataset1 = res.data.response.data;
        this.data1=this.dataset1[0];
        this.total = res.data.response.dataCount;
        console.log(this.total);
        if ((this.total == 1)) {
          this.editFormVisible = true; //编辑可见
          this.order=this.data1.订单编号;
          this.editForm = {
            id: this.data1.ID,
            productid:this.data1.凌达编码,
            tznum: 0,
            listmail:this.emails,
            date: this.data1.要求完成日期,
            bz:this.data1.备注,
            tzbz: "冲减", };
        } else if (this.total > 1) {
          this.$message({
            message: "查询结果有多条订单，请选择一条进行调整",
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
    //编辑提交
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
              para.listmail=this.emails;
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
                  this.emails=null;
                  this.editFormVisible = false; //编辑不可见
                  //获取订单page
                  let para = {
                    ordernum: cusorder,
                    pagenum: 1,
                    pagesize: 15,
                  };
                  getOrderPage(para).then((res) => {
                    this.dataset1 = res.data.response.data; //table
                    this.total = res.data.response.dataCount;
                  });
                } 
                else {
                  this.$message({
                    message: res.data.msg,
                    type: "error",
                  });
                  this.editFormVisible = false; //编辑不可见
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

  },
  mounted() {
    let routers = window.localStorage.router
      ? JSON.parse(window.localStorage.router)
      : [];
    this.buttonList = getButtonList(this.$route.path, routers);
    this.lrr = JSON.parse(window.localStorage.user).uID;    
    this.get_EmailAddress();
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
.item {
      margin: 4px;
    }
</style>
