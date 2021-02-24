<template>
    <section>
        <el-card class="lablestyle hight1">
            <el-row :gutter="10" class="split1">   
                <el-col :span="15">
                    <span class="demonstration">生产基地：</span>
                    <el-select  v-model="curbase" placeholder="生产基地"
                    @change="baseChange"
                    >
                    <el-option v-for="(item,key) in bases" :key="key" :label="item" :value="item"></el-option>
                    </el-select>
                    <el-input v-model="productunitcode" placeholder="单元码" style="width:100px"></el-input>                 
                    <el-input v-model="productname" placeholder="型号" style="width:180px"></el-input>
                    <el-button type="primary" class="pad1" @click="getPlans">查询</el-button>
                </el-col> 
                <el-col :span="4" >
                    <el-form :model="editData">
                      <el-form-item label="数量" prop="zpnum" label-width="90px"
                      :rules="{type:'number',message:'数量必须为数字'}">
                        <el-input v-model.number="editData.zpnum"></el-input>
                    </el-form-item>  
  <!--                   <el-form-item >
                        <el-button type="primary" v-if="isNeedEdit" @click="updateBGong">维护</el-button> 
                    </el-form-item> -->
                    </el-form>
                </el-col>                    
                <el-col  class="buttonstyle2">
                <!--工具条class="toolbar"-->                    
                    <!-- <buttonbar :buttonList="buttonList" @callFunction="callFunction"></buttonbar> -->
                    <el-button type="primary" v-if="isNeedEdit" @click="updateBGong">维护</el-button> 
                </el-col>
            </el-row>        
            <el-row :gutter="10" >
            <el-col :span="15">
              <span class="demonstration">生产线体：</span>
                    <el-select  v-model="curline" placeholder="生产线体">
                        <el-option v-for="(item,key) in lines" :key="key" :label="item" :value="item"></el-option>
                    </el-select>
                <el-select
                  v-model="pickday"
                  placeholder="天数"
                  style="width:100px"         
                >
                  <el-option
                    v-for="item in opsday"
                    :key="item.value"
                    :value="item.value"
                  ></el-option>
            </el-select>                     
            <el-input v-model="productid" placeholder="编码" style="width:180px"></el-input>               
             </el-col>  
             <el-col :span="8" >
                <el-date-picker v-model="plandate" type="date" placeholder="选择日期"></el-date-picker>
                
             </el-col>
            </el-row>  
        
        <!-- <el-row :gutter="10"> -->
        <!--列表1-->
        <el-table :data="dataset" height="300px" 
        highlight-current-row size="mini" v-loading="listLoading" style="width: 100%;"
        @current-change="selectCurrentRow" 
        @selection-change="selsChange" >
            <el-table-column type="index" width="35">
            </el-table-column>
            <el-table-column prop="排产线体" label="排产线体" width="80" >
            </el-table-column>
            <el-table-column prop="单元码" label="单元码" width="60" >
            </el-table-column>
            <el-table-column prop="凌达编码" label="凌达编码" width="100">
            </el-table-column>            
            <el-table-column prop="压缩机型号" label="型号" width="150" show-overflow-tooltip >
            </el-table-column>
            <el-table-column prop="排产数量" label="排产数量" width="50">
            </el-table-column>
            <el-table-column prop="装配数量" label="装配数量" width="50">
            </el-table-column>
            <el-table-column prop="排产时间" label="排产时间" width="130">
            </el-table-column>

            <el-table-column prop="客户料号" label="客户编码"   width="100" >
            </el-table-column>  
            <el-table-column prop="客户" label="客户"   width="150" >
            </el-table-column> 
            <el-table-column prop="订单编号" label="订单编号" width="100" >
            </el-table-column>            
            <el-table-column prop="ID" label="生产编号" width="140" show-overflow-tooltip>
            </el-table-column>


            <el-table-column prop="系列" label="系列" width="60" >
            </el-table-column>
  
        </el-table>
        <!--工具条-->
        <div>
            <el-pagination layout="prev, pager, next" @current-change="handleCurrentChange" :page-size="pagesize"
                           :total="total" style="float:right;">
            </el-pagination>
        </div>            
        <!-- </el-row> -->


        </el-card> 
        <el-card class="hight2">
        <!--列表2-->  
        <el-row :gutter="0">
            <el-col :span="10">
              <span class="demonstration">日期：</span>
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
              <el-button type="primary" class="pad1" @click="getBaoGong1">查询</el-button>
            </el-col>
            <el-col :span="1" class="buttonstyle2">
                <el-button type="primary" v-if="isNeedCancel" @click="cancelBGong">取消</el-button>
            </el-col>
        </el-row>      
        <el-table :data="dataset2" height="250px" 
        highlight-current-row size="mini" v-loading="listLoading2" style="width: 100%;"
        @current-change="selectCurrentRow2" >
            <el-table-column type="index" width="35">
            </el-table-column>
            <el-table-column prop="排产线体" label="下单日期" width="80" >
            </el-table-column>
            <el-table-column prop="单元码" label="月份" width="80" >
            </el-table-column>
            <el-table-column prop="压缩机型号" label="型号" width="100">
            </el-table-column>    

            <el-table-column prop="排产数量" label="排产数量" width="80" >
            </el-table-column>
            <el-table-column prop="装配数量" label="装配数量" width="100" >
            </el-table-column>
            <el-table-column prop="入库数量" label="入库数量"   width="100" >
            </el-table-column>  
            <el-table-column prop="报废数量" label="报废数量"   width="150" >
            </el-table-column> 
            <el-table-column prop="排产时间" label="排产时间" width="100">
            </el-table-column>
            <el-table-column prop="实际生产" label="实际生产" width="220">
            </el-table-column>
  
        </el-table>
                <!--工具条-->
        <div>
            <el-pagination layout="prev, pager, next" @current-change="handleCurrentChange2" :page-size="pagesize2"
                           :total="total2" style="float:right;">
            </el-pagination>
        </div>  
        </el-card>
        <el-divider content-position="left">end</el-divider>





        
    </section>
</template>

<script>
    import util from '../../../util/date'
    import {
        getLine,getBase,getPlanDate1,
        getBaoGong,updateBaoGong,cancelBaoGong } from '../../api/api';
    import { getButtonList } from "../../promissionRouter";
    import Buttonbar from "../../components/Buttonbar";

    export default {
       
       
        components: { Buttonbar },
        data() {
            return {
                user:"",
                bases:[],
                lines:[],
                curbase:"",
                curline:"",
                productunitcode:"",
                editData:{zpnum:0},
                zpnum:0,
                plandate:"",//日期1
                isNeedEdit:false,
                isNeedCancel:false,


                buttonList: [],
                currentRow: null,
                currentRow2: null,
                filters: {
                    LinkUrl: ''
                },
                visitDate:[],//查询日期

                detailnum: "",              
                productid:"",
                productname: "",
                lrr: "",
                pickday: 1,
                opsday:
                [{value:1,label:"11"},
                 {value:2,label:"22"},
                 {value:3,label:"33"}],
                               
                flag: "",
                opsflag:
                [{value:"W",label:"外销"},
                 {value:"G",label:"格力"},
                 {value:"",label:"不区分"}],
                


                visitDate:[],

                dataset: [],
                total: 0,
                page: 1,
                pagesize:10,

                dataset2: [],                
                total2: 0,
                page2: 1,
                pagesize2:10,               
                listLoading: false,
                listLoading2: false,
                sels: [],//列表1选中列
                sels2: [],//列表2选中列

                //编辑界面数据

                //新增界面数据


            }
        },
        methods: {
            selectCurrentRow(val) {
            this.currentRow = val;
            console.log("表1选择行变换");
            console.log(this.currentRow);
            this.getBaoGong2();
            },
            selectCurrentRow2(val) {
            this.currentRow2 = val;
            console.log(this.currentRow2);
            },
            callFunction(item) {
                console.log(item);
                this.page=1;
                this[item.Func].apply(this, item);
            },
            handleCurrentChange(val) {
                console.log("currentchange1");
                this.page = val;
                this.getPlans();},
            handleCurrentChange2(val) {
                this.page2 = val;
                this.getBaoGong1();
            },          
            // formatCreateTime: function (row, column) {
            //     return (!row.需求审核时间e || row.需求审核时间 == '') ? '' : util.formatDate.format(new Date(row.需求审核时间), 'yyyy-MM-dd');
            // },

             //获取基地
            getB() {
                //NProgress.start();
               getBase().then((res) => {
                    this.bases= res.data.response;//data;
                    this.curbase=this.bases[0];
                    console.log(this.bases);
                    //NProgress.done();
                });
            },           
            //获取线体
            getL(val) {
                let para = {
                    bas:val, 
                };
                console.log(para);
                //NProgress.start();
               getLine(para).then((res) => {
                    this.lines= res.data.response;//data;
                    console.log(this.lines);
                    //NProgress.done();
                });
            },   
            //获取计划列表 
            getPlans() {
                let para = {
                    bas:this.curbase,
                    line:this.curline,
                    day:this.pickday,

                    unitcode:this.productunitcode,
                    productid:this.productid, 
                    productname:this.productname, 

                    pagenum:this.page, 
                    pagesize:this.pagesize,
                };
                this.listLoading = true; 
                console.log(para);

                //NProgress.start();
               getPlanDate1(para).then((res) => {
                    console.log(res.data.response);
                    this.total = res.data.response.dataCount;
                    this.dataset = res.data.response.data;
                    this.listLoading = false;
                    console.log(this.dataset);
                    //NProgress.done();
                });
            },
            
            //获取报工表baoGong
            getBaoGong1() {
                let para = {
                    line:this.curline,
                    starttime:this.visitDate[0],
                    endtime:this.visitDate[1],

                    pagenum:this.page2, 
                    pagesize:this.pagesize2,
                };
                //this.listLoading = true; 
                console.log(para);
                //NProgress.start();
               getBaoGong(para).then((res) => {
                    this.dataset2 = res.data.response.data;//.source
                    this.total2=res.data.response.dataCount;
                    //this.listLoading = false;
                    console.log(this.dataset);
                    //NProgress.done();
                });
            }, 
            //获取报工表baoGong
            getBaoGong2() {
                let para = {
                    productionnum:this.currentRow.ID,

                    pagenum:this.page2, 
                    pagesize:this.pagesize2,
                };
                //this.listLoading = true; 
                console.log(para);
                //NProgress.start();
               getBaoGong(para).then((res) => {
                    this.dataset2 = res.data.response.data;//;.source
                    this.total2 = res.data.response.dataCount;
                    //this.listLoading = false;
                    console.log(this.dataset2);
                    //NProgress.done();

                    
                });
            },      
            //更新报工,
            updateBGong() {
            this.zpnum=this.editData.zpnum;
            if(this.zpnum==0)
            {this.$alert("维护数量不可为0")}
            else{
                if(this.currentRow==null)
            {this.$alert("请选择需要维护的数据")}
             else{   
            this.$confirm("型号："+this.currentRow.压缩机型号+",数量："+this.zpnum+"  确认提交吗？", "提示", {}).then(() => {
                let para = {
                    "id": 0,//ADD
                     排产线体:this.currentRow.排产线体,
                     单元码:this.currentRow.单元码,
                     压缩机型号:this.currentRow.压缩机型号,
                     凌达编码:this.currentRow.凌达编码,
                     生产编号:this.currentRow.ID,
                     订单编号:this.currentRow.订单编号,
                     客户:this.currentRow.客户,
                     系列:this.currentRow.系列,
                     排产数量:this.currentRow.排产数量,
                     排产日期:this.currentRow.排产时间,
                     客户编码:this.currentRow.客户料号,

                     实际生产:this.plandate,//
                     装配数量:this.zpnum,//
                     维护人:this.user.uID, //
                     };
                
                //this.listLoading = true; 
                console.log(para);
                //NProgress.start();
                updateBaoGong(para).then((res) => {
                    this.dataset2 = res.data.response.data;//.source
                    this.total2=res.data.response.dataCount;
                    //this.listLoading = false;
                    console.log(this.dataset);
                if (res.data.success) {
                //NProgress.done();
                this.$message({
                  message: res.data.msg,
                  type: "success",
                });
                this.getPlans();
                this.getBaoGong2();
              } else {
                this.$message({
                  message: res.data.msg,
                  type: "error",
                });
              }
                });
                
            })
            }}},           
            //撤销报工
            cancelBGong() {
            if(this.currentRow2==null)
            {this.$alert("请选择需要撤销的数据")}
             else{   
                let para = {
                    id:this.currentRow2.ID,
                    zpnum:this.currentRow2.装配数量,
                    productionnum:this.currentRow2.生产编号,
                };
                //this.listLoading = true; 
                console.log(para);
                //NProgress.start();
               cancelBaoGong(para).then((res) => {
                    this.dataset2 = res.data.response.data;//.source
                    this.total2=res.data.response.dataCount;
                    //this.listLoading = false;
                    console.log(this.dataset);
                    //NProgress.done();
                if (res.data.success) {
                //NProgress.done();
                this.$message({
                  message: res.data.msg,
                  type: "success",
                });
                this.getPlans();
                this.getBaoGong2();
              } else {
                this.$message({
                  message: res.data.msg,
                  type: "error",
                });
              }
            }
                
                );
            }},        
            
            
            
            
            //显示编辑界面
            //显示新增界面

            //编辑
            //新增
            //批量删除
            selsChange: function (sels) {
                this.sels = sels;
            },
            selsChange2: function (sels) {
                this.sels2 = sels;
            },
            baseChange(sels) {
                this.getL(this.curbase);
            },
            
        },
        mounted() {
        let nowDate = new Date();
        let year = nowDate.getFullYear();
        let month = nowDate.getMonth() + 1;
        let day = nowDate.getDate();
        let endtime;
        if (month<10){ endtime= `${year}-0${month}-${day}`;}
        else{endtime = `${year}-${month}-${day}`;}

        this.visitDate = [endtime, endtime];
        this.plandate=endtime;


            //visitDate
            this.getB();
            this.user = JSON.parse(window.localStorage.user);

            //this.getPlans();

            let routers = window.localStorage.router
            ? JSON.parse(window.localStorage.router)
            : [];
            this.buttonList = getButtonList(this.$route.path, routers);
            if(this.buttonList.length==2){
                this.isNeedEdit=true;
                this.isNeedCancel=true;}
            else if(this.buttonList.length==1)
                if(this.buttonList[0].name=="维护")
                {this.isNeedEdit=true;}
                else{this.isNeedCancel=true;}

            
            console.log(this.buttonList);

            this.getL("珠海凌达");
        }
    }

</script>

<style scoped>
.lablestyle{
    position: relative;
    left:0;
}
.hight1{
    height: 60%;
}
.hight2{
    height: 40%;
}
.split1{
    padding-bottom: 8px;
}
.buttonstyle{
    position: absolute;
    left:62%;
    top:0;
}
.buttonstyle2{
    position: absolute;
    left:90%;
    top:0;
}
.pad1{
    padding-left: 20px;
}
</style>
