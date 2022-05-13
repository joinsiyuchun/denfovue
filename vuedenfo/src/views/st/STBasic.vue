<template>
    <div>
        <div>
            <div style="display: flex;justify-content: space-between">
                <div>
                    <el-input placeholder="请输入人员姓名进行搜索，可以直接回车搜索..." prefix-icon="el-icon-search"
                              clearable
                              @clear="initSTs"
                              style="width: 350px;margin-right: 10px" v-model="keyword"
                              @keydown.enter.native="initSTs" :disabled="showAdvanceSearchView"></el-input>
                    <el-button icon="el-icon-search" type="primary" @click="initSTs" :disabled="showAdvanceSearchView">
                        搜索
                    </el-button>
                    <el-button type="primary" @click="showAdvanceSearchView = !showAdvanceSearchView">
                        <i :class="showAdvanceSearchView?'fa fa-angle-double-up':'fa fa-angle-double-down'"
                           aria-hidden="true"></i>
                        高级搜索
                    </el-button>
                </div>
                <div>
                    <el-upload
                            :show-file-list="false"
                            :before-upload="beforeUpload"
                            :on-success="onSuccess"
                            :on-error="onError"
                            :disabled="importDataDisabled"
                            style="display: inline-flex;margin-right: 8px"
                            action="/st/basic/import">
                        <el-button :disabled="importDataDisabled" type="success" :icon="importDataBtnIcon">
                            {{importDataBtnText}}
                        </el-button>
                    </el-upload>
                    <el-button type="success" @click="exportData" icon="el-icon-download">
                        导出数据
                    </el-button>
                    <el-button type="primary" icon="el-icon-plus" @click="showAddSTView">
                        添加用户
                    </el-button>
                </div>
            </div>
            <transition name="slide-fade">
                <div v-show="showAdvanceSearchView"
                     style="border: 1px solid #409eff;border-radius: 5px;box-sizing: border-box;padding: 5px;margin: 10px 0px;font-size: 14px;">
                    <el-row>
                      <el-col :span="6">
                        订单编号:<el-input size="mini" style="width: 150px" prefix-icon="el-icon-edit" v-model="searchValue.orderNo"
                                       placeholder="请输入预订单号"></el-input>
                      </el-col>
                        <el-col :span="6">
                            人员编号:<el-input size="mini" style="width: 150px" prefix-icon="el-icon-edit" v-model="searchValue.patIndexNo"
                                           placeholder="请输入人员编号"></el-input>
                        </el-col>
                        <el-col :span="6">
                            姓名:<el-input size="mini" style="width: 150px" prefix-icon="el-icon-edit" v-model="searchValue.name"
                                         placeholder="请输入人员姓名"></el-input>
                        </el-col>
                        <el-col :span="6">
                            手机号:<el-input size="mini" style="width: 150px" prefix-icon="el-icon-edit" v-model="searchValue.telephone"
                                          placeholder="请输入员工手机号"></el-input>
                        </el-col>

                    </el-row>
                    <el-row style="margin-top: 10px">

                      <el-col :span="12">
                        创建日期:
                        <el-date-picker
                          v-model="searchValue.createDTScope"
                          type="daterange"
                          size="mini"
                          unlink-panels
                          value-format="yyyy-MM-dd"
                          range-separator="至"
                          start-placeholder="开始日期"
                          end-placeholder="结束日期">
                        </el-date-picker>
                      </el-col>
                      <el-col :span="12">
                        状态:
                        <el-select v-model="searchValue.status" placeholder="请输入状态" size="mini"
                                   style="width: 120px;" >
                          <el-option
                            v-for="item in statusoptions"
                            :key="item.value"
                            :label="item.label"
                            :value="item.value">
                          </el-option>
                        </el-select>
<!--                        最后更新日期:-->
<!--                        <el-date-picker-->
<!--                          v-model="searchValue.updateDTScope"-->
<!--                          type="daterange"-->
<!--                          size="mini"-->
<!--                          unlink-panels-->
<!--                          value-format="yyyy-MM-dd"-->
<!--                          range-separator="至"-->
<!--                          start-placeholder="开始日期"-->
<!--                          end-placeholder="结束日期">-->
<!--                        </el-date-picker>-->
                      </el-col>
                    </el-row>

                    <el-row style="margin-top: 10px">
                      <el-col :span="24" >
                        <el-button size="mini" @click="emptySearch">重置</el-button>
                        <el-button size="mini" icon="el-icon-search" type="primary" @click="initSTs('advanced')">搜索</el-button>
                      </el-col>
                    </el-row>
                </div>
            </transition>
        </div>
        <div style="margin-top: 10px">
            <el-table
                    :data="sts"
                    stripe
                    border
                    v-loading="loading"
                    element-loading-text="正在加载..."
                    element-loading-spinner="el-icon-loading"
                    element-loading-background="rgba(0, 0, 0, 0.8)"
                    style="width: 100%">
                <el-table-column
                        type="selection"
                        width="55">
                </el-table-column>
                <el-table-column
                  prop="id"
                  fixed
                  align="left"
                  label="记录编号"
                  width="90">
                </el-table-column>
                <el-table-column
                        prop="orderNo"
                        align="left"
                        label="预约单号"
                        width="90">
                </el-table-column>
                <el-table-column
                        prop="patIndexNo"
                        label="人员编号"
                        align="left"
                        width="85">
                </el-table-column>
                <el-table-column
                        prop="name"
                        label="姓名"
                        align="left"
                        width="85">
                </el-table-column>
                <el-table-column
                        prop="telephone"
                        width="100"
                        align="left"
                        label="电话号码">
                </el-table-column>
                <el-table-column
                  prop="status"
                  label="状态"
                  align="left"
                  width="85">
                </el-table-column>
                <el-table-column
                        prop="reportLink"
                        width="600"
                        align="left"
                        label="体检报告下载地址">
                </el-table-column>
                <el-table-column
                        prop="createDT"
                        width="95"
                        align="left"
                        label="创建日期">
                </el-table-column>
                <el-table-column
                        prop="updateDT"
                        width="95"
                        align="left"
                        label="最后更新日期">
                </el-table-column>
                <el-table-column
                        fixed="right"
                        width="200"
                        label="操作">
                    <template slot-scope="scope">
                        <el-button @click="showEditSTView(scope.row)" style="padding: 3px" size="mini">编辑</el-button>
                        <el-button @click="interfaceST(scope.row)" style="padding: 3px" size="mini" type="primary">发送药联接口</el-button>
                        <el-button @click="deleteST(scope.row)" style="padding: 3px" size="mini" type="danger">删除</el-button>
                    </template>
                </el-table-column>
            </el-table>
            <div style="display: flex;justify-content: flex-end">
                <el-pagination
                        background
                        @current-change="currentChange"
                        @size-change="sizeChange"
                        layout="sizes, prev, pager, next, jumper, ->, total, slot"
                        :total="total">
                </el-pagination>
            </div>
        </div>
        <el-dialog
                :title="title"
                :visible.sync="dialogVisible"
                width="80%">
            <div>
                <el-form :model="st" :rules="rules" ref="stForm">

                    <el-row>
                      <el-col :span="6">
                        <el-form-item label="记录编号:" prop="orderNo">
                          <el-input size="mini" style="width: 120px" prefix-icon="el-icon-edit"
                                    v-model="st.orderNo" placeholder="请输入记录编号"></el-input>
                        </el-form-item>
                      </el-col>
                        <el-col :span="6">
                            <el-form-item label="人员编号:" prop="patIndexNo">
                                <el-input size="mini" style="width: 120px" prefix-icon="el-icon-edit"
                                          v-model="st.patIndexNo" placeholder="请输入人员编号"></el-input>
                            </el-form-item>
                        </el-col>
                        <el-col :span="6">
                            <el-form-item label="姓名:" prop="name">
                                <el-input size="mini" style="width: 150px" prefix-icon="el-icon-message"
                                          v-model="st.name" placeholder="请输入人员姓名"></el-input>
                            </el-form-item>
                        </el-col>
                        <el-col :span="6">
                            <el-form-item label="电话号码:" prop="telephone">
                                <el-input size="mini" style="width: 200px" prefix-icon="el-icon-edit"
                                          v-model="st.telephone" placeholder="请输入电话号码"></el-input>
                            </el-form-item>
                        </el-col>
                    </el-row>
                  <el-row>
                    <el-col :span="6">
                      <el-form-item label="状态:" prop="status">
                          <el-select v-model="st.status" placeholder="请输入状态" size="mini"
                                     style="width: 120px;">
                            <el-option
                              v-for="item in statusoptions"
                              :key="item.value"
                              :label="item.label"
                              :value="item.value">
                            </el-option>
                          </el-select>
                      </el-form-item>
                    </el-col>
                    <el-col :span="18">
                      <el-form-item label="体检报告下载地址:" prop="reportLink">
                        <el-input size="mini" style="width: 100%" prefix-icon="el-icon-edit"
                                  v-model="st.reportLink" placeholder="请输入体检报告下载地址"></el-input>
                      </el-form-item>
                    </el-col>
                  </el-row>
                </el-form>
            </div>
            <span slot="footer" class="dialog-footer">
    <el-button @click="dialogVisible = false">取 消</el-button>
    <el-button type="primary" @click="doAddST">确 定</el-button>
  </span>
        </el-dialog>
    </div>
</template>

<script>
    export default {
        name: "STBasic",
        data() {
            return {
                searchValue: {
                    orderNo: null,
                    patIndexNo: null,
                    name: null,
                    telephone: null,
                    reportLink: null,
                    status: null,
                    createDT: null,
                    updateDT: null
                },
                title: '',
                importDataBtnText: '导入数据',
                importDataBtnIcon: 'el-icon-upload2',
                importDataDisabled: false,
                showAdvanceSearchView: false,
                sts: [],
                loading: false,
                popVisible: false,
                popVisible2: false,
                dialogVisible: false,
                total: 0,
                page: 1,
                keyword: '',
                size: 10,
                statusoptions: [{
                  value: 'reject',
                  label: '拒绝服务或者爽约'
                }, {
                  value: 'reschedule',
                  label: '改约'
                }, {
                  value: 'unreachable',
                  label: '电话不通'
                }, {
                  value: 'incorrectinfo',
                  label: '信息不一致'
                }, {
                  value: 'completed',
                  label: '服务完成'
                }],
                st: {
                    orderNo: "",
                    patIndexNo: "",
                    name: "",
                    telephone: "",
                    reportLink: "",
                    status: "",
                    createDT: "",
                    updateDT: ""
                },
                defaultProps: {
                    children: 'children',
                    label: 'name'
                },
                rules: {
                    orderNo: [{required: true, message: '请输入预定单号', trigger: 'blur'}],
                    patIndexNo: [{required: true, message: '请输入人员ID', trigger: 'blur'}],
                    name: [{required: true, message: '请输入人员名称', trigger: 'blur'}],
                    telephone: [{required: true, message: '请输入手机号', trigger: 'blur'}],
                    reportLink: [{required: false, message: '请输入体检报告地址', trigger: 'blur'}],
                    status: [{required: true, message: '请选择状态', trigger: 'blur'}]
                }
            }
        },
        mounted() {
            this.initSTs();
        },
        methods: {
            onError(err, file, fileList) {
                this.importDataBtnText = '导入数据';
                this.importDataBtnIcon = 'el-icon-upload2';
                this.importDataDisabled = false;
            },
            onSuccess(response, file, fileList) {
                this.importDataBtnText = '导入数据';
                this.importDataBtnIcon = 'el-icon-upload2';
                this.importDataDisabled = false;
                this.initSTs();
            },
            beforeUpload() {
                this.importDataBtnText = '正在导入';
                this.importDataBtnIcon = 'el-icon-loading';
                this.importDataDisabled = true;
            },
            exportData() {
                window.open('/st/basic/export', '_parent');
            },
            emptyST() {
                this.st = {
                    patindexNo: "",
                    name: "",
                    telephone: "",
                    reportLink: "",
                    status: "",
                    createDT: null,
                    updateDT: null
                }
            },
          emptySearch() {
            this.searchValue = {
              patindexNo: "",
              name: "",
              telephone: "",
              reportLink: "",
              status: "",
              createDT: null,
              updateDT: null
            }
          },
            showEditSTView(data) {
                // this.initPositions();
                this.title = '编辑状态跟踪信息';
                this.st = data;
                this.dialogVisible = true;
            },
            deleteST(data) {
                this.$confirm('此操作将永久删除【' + data.name + '】, 是否继续?', '提示', {
                    confirmButtonText: '确定',
                    cancelButtonText: '取消',
                    type: 'warning'
                }).then(() => {
                    this.deleteRequest("/st/basic/" + data.id).then(resp => {
                        if (resp) {
                            this.initSTs();
                        }
                    })
                }).catch(() => {
                    this.$message({
                        type: 'info',
                        message: '已取消删除'
                    });
                });
            },
            interfaceST(data) {
              this.$confirm('此操作将【' + data.name + '】的数据同步给药联, 是否继续?', '提示', {
                confirmButtonText: '确定',
                cancelButtonText: '取消',
                type: 'warning'
              }).then(() => {
                this.getRequest("/st/basic/" + data.id).then(resp => {
                  if (resp) {
                    this.$message({
                      type: 'info',
                      message: '同步成功'
                    });
                  }
                })
              }).catch(() => {
                this.$message({
                  type: 'info',
                  message: '已取消同步'
                });
              });
            },
            sizeChange(currentSize) {
                this.size = currentSize;
                this.initSTs();
            },
           doAddST() {
            if (this.st.id) {
              this.$refs['stForm'].validate(valid => {
                if (valid) {
                  this.putRequest("/st/basic/", this.st).then(resp => {
                    if (resp) {
                      this.dialogVisible = false;
                      this.initSTs();
                    }
                  })
                }
              });
            } else {
              this.$refs['stForm'].validate(valid => {
                if (valid) {
                  this.postRequest("/st/basic/", this.st).then(resp => {
                    if (resp) {
                      this.dialogVisible = false;
                      this.initSTs();
                    }
                  })
                }
              });
            }
          },
            currentChange(currentPage) {
                this.page = currentPage;
                this.initSTs('advanced');
            },
            showAddSTView() {
                this.emptyST();
                this.title = '添加记录';
                this.dialogVisible = true;
            },
            initSTs(type) {
                this.loading = true;
                let url = '/st/basic/?page=' + this.page + '&size=' + this.size;

                if (type && type == 'advanced') {
                  console.log(this.searchValue.orderNo);
                  if (this.searchValue.orderNo) {
                    url += '&orderNo=' + this.searchValue.orderNo;
                  }

                  if (this.searchValue.patIndexNo) {
                    url += '&patIndexNo=' + this.searchValue.patIndexNo;
                  }

                  if (this.searchValue.name) {
                    url += '&name=' + this.searchValue.name;
                  }

                  if (this.searchValue.telephone) {
                    url += '&telephone=' + this.searchValue.telephone;
                  }

                  if (this.searchValue.status) {
                    url += '&status=' + this.searchValue.status;
                  }

                  if (this.searchValue.createDTScope) {
                    url += '&createDTScope=' + this.searchValue.createDTScope;
                  }

                  if (this.searchValue.updateDTScope) {
                    url += '&updateDTScope=' + this.searchValue.updateDTScope;
                  }

                }else {
                    url += "&name=" + this.keyword;
                }
                this.getRequest(url).then(resp => {
                    this.loading = false;
                    if (resp) {
                        this.sts = resp.data;
                        this.total = resp.total;
                    }
                });
            }
        }
    }
</script>

<style>
    /* 可以设置不同的进入和离开动画 */
    /* 设置持续时间和动画函数 */
    .slide-fade-enter-active {
        transition: all .8s ease;
    }

    .slide-fade-leave-active {
        transition: all .8s cubic-bezier(1.0, 0.5, 0.8, 1.0);
    }

    .slide-fade-enter, .slide-fade-leave-to
        /* .slide-fade-leave-active for below version 2.1.8 */
    {
        transform: translateX(10px);
        opacity: 0;
    }
</style>
