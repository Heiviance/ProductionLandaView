<template>
    <section>
        <!--工具条class="toolbar"-->
        <el-row :gutter="0">
            <el-col :span="4">
              <span class="demonstration">客户分类：</span>
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
            </el-col>
            <el-col :span="16">
             <span class="demonstration">订单进度：</span>
            <el-select
                  v-model="progress"
                  placeholder="订单进度"
                  style="width:150px"         
                >
                  <el-option
                    v-for="item in opsprogress"
                    :key="item.value"
                    :label="item.label"
                    :value="item.value"
                  ></el-option>
            </el-select>            
            <el-input v-model="detailnum" placeholder="订单编号" style="width:200px"></el-input>
            <el-input v-model="productid" placeholder="凌达编码" style="width:150px"></el-input>               
            <el-input v-model="productname" placeholder="产品型号" style="width:200px"></el-input>

             </el-col>

            <el-col :span="4">
              <buttonbar :buttonList="buttonList" @callFunction="callFunction"></buttonbar>   
            </el-col>   
         </el-row>           

        <!--列表1-->
        <el-table :data="dataset" 
        highlight-current-row size="mini" v-loading="listLoading" style="width: 100%;"
        @current-change="selectCurrentRow" 
        @selection-change="selsChange" >
            <el-table-column type="index" width="35">
            </el-table-column>  
            <el-table-column prop="业务员" label="业务员" width="70" 
            show-overflow-tooltip>
            </el-table-column>
            <el-table-column prop="外销系统编号" label="外销编号" width="150"  show-overflow-tooltip>
            </el-table-column>
            <el-table-column prop="订单编号" label="订单编号" width="120" >
            </el-table-column>
            <el-table-column prop="客户料号" label="客户编号"   width="120"  show-overflow-tooltip>
            </el-table-column>  
            <el-table-column prop="客户" label="客户"   width="160"  show-overflow-tooltip>
            </el-table-column> 
            <el-table-column prop="凌达编码" label="凌达编码" width="120"  show-overflow-tooltip>
            </el-table-column>
            <el-table-column prop="压缩机型号" label="型号" width="130"  show-overflow-tooltip>
            </el-table-column>
            <el-table-column prop="订单数量" label="订单数量" width="50">
            </el-table-column>
            <el-table-column prop="首下订单数量" label="首下数量" width="50">
            </el-table-column>
            <el-table-column prop="调整后订单数量" label="调整后数" width="50">
            </el-table-column>  
            <el-table-column prop="生产计划数量" label="计划数量" width="50">
            </el-table-column>    
            <el-table-column prop="库存数量" label="库存数量" width="50">
            </el-table-column>  
        </el-table>

        <!--工具条-->
        <div>
            <el-pagination layout="prev, pager, next" @current-change="handleCurrentChange" :page-size="pagesize"
                           :total="total" style="float:right;">
            </el-pagination>
        </div>

        <!--查看生产时间界面-->
        <el-dialog title="生产时间" width="90%"
        v-model="productionFormVisible"
        :visible.sync="productionFormVisible"  :close-on-click-modal="false">
            <el-table :data="dataset2" 
            highlight-current-row size="mini" style="width: 100%;" >
            <el-table-column type="index" width="35">
            </el-table-column>  
            <el-table-column prop="客户" label="客户"   width="150" show-overflow-tooltip>
            </el-table-column> 
            <el-table-column prop="单元码" label="单元码" width="80">
            </el-table-column>
            <el-table-column prop="凌达编码" label="凌达编码" width="120">
            </el-table-column>
            <el-table-column prop="包装要求" label="包装要求" width="120">
            </el-table-column>
            <el-table-column prop="排产线体" label="排产线体" width="120">
            </el-table-column>
            <el-table-column prop="排产时间" label="排产时间" width="130" show-overflow-tooltip>
            </el-table-column>
            <el-table-column prop="排产数量" label="排产数量" width="50">
            </el-table-column>  
            <el-table-column prop="装配数量" label="装配数量" width="50">
            </el-table-column>  
            <el-table-column prop="备注" label="备注" width="200"
            show-overflow-tooltip>
            </el-table-column>   
        </el-table>

            <div slot="footer" class="dialog-footer">
                <el-button @click.native="productionFormVisible = false">关闭</el-button>
            </div>
        </el-dialog>
        
    </section>
</template>

<script>
    import util from '../../../util/date'
    import { getOrderProgerssPage ,getPlanDate2 } from '../../api/api';
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

                detailnum: "",              
                productid:"",
                productname: "",
                progress:"未排产",
                opsprogress:
                [{value:"未排产",label:"未排产"},
                 {value:"排产中",label:"排产中"},                
                 {value:"已产出,库存满足",label:"已产出,库存满足"},
                 {value:"已产出,库存不满足",label:"已产出,库存不满足"},

                 {value:"已发货",label:"已发货"},
                 {value:"订单关闭",label:"订单关闭"},
                 {value:"未知",label:"未知"},             
                 {value:"",label:"无"}
                 ],

                flag: "",
                opsflag:
                [{value:"W",label:"外销"},
                 {value:"G",label:"格力"},
                 {value:"",label:"不区分"}],
                

                
                dataset: [],
                dataset2: [],

                total: 0,
                page: 1,
                pagesize:15,
                listLoading: false,
                sels: [],//列表选中列

                //生产时间界面数据
                productionFormVisible: false,//界面是否显示
                editLoading: false,

                //新增界面数据


            }
        },
        methods: {
            selectCurrentRow(val) {
            this.currentRow = val;
            console.log(this.currentRow);
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
                    ddnum:this.detailnum, 
                    kh:this.flag,                     
                    productid:this.productid, 
                    prodname:this.productname, 
                    progress:this.progress,
                    pagenum:this.page, 
                    pagesize:this.pagesize,
                };
                this.listLoading = true;
                console.log(para);

                //NProgress.start();
                getOrderProgerssPage(para).then((res) => {
                    this.total = res.data.response.dataCount;//totalpage;//
                    this.dataset = res.data.response.data;//source;//
                    this.listLoading = false;
                    console.log(this.dataset);
                    //NProgress.done();
                });
            },
            
            GetProdata(){
                this.productionFormVisible = true;
                let para = {
                    ddnum:this.currentRow.订单编号, 
                };
                //this.listLoading = true;
                console.log(para);
                //NProgress.start();
                getPlanDate2(para).then((res) => {
                    this.dataset2 = res.data.response;
                    //this.listLoading = false;
                    console.log(this.dataset2);
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
            this.getOrders();

            let routers = window.localStorage.router
            ? JSON.parse(window.localStorage.router)
            : [];
            this.buttonList = getButtonList(this.$route.path, routers);
        }
    }

</script>

<style scoped>
.demonstration{
    color:rgb(88, 79, 79);
    font-size: 16px;
    
}
</style>
