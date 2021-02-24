<template>
    <section>
        <!--工具条-->
     <toolbar :buttonList="buttonList" @callFunction="callFunction"></toolbar>
     

        <!--列表-->
        <el-table :data="users" highlight-current-row 
        @current-change="selectCurrentRow"
        v-loading="listLoading" @selection-change="selsChange"
                  style="width: 100%;">
            <el-table-column type="selection" width="50">
            </el-table-column>
            <el-table-column type="index" width="80">
            </el-table-column>
            <el-table-column prop="Id" label="id" width="" sortable>
            </el-table-column>
            <el-table-column prop="UserId" label="userid" width="" sortable>
            </el-table-column>
            <el-table-column prop="UserName" label="名称" width="" sortable>
            </el-table-column>
            <el-table-column prop="RoleId" label="角色" width="" sortable>
            </el-table-column>
            <el-table-column prop="IsDeleted" label="状态" width="" sortable>
                <template slot-scope="scope">
                    <el-tag
                            :type="scope.row.IsDeleted == 0  ? 'success' : 'danger'"
                            disable-transitions>{{scope.row.IsDeleted == 0 ? "正常":"禁用"}}
                    </el-tag>
                </template>
            </el-table-column>
            <!-- <el-table-column label="操作" width="150">
                <template scope="scope">
                    <el-button size="small" @click="handleEdit(scope.$index, scope.row)">编辑</el-button>
                    <el-button type="danger" size="small" @click="handleDel(scope.$index, scope.row)">删除</el-button>
                </template>
            </el-table-column> -->
        </el-table>

        <!--工具条-->
        <el-col :span="24" class="toolbar">
            <el-button type="danger" @click="batchRemove" :disabled="this.sels.length===0">批量删除</el-button>
            <el-pagination layout="prev, pager, next" @current-change="handleCurrentChange" :page-size="50"
                           :total="total" style="float:right;">
            </el-pagination>
        </el-col>

        <!--编辑界面-->
        <el-dialog title="编辑" :visible.sync="editFormVisible" v-model="editFormVisible" :close-on-click-modal="false">
            <el-form :model="editForm" label-width="80px" :rules="editFormRules" ref="editForm">
                <el-form-item label="账号" prop="UserId">
                    <el-input v-model="editForm.UserId" auto-complete="off" disabled></el-input>
                </el-form-item>
                <el-form-item label="姓名" prop="UserName">
                    <el-input v-model="editForm.UserName" auto-complete="off"></el-input>
                </el-form-item>
                <el-form-item label="角色" prop="RoleId">
                    <el-select  v-model="editForm.RoleId" placeholder="请选择角色">
                        <el-option  :key="0" :label="'未选择角色'" :value="0"></el-option>
                        <el-option v-for="item in roles" :key="item.Id" :label="item.Name" :value="item.Id"></el-option>
                    </el-select>
                </el-form-item>
            </el-form>
            <div slot="footer" class="dialog-footer">
                <el-button @click.native="editFormVisible = false">取消</el-button>
                <el-button type="primary" @click.native="editSubmit" :loading="editLoading">提交</el-button>
            </div>
        </el-dialog>

        <!--新增界面-->
        <el-dialog title="新增" :visible.sync="addFormVisible" v-model="addFormVisible" :close-on-click-modal="false">
            <el-form :model="addForm" label-width="80px" :rules="addFormRules" ref="addForm">
                <el-form-item label="账号" prop="uid">
                    <el-input v-model="addForm.uid" auto-complete="off"></el-input>
                </el-form-item>
                <el-form-item label="角色" prop="rid">
                    <el-select  v-model="addForm.rid" placeholder="请选择角色">
                        <el-option  :key="0" :label="'未选择角色'" :value="0"></el-option>
                        <el-option v-for="item in roles" :key="item.Id" :label="item.Description" :value="item.Id"></el-option>
                    </el-select>
                </el-form-item>
            </el-form>
            <div slot="footer" class="dialog-footer">
                <el-button @click.native="addFormVisible = false">取消</el-button>
                <el-button type="primary" @click.native="addSubmit" :loading="addLoading">提交</el-button>
            </div>
        </el-dialog>
    </section>
</template>

<script>
    import util from '../../../util/date'
    import {getUserListPage,getRoleListPage , removeUser, batchRemoveUser, editUser, addUser} from '../../api/api';
    import { getButtonList } from "../../promissionRouter";
    import Toolbar from "../../components/Toolbar";
import { User } from 'oidc-client';

    export default {
        components: { Toolbar },
        data() {
            return {
                filters: {
                    name: ''
                },
                user:'',
                users: [],
                roles: [],
                total: 0,
                buttonList: [],
                currentRow: null,
                page: 1,
                listLoading: false,
                sels: [],//列表选中列

                addDialogFormVisible: false,
                editFormVisible: false,//编辑界面是否显示
                editLoading: false,
                editFormRules: {
                    uLoginName: [
                        {required: true, message: '请输入登录名', trigger: 'blur'}
                    ],
                    uRealName: [
                        {required: true, message: '请输入昵称', trigger: 'blur'}
                    ],
                    birth: [
                        {required: true, message: '请填写生日', trigger: 'blur'}
                    ]
                },
                //编辑界面数据
                editForm: {
                    // id: 0,
                    // uID: 0,
                    // RIDs: 0,
                    // uLoginName: '',
                    // uRealName: '',
                    // name: '',
                    Id:0,
                    UserId:"",
                    UserName:"",
                    RoleId:0,
                    //sex: -1,
                    //age: 0,
                    //birth: '',
                    //addr: ''
                },

                addFormVisible: false,//新增界面是否显示
                addLoading: false,
                addFormRules: {
                    uLoginName: [
                        {required: true, message: '请输入登录名', trigger: 'blur'}
                    ],
                    uRealName: [
                        {required: true, message: '请输入昵称', trigger: 'blur'}
                    ],
                    uLoginPWD: [
                        {required: true, message: '请输入密码', trigger: 'blur'}
                    ],
                    birth: [
                        {required: true, message: '请填写生日', trigger: 'blur'}
                    ]
                },
                //新增界面数据
                addForm: {
                    uid:"",
                    rid:0,
                    // name: '',
                    // uID: 0,
                    // RIDs: 0,
                    // uLoginName: '',
                    // uRealName: '',
                    // uLoginPWD: '',
                    //sex: -1,
                    //age: 0,
                    //birth: '',
                    //addr: ''
                }

            }
        },
        methods: {
            selectCurrentRow(val) {
            this.currentRow = val;
            },
            callFunction(item) {
            this.filters = {
                name: item.search
            };
            this[item.Func].apply(this, item);
            },
            //性别显示转换
            // formatSex: function (row, column) {
            //     return row.sex == 1 ? '男' : row.sex == 0 ? '女' : '未知';
            // },
            // formatBirth: function (row, column) {
            //     return (!row.birth || row.birth == '') ? '' : util.formatDate.format(new Date(row.birth), 'yyyy-MM-dd');
            // },
            handleCurrentChange(val) {
                this.page = val;
                this.getUsers();
            },
            //获取用户列表
            getUsers() {
                let para = {
                    
                    uid: this.filters.name
                };
                this.listLoading = true;
                //NProgress.start();
                getUserListPage(para).then((res) => {

                    //this.total = res.data.response.dataCount;
                    this.users = res.data.response;//.data;
                    this.listLoading = false;
                    //NProgress.done();
                });
            },
            //删除
            handleDel() {
                let row = this.currentRow;
                if (!row) {
                    this.$message({
                    message: "请选择要删除的一行数据！",
                    type: "error"
                    });

                    return;
                }
                this.$confirm('确认删除该记录吗?', '提示', {
                    type: 'warning'
                }).then(() => {
                    this.listLoading = true;
                    //NProgress.start();
                    let para = {id: row.Id};
                    removeUser(para).then((res) => {

                        if (util.isEmt.format(res)) {
                            this.listLoading = false;
                            return;
                        }
                        this.listLoading = false;
                        //NProgress.done();
                        if (res.data.success) {
                            this.$message({
                                message: '删除成功',
                                type: 'success'
                            });

                        } else {
                            this.$message({
                                message: res.data.msg,
                                type: 'error'
                            });
                        }

                        this.getUsers();
                    });
                }).catch(() => {

                });
            },
            getRoles(){
                getRoleListPage().then((res) => {
                    this.roles = res.data.response.data;
                });
            },

            //显示编辑界面 
            handleEdit() {
                let row = this.currentRow;
                if (!row) {
                    this.$message({
                    message: "请选择要编辑的一行数据！",
                    type: "error"
                    });
                    return;
                }
                this.editFormVisible = true;
                this.editForm = Object.assign({}, row);

            },

            //显示新增界面
            handleAdd() {
                this.addFormVisible = true;
                this.addForm = {
                    uid:"",
                    rid:0,
                };
            },            

            //编辑
            editSubmit: function () {
                this.$refs.editForm.validate((valid) => {
                    if (valid) {
                        this.$confirm('确认提交吗？', '提示', {}).then(() => {
                            this.editLoading = true;
                            let para = Object.assign({}, this.editForm);
                            para.ModifyId=this.user;

                            editUser(para).then((res) => {

                                if (util.isEmt.format(res)) {
                                    this.editLoading = false;
                                    return;
                                }
                                if (res.data.success) {
                                    this.editLoading = false;
                                    //NProgress.done();
                                    this.$message({
                                        message: res.data.msg,
                                        type: 'success'
                                    });
                                    this.$refs['editForm'].resetFields();
                                    this.editFormVisible = false;
                                    this.getUsers();
                                } else {
                                    this.$message({
                                        message: res.data.msg,
                                        type: 'error'
                                    });

                                }
                            });
                        });
                    }
                });
            },
            //新增
            addSubmit: function () {
                this.$refs.addForm.validate((valid) => {
                    if (valid) {
                        this.$confirm('确认提交吗？', '提示', {}).then(() => {
                            this.addLoading = true;
                            let para = {
                             uid: this.addForm.uid,
                             rid: this.addForm.rid,

                            };
                            addUser(para).then((res) => {
                                if (util.isEmt.format(res)) {
                                    this.addLoading = false;
                                    return;
                                }
                                if (res.data.success) {
                                    this.addLoading = false;
                                    //NProgress.done();
                                    this.$message({
                                        message: res.data.msg,
                                        type: 'success'
                                    });
                                    this.$refs['addForm'].resetFields();
                                    this.addFormVisible = false;
                                    this.getUsers();
                                }
                                else {
                                    this.$message({
                                        message: res.data.msg,
                                        type: 'error'
                                    });

                                }

                            });

                        });
                    }
                });
            },
            selsChange: function (sels) {
                this.sels = sels;
            },
            //批量删除
            batchRemove: function () {

                // return;

                var ids = this.sels.map(item => item.uID).toString();
                this.$confirm('确认删除选中记录吗？', '提示', {
                    type: 'warning'
                }).then(() => {
                    this.listLoading = true;
                    //NProgress.start();
                    let para = {ids: ids};

                    batchRemoveUser(para).then((res) => {
                        this.listLoading = false;
                        //NProgress.done();
                        this.$message({
                            message: '该功能未开放',
                            type: 'warning'
                        });
                        console.log(res)
                    });
                }).catch(() => {

                });
            }
        },
        mounted() {
            this.getUsers();
            this.getRoles();
            this.user= JSON.parse(window.localStorage.user).uID;

            let routers = window.localStorage.router
            ? JSON.parse(window.localStorage.router)
            : [];
            this.buttonList = getButtonList(this.$route.path, routers);
        }
    }

</script>

<style scoped>

</style>
