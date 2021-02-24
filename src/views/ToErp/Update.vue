<template>
  <section>
    <!--工具条-->
    <el-row :gutter="0">
      <el-col :span="8">
        <el-input
          v-model="item1"
          clearable
          placeholder="订单编号"
          style="width: 200px"
        ></el-input>
        <!--         <el-input
          v-model="item2"
          placeholder="item2"
          style="width: 200px"
        ></el-input> -->
        <el-select v-model="itemlist1" placeholder="状态" style="width: 120px">
          <el-option
            v-for="item in opsitemlist1"
            :key="item.value"
            :label="item.label"
            :value="item.value"
          ></el-option>
        </el-select>
        <el-select v-model="itemlist2" placeholder="线体" style="width: 120px">
          <el-option
            v-for="item in opsitemlist2"
            :key="item.value"
            :label="item.label"
            :value="item.value"
          ></el-option>
        </el-select>
      </el-col>
      <el-col :span="16">
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
        label="创建时间"
        width="150"
        show-overflow-tooltip
      >
      </el-table-column>
      <el-table-column prop="xgdt" label="修改时间" width="150"  show-overflow-tooltip>
      </el-table-column>
      <el-table-column prop="comment" label="日志" width="">
        <template slot-scope="scope">
          <el-popover trigger="hover" placement="top">
            <p v-html="scope.row.comment"></p>
            <div slot="reference" class="name-wrapper">
              <el-tag size="medium">LOG</el-tag>
            </div>
          </el-popover>
        </template>
      </el-table-column>
      <el-table-column prop="states" label="状态" width="">
        <template slot-scope="scope">
          <el-tag
            :type="scope.row.states == 1 ? 'success' : 'warning'"
            disable-transitions
            >{{ scope.row.states == 1 ? "正常" : "待更新" }}
          </el-tag>
        </template>
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

    <!--编辑界面-->

    <!--新增界面-->

  </section>
</template>

<script>
import util from "../../../util/date";
import {
  getModuleListPage,
  removeModule,
  editModule,
  addModule,
} from "../../api/api";
import {
  getERP,
  updateERPNum,
  updateERPNumAuto,
  updateToERP,
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
      itemlist1: 0, //状态
      opsitemlist1: [
        { value: 0, label: "待更新" },
        { value: 1, label: "已更新" },
        { value: null, label: "不区分" },
      ],
      itemlist2: null,
      opsitemlist2: [
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
    //获取用户列表
    getData1() {
      let para = {
        customercode: this.item1,
        states: this.itemlist1,
        line:this.itemlist2,
        pageIndex: this.page,
        pageSize: this.pagesize,
      };
      this.listLoading1 = true;

      //NProgress.start();
      getERP(para).then((res) => {
        this.total = res.data.response.dataCount;
        this.dataset1 = res.data.response.data;
        this.listLoading1 = false;
        //NProgress.done();
      });
    },
    //删除
    // handleDel() {
    //     let row = this.currentRow;
    //     if (!row) {
    //         this.$message({
    //         message: "请选择要删除的一行数据！",
    //         type: "error"
    //         });

    //         return;
    //     }
    //     this.$confirm('确认删除该记录吗?', '提示', {
    //         type: 'warning'
    //     }).then(() => {
    //         this.listLoading1 = true;
    //         //NProgress.start();
    //         let para = {id: row.Id};
    //         removeModule(para).then((res) => {

    //             if (util.isEmt.format(res)) {
    //                 this.listLoading1 = false;
    //                 return;
    //             }
    //             this.listLoading1 = false;
    //             //NProgress.done();
    //             if (res.data.success) {
    //                 this.$message({
    //                     message: '删除成功',
    //                     type: 'success'
    //                 });

    //             } else {
    //                 this.$message({
    //                     message: res.data.msg,
    //                     type: 'error'
    //                 });
    //             }

    //             this.getData1();
    //         });
    //     }).catch(() => {

    //     });
    // },

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
      this.editFormVisible = true;
      //this.editForm = Object.assign({}, row);
      this.editForm = {
        id: row.Id,
        erpcode: row.ErpCode,
      };
    },
    //显示新增界面
    handleAdd() {
      this.addFormVisible = true;
      this.addForm = {
        CreateBy: "",
        LinkUrl: "",
        Name: "",
        Enabled: "true",
      };
    },
    AutoUpdate() {
      let para = {
        bbnum: this.bbnumed,
      };
      this.listLoading1 = true;
      updateERPNumAuto().then((res) => {
        if (res.data.success) {
          this.listLoading1 = false;
          this.$message({
            message: res.data.msg,
            type: "success",
          });
          this.getData1();
        } else {
          this.listLoading1 = false;
          this.$message({
            message: res.data.msg,
            type: "error",
          });
        }
      });
    },

    handleAdjust() {
      let para = {
        bbnum: this.bbnumed,
      };
      updateToERP().then((res) => {
        if (res.data.success) {
          this.$message({
            message: res.data.msg,
            type: "success",
          });
          this.getData1();
        } else {
          this.$message({
            message: res.data.msg,
            type: "error",
          });
        }
      });
    },
    //编辑
    editSubmit: function () {
      this.$refs.editForm.validate((valid) => {
        if (valid) {
          this.$confirm("确认提交吗？", "提示", {}).then(() => {
            this.editLoading = true;
            //NProgress.start();
            let para = Object.assign({}, this.editForm);
            //console.log(para);
            //para.ModifyTime = util.formatDate.format(new Date(), 'yyyy-MM-dd');

            updateERPNum(para).then((res) => {
              if (util.isEmt.format(res)) {
                this.editLoading = false;
                return;
              }
              if (res.data.success) {
                this.editLoading = false;
                //NProgress.done();
                this.$message({
                  message: res.data.msg,
                  type: "success",
                });
                this.$refs["editForm"].resetFields();
                this.editFormVisible = false;
                this.getData1();
              } else {
                this.$message({
                  message: res.data.msg,
                  type: "error",
                });
              }
            });
          });
        }
      });
    },

    //新增
    // addSubmit: function () {
    //   let _this = this;
    //   this.$refs.addForm.validate((valid) => {
    //     if (valid) {
    //       this.$confirm("确认提交吗？", "提示", {}).then(() => {
    //         this.addLoading = true;
    //         //NProgress.start();
    //         let para = Object.assign({}, this.addForm);

    //         para.CreateTime = util.formatDate.format(new Date(), "yyyy-MM-dd");
    //         para.ModifyTime = para.CreateTime;
    //         para.IsDeleted = false;

    //         var user = JSON.parse(window.localStorage.user);

    //         if (user && user.uID > 0) {
    //           para.CreateId = user.uID;
    //           para.CreateBy = user.uRealName;
    //         } else {
    //           this.$message({
    //             message: "用户信息为空，先登录",
    //             type: "error",
    //           });
    //           _this.$router.replace(
    //             _this.$route.query.redirect ? _this.$route.query.redirect : "/"
    //           );
    //         }

    //         addModule(para).then((res) => {
    //           if (util.isEmt.format(res)) {
    //             this.addLoading = false;
    //             return;
    //           }

    //           if (res.data.success) {
    //             this.addLoading = false;
    //             //NProgress.done();
    //             this.$message({
    //               message: res.data.msg,
    //               type: "success",
    //             });
    //             this.$refs["addForm"].resetFields();
    //             this.addFormVisible = false;
    //             this.getData1();
    //           } else {
    //             this.$message({
    //               message: res.data.msg,
    //               type: "error",
    //             });
    //           }
    //         });
    //       });
    //     }
    //   });
    // },

  },

  mounted() {
    //console.log("页面打开成功！");
    this.getData1();

    let routers = window.localStorage.router
      ? JSON.parse(window.localStorage.router)
      : [];
    this.buttonList = getButtonList(this.$route.path, routers);
  },
};
</script>

<style scoped>
</style>
