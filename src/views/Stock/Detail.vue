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
            <el-col :span="14">           
            <el-input v-model="detailnum" placeholder="订单编号" style="width:150px"></el-input>
            <el-input v-model="productid" placeholder="凌达编码" style="width:150px"></el-input>               
            <el-input v-model="productname" placeholder="产品型号" style="width:200px"></el-input>
            <el-input v-model="lrr" placeholder="业务员" style="width:100px"></el-input>
             </el-col>

            <el-col :span="6">
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

            <el-table-column prop="添加人" label="添加人" width="100" 
            show-overflow-tooltip>
            </el-table-column>

            <el-table-column prop="订单编号" label="订单编号" width="150"  show-overflow-tooltip>
            </el-table-column>

            <el-table-column prop="客户编码" label="客户编号"   width="120"  show-overflow-tooltip>
            </el-table-column>  

            <el-table-column prop="客户" label="客户"   width="160"  show-overflow-tooltip>
            </el-table-column> 

            <el-table-column prop="凌达编码" label="凌达编码" width="120"  show-overflow-tooltip>
            </el-table-column>

            <el-table-column prop="压缩机型号" label="型号" width="130"  show-overflow-tooltip>
            </el-table-column>

            <el-table-column prop="库存数量" label="库存数量" width="50">
            </el-table-column>

            <el-table-column prop="合格数量" label="合格数量" width="50">
            </el-table-column> 
            
            <el-table-column prop="上月库存数" label="上月库存" width="50">
            </el-table-column>

            <el-table-column prop="增加或减少量" label="变动" width="50">
            </el-table-column>


            <el-table-column prop="最后一次入库日期" label="入库日期" width="130" show-overflow-tooltip>
            </el-table-column> 

            <el-table-column prop="库存类型" label="库存类型" width="50">
            </el-table-column>  
        </el-table>

        <!--工具条-->
        <div>
            <el-pagination layout="prev, pager, next" @current-change="handleCurrentChange" :page-size="pagesize"
                           :total="total" style="float:right;">
            </el-pagination>
        </div>


        <!--查看生产时间界面-->
        <el-dialog title="汇总" width="90%"
        v-model="productionFormVisible"
        :visible.sync="productionFormVisible"  :close-on-click-modal="false">
            <el-table :data="dataset2" 
            highlight-current-row size="mini" style="width: 100%;" >
            <el-table-column type="index" width="35">
            </el-table-column>  

            <el-table-column prop="外销系统编号" label="外销编号"   width="150" show-overflow-tooltip></el-table-column>

            <el-table-column prop="订单编号" label="订单编号" width="150"  show-overflow-tooltip>
            </el-table-column>
            <el-table-column prop="客户编码" label="客户编号"   width="120"  show-overflow-tooltip>
            </el-table-column>  
            <el-table-column prop="客户" label="客户"   width="160"  show-overflow-tooltip>
            </el-table-column> 

            <el-table-column prop="系列" label="系列" width="80">
            </el-table-column>
            <el-table-column prop="凌达编码" label="凌达编码" width="120">
            </el-table-column>
            <el-table-column prop="压缩机型号" label="型号" width="130"  show-overflow-tooltip>
            </el-table-column>
            <el-table-column prop="库存数量" label="库存数量" width="50">
            </el-table-column>  
            <el-table-column prop="质量状态" label="质量状态" width="200"
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
    import { getDetail  } from '../../api/api';
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
                lrr:"",

                flag: "",
                opsflag:
                [{value:"外销",label:"外销"},
                 {value:"格力",label:"格力"},
                 {value:"",label:"不区分"}],
                

                
                dataset: [],
                dataset2: [],

                total: 0,
                total2: 0,
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
                this.getD();
            },            
            // formatCreateTime: function (row, column) {
            //     return (!row.需求审核时间e || row.需求审核时间 == '') ? '' : util.formatDate.format(new Date(row.需求审核时间), 'yyyy-MM-dd');
            // },

            //获取详情 
            getD() {
                let para = {
                    ddnum:this.detailnum, 
                    khtype:this.flag,  
                    productid:this.productid, 
                    productname:this.productname, 
                    ywy:this.lrr,
                    page:this.page, 
                    size:this.pagesize,
                };
                this.listLoading = true;
                console.log(para);

                //NProgress.start();
                getDetail(para).then((res) => {
                    this.total = res.data.response.dataCount;//totalpage;//
                    this.dataset = res.data.response.data;//source;//
                    this.listLoading = false;
                    console.log(this.dataset);
                    //NProgress.done();
                });
            },
            
            //获取汇总
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
            this.lrr=JSON.parse(window.localStorage.user).uRealName;//uRemark;
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
