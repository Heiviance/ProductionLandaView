<template>
    <section>
        <!--工具条class="toolbar"-->
        <el-row :gutter="0">
            <el-col :span="20">
              <span class="demonstration">下单时间：</span>
              <el-date-picker
                v-model="visitDate"
                type="daterange"
                align="right"
                format="yyyy-MM-dd"
                value-format="yyyy-MM-dd"
                start-placeholder="下单开始"
                end-placeholder="下单结束"
                style="width:230px"
              ></el-date-picker>
            <el-select
                  v-model="flag"
                  placeholder="客户分类"
                  style="width:100px"         
                >
                  <el-option
                    v-for="item in opsflag"
                    :key="item.value"
                    :label="item.label"
                    :value="item.value"
                  ></el-option>
            </el-select>                     
            <el-input v-model="curordernum" placeholder="订单编号" style="width:130px"></el-input>
            <el-input v-model="productid" placeholder="凌达编码" style="width:130px"></el-input>               
            <el-input v-model="productname" placeholder="产品型号" style="width:140px"></el-input>
            <el-input v-model="lrr" placeholder="员工工号" style="width:120px"></el-input> 
             </el-col>

            <el-col :span="4">
              <buttonbar :buttonList="buttonList" @callFunction="callFunction"></buttonbar>   
            </el-col>   
         </el-row>           

        <!--列表-->
        <el-table :data="dataset" 
        highlight-current-row size="mini" v-loading="listLoading" style="width: 100%;"
        @current-change="selectCurrentRow" 
        @selection-change="selsChange" >
            <el-table-column type="index" width="35">
            </el-table-column>
            <el-table-column prop="下单日期" label="下单日期" width="130" show-overflow-tooltip 
            >
            </el-table-column>
            <el-table-column prop="月份" label="月份" width="70" >
            </el-table-column>
            <el-table-column prop="要求完成日期" label="要求日期" width="90" show-overflow-tooltip>
            </el-table-column>
            <el-table-column prop="订单编号" label="订单编号" width="110" show-overflow-tooltip>
            </el-table-column>
            <el-table-column prop="客户料号" label="客户编码"   width="120" show-overflow-tooltip >
            </el-table-column>  
            <el-table-column prop="客户" label="客户"   width="150" show-overflow-tooltip >
            </el-table-column > 
            <el-table-column prop="凌达编码" label="凌达编码" width="120" show-overflow-tooltip>
            </el-table-column>
            <el-table-column prop="压缩机型号" label="型号" width="150"
            show-overflow-tooltip >
            </el-table-column>
            <el-table-column prop="订单数量" label="订单数量" width="50">
            </el-table-column>
            <el-table-column prop="首下订单数量" label="首下数量" width="50">
            </el-table-column>
            <el-table-column prop="调整后订单数量" label="调整后数" width="50">
            </el-table-column>    
        </el-table>

        <!--工具条-->
        <div>
            <el-pagination layout="prev, pager, next" @current-change="handleCurrentChange" :page-size="pagesize"
                           :total="total" style="float:right;">
            </el-pagination>
        </div>
        
    </section>
</template>

<script>
    import util from '../../../util/date'
    import { getOrderPage  } from '../../api/api';
    import { getButtonList } from "../../promissionRouter";
    import Buttonbar from "../../components/Buttonbar";

    export default {
        components: { Buttonbar },
        data() {
            return {
                buttonList: [],
                currentRow: null,
                filters: {
                    LinkUrl: ''
                },
                visitDate:[],

                curordernum: "",              
                productid:"",
                productname: "",
                lrr: "",
                flag: "",
                opsflag:
                [{value:"W",label:"外销"},
                 {value:"G",label:"格力"},
                 {value:"",label:"不区分"}],
                
                dataset: [],

                total: 0,
                page: 1,
                pagesize:15,
                listLoading: false,
                sels: [],//列表选中列

                //编辑界面数据

                //新增界面数据


            }
        },
        methods: {
            selectCurrentRow(val) {
            this.currentRow = val;
            },
            callFunction(item) {
                console.log(item);
                this.page=1;
                this[item.Func].apply(this, item);
            },
            handleCurrentChange(val) {
                this.page = val;
                this.getOrders();
            },            
            // formatCreateTime: function (row, column) {
            //     return (!row.需求审核时间e || row.需求审核时间 == '') ? '' : util.formatDate.format(new Date(row.需求审核时间), 'yyyy-MM-dd');
            // },

            //获取用户列表 
            getOrders() {
                let para = {
                    date1:this.visitDate[0], 
                    date2:this.visitDate[1],
                    productid:this.productid, 
                    prodname:this.productname, 
                    ordernum:this.curordernum, 
                    lrr:this.lrr,
                    kh:this.flag, 

                    pagenum:this.page, 
                    pagesize:this.pagesize,
                };
                this.listLoading = true;
                console.log(para);

                //NProgress.start();
                getOrderPage(para).then((res) => {
                    this.total = res.data.response.dataCount;
                    this.dataset = res.data.response.data;
                    this.listLoading = false;
                    console.log(this.dataset);
                    //NProgress.done();
                });
            },
            //删除
            //显示编辑界面
            //显示新增界面

            //编辑
            //新增
            //批量删除
            selsChange: function (sels) {
                this.sels = sels;
            },
            
        },
        mounted() {

    var vm = this;
    let nowDate = new Date();
    let year = nowDate.getFullYear();
    let month = nowDate.getMonth() + 1;
    let day = nowDate.getDate();
    let endtime;
    if (month<10){ endtime= `${year}-0${month}-${day}`;}
    else{endtime = `${year}-${month}-${day}`;}
    //var endtime=(new Date()).Format('yyyy-MM-dd');
    let beDate = new Date(nowDate.getTime() - 7 * 24 * 3600 * 1000);
    let beyear = beDate.getFullYear();
    let bemonth = beDate.getMonth() + 1;
    let beday = beDate.getDate();
    let starttime;
    if (month<10){starttime = `${beyear}-0${bemonth}-${beday}`;}
    else{starttime = `${beyear}-${bemonth}-${beday}`;}
    
    //var starttime=beDate.Format('yyyy-MM-dd');
    vm.visitDate = [starttime, endtime];
        let user = JSON.parse(window.localStorage.user);
    this.lrr = user.uID;

            this.getOrders();

            let routers = window.localStorage.router
            ? JSON.parse(window.localStorage.router)
            : [];
            this.buttonList = getButtonList(this.$route.path, routers);
        }
    }

</script>

<style scoped>

</style>
