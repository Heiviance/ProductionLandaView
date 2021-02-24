<template>
    <div class="wrapper">
        <ul class="bg-bubbles">
            <li v-for="n in 10" :key="n+'n'"></li>
            <ol v-for="m in 5"  :key="m+'m'"></ol>
        </ul>
        <div class="bg bg-blur" style="display: none;"></div>
        <div style="height: 10%;"></div>
       
        <el-form :model="ruleForm2" :rules="rules2" ref="ruleForm2" label-position="left" label-width="0px"
                 class="demo-ruleForm login-container">
                 <el-row :gutter="20" >
                     <el-col :span="12">
                         <div class="diyborder">
                         <h2 class="title">订单管理系统</h2>
                         <div class="comment2">版本:v{{version}}</div>
                         <div class="comment">
                             <div>系统管理:{{admin}} ({{keshi1}})</div>
                             <br>
                             <div>技术支持:{{kaifa}} ({{keshi2}})</div>
                         
                         </div>
                         </div>
                         </el-col>
                
                     <el-col :span="12">

            
            <el-form-item prop="account">
                <el-input type="text" v-model="ruleForm2.account" auto-complete="off" placeholder="账号"></el-input>
            </el-form-item>
            <el-form-item prop="checkPass">
                <el-input v-model="ruleForm2.checkPass" auto-complete="off" show-password placeholder="密码"></el-input>
            </el-form-item>
            <el-checkbox v-model="checked" checked class="remember">记住密码</el-checkbox>
            <el-form-item style="width:100%;">
                <el-button type="primary" style="width:100%;" @click.native.prevent="getUsers" :loading="logining">
                    {{loginStr}}
                </el-button>
            </el-form-item>      
            
                 </el-col>
                 </el-row>
        </el-form>


    </div>
</template>

<script>
    import {getUserListPage,requestLogin, requestLoginMock, getUserByToken, getNavigationBar} from '../api/api';
    import router from '@/router'
    import util from "../../util/date";
    import {resetRouter, filterAsyncRouter} from '@/router/index'

    export default {
        data() {
            return {
                version:"1.0",
                admin:"解成亮",
                keshi1:"生产计划部",
                kaifa:"吴海媚",
                keshi2:"信息化推进办公室",
                instance: "",
                loginStr: '登录',
                logining: false,
                ruleForm2: {
                    account: '',
                    checkPass: ''
                },
                rules2: {
                    account: [
                        {required: true, message: '请输入账号', trigger: 'blur'},
                        //{ validator: validaePass }
                    ],
                    checkPass: [
                        {required: true, message: '请输入密码', trigger: 'blur'},
                        //{ validator: validaePass2 }
                    ]
                },
                checked: true
            };
        },
        methods: {
            //获取用户列表
            getUsers() {
                let para = {          
                    uid: this.ruleForm2.account
                };
                getUserListPage(para).then((res) => {
                        console.log(res.data.response);                    
                    if(res.data.response.length>0){

                        this.handleSubmit2();
                    }
                    else{
                        this.$message({
                        message: "该用户未注册",
                        type: "error",
                        });
                    }
                });
            },
            handleReset2() {
                this.$refs.ruleForm2.resetFields();
            },
            // 获取 Token
            handleSubmit2() {//ev
                var _this = this;
                this.$refs.ruleForm2.validate((valid) => {
                    if (valid) {
                        //_this.$router.replace('/table');
                        this.logining = true;

                        //NProgress.start();
                        var loginParams = {uid: this.ruleForm2.account,
                         pass: this.ruleForm2.checkPass};

                        _this.loginStr = "登录中...";

                        requestLogin(loginParams).then(data => {
                            if (!data.success) {
                                _this.$message({
                                    message: data.msg,
                                    type: 'error'
                                });
                                _this.logining = false;
                                _this.loginStr = "重新登录";
                                // _this.closeAlert()
                            } else {

                                var token = data.response.token;
                                _this.$store.commit("saveToken", token);

                                var curTime = new Date();
                                var expiredate = new Date(curTime.setSeconds(curTime.getSeconds() + data.response.expires_in));
                                _this.$store.commit("saveTokenExpire", expiredate);

                                window.localStorage.refreshtime = expiredate;
                                window.localStorage.expires_in = data.response.expires_in;

                                _this.$notify({
                                    type: "success",
                                    message: `成功获取数据，界面初始化中...`,
                                    duration: 3000
                                });

                                _this.loginStr = "等待服务器返回用户信息...";
                                _this.getUserInfoByToken(token)


                            }
                        });
                    } else {
                        console.log('error submit!!');
                        return false;
                    }
                });
            },
            // 获取用户数据
            getUserInfoByToken(token) {
                var _this = this;
                var loginParams = {token: token};
                getUserByToken(loginParams).then(data => {

                    if (!data.success) {
                        _this.$message({
                            message: data.msg,
                            type: 'error'
                        });
                    } 
                    else {
                        _this.loginStr = "接收到用户数据，初始化中";
                        console.log(data.response);
                        window.localStorage.user = JSON.stringify(data.response)
                        if (!util.isEmt.format(data.response.uID)) {
                            _this.GetNavigationBar(data.response.uID)
                        }
                    }
                });
            },
            // 获取路由树
            GetNavigationBar(uid) {
                var _this = this;
                var loginParams = {uid: uid, t: new Date()};

                getNavigationBar(loginParams).then(data => {
                    _this.logining = false;


                    if (!data.success) {
                        _this.$message({
                            message: data.msg,
                            type: 'error'
                        });
                    } else {

                        // _this.closeAlert()

                        _this.$message({
                            message: "后台初始化成功",
                            type: 'success'
                        });


                        let userinfo = JSON.parse(window.localStorage.user ? window.localStorage.user : null);
                        _this.$notify({
                            type: "success",
                            message: `登录成功 \n 欢迎管理员：${userinfo.uRealName} ！登录将在 ${window.localStorage.expires_in / 60} 分钟后过期！`,
                            duration: 6000
                        });


                        window.localStorage.router = (JSON.stringify(data.response.children));

                        let getRouter = data.response.children//后台拿到路由
                        getRouter = filterAsyncRouter(getRouter) //过滤路由
                        router.$addRoutes(getRouter) //动态添加路由

                        _this.$router.replace(_this.$route.query.redirect ? _this.$route.query.redirect : "/");
                    }
                });
            }
        },
        mounted() {
            // window.localStorage.clear()
            console.info('%c 本地缓存已清空!', "color:green")

        },
    }

</script>

<style>
    .bg {
        margin: 0px;
        position: absolute;
        left: 0;
        top: 0;
        right: 0;
        bottom: 0;
        background: url(../assets/loginbck.png) no-repeat top left;
        background-repeat: no-repeat;
        background-size: cover;
        width: 100%;
        height: 100%;
    }

    .login-container {
        -webkit-border-radius: 5px;
        border-radius: 5px;
        -moz-border-radius: 5px;
        background-clip: padding-box;
        margin: auto;
        width: 550px;
        padding: 35px 35px 15px 35px;
        background: #fff;
        border: 1px solid #eaeaea;
        /* box-shadow: 0 0 25px #cac6c6; */
        z-index: 9999;
        position: relative;
    }

    .login-container .title {
        margin: 0px auto 40px auto;
        text-align: left;
        color: #505458;
    }

    .login-container .remember {
        margin: 0px 0px 25px 0px;
    }
    
    .wrapper {
        background: #3f7695;
        background: -webkit-linear-gradient(top left, #3f7695 0%, #2179b4 100%);
        background: linear-gradient(to bottom right, #21658d 0, #3f7695);
        opacity: 0.8;
        position: absolute;
        left: 0;
        width: 100%;
        height: 100%;
        overflow: hidden;
    }

    .wrapper.form-success .containerLogin h1 {
        -webkit-transform: translateY(85px);
        -ms-transform: translateY(85px);
        transform: translateY(85px);
    }

    .containerLogin {
        max-width: 600px;
        margin: 0 auto;
        padding: 80px 0;
        height: 400px;
        text-align: center;
    }

    .containerLogin h1 {
        font-size: 40px;
        -webkit-transition-duration: 1s;
        transition-duration: 1s;
        -webkit-transition-timing-function: ease-in-put;
        transition-timing-function: ease-in-put;
        font-weight: 200;
    }

    .bg-bubbles {
        position: absolute;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
        z-index: 1;
    }

    .bg-bubbles li, .bg-bubbles ol {
        position: absolute;
        list-style: none;
        display: block;
        width: 40px;
        height: 40px;
        background-color: rgba(255, 255, 255, 0.15);
        bottom: -160px;
        -webkit-animation: square 25s infinite;
        animation: square 25s infinite;
        -webkit-transition-timing-function: linear;
        transition-timing-function: linear;
    }

    ol {
        padding: 0 !important;
    }

    .bg-bubbles ol:nth-child(11) {
        left: 10%;
        top: 10%;
        width: 20px;
        height: 20px;
    }

    .bg-bubbles ol:nth-child(12) {
        left: 20%;
        top: 40%;

        width: 60px;
        height: 60px;
    }

    .bg-bubbles ol:nth-child(13) {
        left: 65%;
        top: 30%;

        width: 100px;
        height: 60px;
    }

    .bg-bubbles ol:nth-child(14) {
        left: 70%;
        top: 30%;
        width: 100px;
        height: 150px;
    }

    .bg-bubbles ol:nth-child(15) {
        left: 50%;
        top: 70%;

        width: 40px;
        height: 60px;
    }

    .bg-bubbles li:nth-child(1) {
        left: 10%;
    }

    .bg-bubbles li:nth-child(2) {
        left: 20%;
        width: 80px;
        height: 80px;
        -webkit-animation-delay: 2s;
        animation-delay: 2s;
        -webkit-animation-duration: 17s;
        animation-duration: 17s;
    }

    .bg-bubbles li:nth-child(3) {
        left: 25%;
        -webkit-animation-delay: 4s;
        animation-delay: 4s;
    }

    .bg-bubbles li:nth-child(4) {
        left: 40%;
        width: 60px;
        height: 60px;
        -webkit-animation-duration: 22s;
        animation-duration: 22s;
        background-color: rgba(255, 255, 255, 0.322);
    }

    .bg-bubbles li:nth-child(5) {
        left: 70%;
    }

    .bg-bubbles li:nth-child(6) {
        left: 80%;
        width: 120px;
        height: 120px;
        -webkit-animation-delay: 3s;
        animation-delay: 3s;
        background-color: rgba(255, 255, 255, 0.2);
    }

    .bg-bubbles li:nth-child(7) {
        left: 32%;
        width: 160px;
        height: 160px;
        -webkit-animation-delay: 7s;
        animation-delay: 7s;
    }

    .bg-bubbles li:nth-child(8) {
        left: 55%;
        width: 20px;
        height: 20px;
        -webkit-animation-delay: 15s;
        animation-delay: 15s;
        -webkit-animation-duration: 40s;
        animation-duration: 40s;
    }

    .bg-bubbles li:nth-child(9) {
        left: 25%;
        width: 10px;
        height: 10px;
        -webkit-animation-delay: 2s;
        animation-delay: 2s;
        -webkit-animation-duration: 40s;
        animation-duration: 40s;
        background-color: rgba(255, 255, 255, 0.3);
    }

    .bg-bubbles li:nth-child(10) {
        left: 90%;
        width: 160px;
        height: 160px;
        -webkit-animation-delay: 11s;
        animation-delay: 11s;
    }

    @-webkit-keyframes square {
        0% {
            -webkit-transform: translateY(0);
            transform: translateY(0);
        }

        100% {
            -webkit-transform: translateY(-700px) rotate(600deg);
            transform: translateY(-700px) rotate(600deg);
        }
    }

    @keyframes square {
        0% {
            -webkit-transform: translateY(0);
            transform: translateY(0);
        }

        100% {
            -webkit-transform: translateY(-700px) rotate(600deg);
            transform: translateY(-700px) rotate(600deg);
        }
    }

    .content-az {
        padding: 0 !important;
        border: none !important;
    }
    .comment{
        color: gray;
        font-size: 13px;
        text-align: left;
    }
    .comment2{
        color: rgb(167, 39, 39);
        font-size: 12px;
        text-align: right;
    }
    .diyborder{
        border-right: gray;
    }
</style>
