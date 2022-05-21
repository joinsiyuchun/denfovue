<template>
    <div>
        <el-container style="height:800px; ">
            <el-aside style="width: 200px;">
                    <div class="sysname">
                       敦复渠道管理平台 
                    </div>
                    <el-menu router unique-opened>
                        <el-submenu :index="index+''" v-for="(item,index) in routes" v-if="!item.hidden" :key="index">
                            <template slot="title">
                                <i style="color: #fff;margin-right: 13px" :class="item.iconCls"></i>
                                <span>{{item.name}}</span>
                            </template>
                            <el-menu-item :index="child.path" v-for="(child,indexj) in item.children" :key="indexj">
                                {{child.name}}
                            </el-menu-item>

                        </el-submenu>
                    </el-menu>
            </el-aside>
          
            <el-container>
                <el-header class="homeHeader">
                    <div class="title"></div>
                    <div>
                        <el-dropdown class="userInfo" @command="commandHandler">
                            <span class="el-dropdown-link">
                            {{user.name}}<i class="fa fa-user-circle" style="color: rgb(255, 255, 255); margin-left: 13px;"></i>
                            </span>
                            <el-dropdown-menu slot="dropdown">
                                <el-dropdown-item command="userinfo">个人中心</el-dropdown-item>
                                <el-dropdown-item command="logout" divided>注销登录</el-dropdown-item>
                            </el-dropdown-menu>
                        </el-dropdown>
                    </div>
                </el-header>
                <el-main>
                    <el-breadcrumb separator-class="el-icon-arrow-right" v-if="this.$router.currentRoute.path!='/home'">
                        <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
                        <el-breadcrumb-item>{{this.$router.currentRoute.name}}</el-breadcrumb-item>
                    </el-breadcrumb>
                    <div class="homeWelcome" v-if="this.$router.currentRoute.path=='/home'">
                        欢迎来到敦复渠道管理系统！
                    </div>
                    <router-view class="homeRouterView"/>
                </el-main>
            </el-container>
        </el-container>
    </div>
</template>

<script>
    export default {
        name: "Home",
        data() {
            return {
                // user: JSON.parse(window.sessionStorage.getItem("user"))
            }
        },
        computed: {
            routes() {
                return this.$store.state.routes;
            },
            user() {
                return this.$store.state.currentHr;
            }
        },
        methods: {
            // goChat() {
            //     this.$router.push("/chat");
            // },
            commandHandler(cmd) {
                if (cmd == 'logout') {
                    this.$confirm('此操作将注销登录, 是否继续?', '提示', {
                        confirmButtonText: '确定',
                        cancelButtonText: '取消',
                        type: 'warning'
                    }).then(() => {
                        this.getRequest("/logout");
                        window.sessionStorage.removeItem("user")
                        this.$store.commit('initRoutes', []);
                        this.$router.replace("/");
                    }).catch(() => {
                        this.$message({
                            type: 'info',
                            message: '已取消操作'
                        });
                    });
                }else if (cmd == 'userinfo') {
                    this.$router.push('/hrinfo');
                }
            }
        }
    }
</script>

<style>
    .homeRouterView {
        margin-top: 10px;
    }

    .homeWelcome {
        text-align: center;
        font-size: 30px;
        font-family: 华文行楷;
        color: #409eff;
        padding-top: 50px;
    }

    .homeHeader {
        background-color: #409eff;
        display: flex;
        align-items: center;
        justify-content: space-between;
        padding: 0px 50px;
        box-sizing: border-box;
    }

    .homeHeader .title {
        font-family: PingFangSC-Regular;
        font-size: 24px;
        color: #ffffff
    }

    .homeHeader .userInfo {
        cursor: pointer;
    }

    .el-dropdown-link img {
        width: 48px;
        height: 48px;
        border-radius: 24px;
        margin-left: 8px;
    }

    .el-dropdown-link {
        display: flex;
        align-items: center;
        color: #ffffff;
    }

    .el-submenu .el-submenu__title {
        background-color: #001529;
        color: #ffffff;
    }

    .el-menu .el-menu-item:hover{
        outline: 0 !important;
        color: #ffffff !important;
        background: #001529!important;
    }
    .el-menu .el-menu-item.is-active {
        color: #fff !important;
        background: #1890FF!important;
        font-size: 14px;
    }
    .el-submenu .el-submenu__title:hover {
        color: #fff !important;
        background: #001529!important;
    }

    .el-menu .el-menu-item{
        background-color: #001529;
        color: #fff;
    }

    .el-aside{
        background-color: #001529;
        height:800px;
        color: #fff !important;
    }

    .sysname{
        min-height:52px;
        position:relative;
        display:flex;
        align-items:center;
        justify-content: center;
        font-family: PingFangSC-Regular;
        font-size: 20px;
        color: #ffffff
    }

    .el-submenu__title{
        font-size: 16px;
    }
    
    .el-menu{
        border-right: solid 0px #001529;
    }
</style>
