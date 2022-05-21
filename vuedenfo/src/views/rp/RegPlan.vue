<template>
    <div>
        <div>
            <div style="display: flex;justify-content: space-between">
                <div>
                    <el-input placeholder="请输入接口名称进行搜索，可以直接回车搜索..." prefix-icon="el-icon-search"
                              clearable
                              @clear="initRPs"
                              style="width: 350px;margin-right: 10px" v-model="keyword"
                              @keydown.enter.native="initRPs" :disabled="showAdvanceSearchView"></el-input>
                    <el-button icon="el-icon-search" type="primary" @click="initRPs" :disabled="showAdvanceSearchView">
                        搜索
                    </el-button>
                    <el-button type="primary" @click="showAdvanceSearchView = !showAdvanceSearchView">
                        <i :class="showAdvanceSearchView?'fa fa-angle-double-up':'fa fa-angle-double-down'"
                           aria-hidden="true"></i>
                        高级搜索
                    </el-button>
                </div>
                <div>
                    <el-button type="success" @click="exportData" icon="el-icon-download">
                        导出数据
                    </el-button>
                    <el-button type="primary" icon="el-icon-plus" @click="showAddRPView">
                        新建请求
                    </el-button>
                </div>
            </div>
            <transition name="slide-fade">
                <div v-show="showAdvanceSearchView"
                     style="border: 1px solid #409eff;border-radius: 5px;box-sizing: border-box;padding: 5px;margin: 10px 0px;font-size: 12px;">
                    <el-row>
                      <el-col :span="5">
                        请求id:<el-input size="mini" style="width: 150px" prefix-icon="el-icon-edit" v-model="searchValue.id"
                                       placeholder="请输入接口请求ID"></el-input>
                      </el-col>

                        <el-col :span="5">
                          接口名称:
                          <el-select v-model="searchValue.method" placeholder="请选择接口" size="mini"
                                     style="width: 150px;" >
                            <el-option
                              v-for="item in methodoptions"
                              :key="item.value"
                              :label="item.label"
                              :value="item.value">
                            </el-option>
                          </el-select>
                        </el-col>
                        <el-col :span="5">
                          状态:
                          <el-select v-model="searchValue.status" placeholder="请选择状态" size="mini"
                                        style="width: 150px;" >
                            <el-option
                              v-for="item in statusoptions"
                              :key="item.value"
                              :label="item.label"
                              :value="item.value">
                            </el-option>
                          </el-select>
                        </el-col>
                        <el-col :span="9">
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
                    </el-row>
                    <el-row style="margin-top: 10px">
                     
                      <el-col :span="14">
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
                        <el-button size="mini" icon="el-icon-search" type="primary" @click="initRPs('advanced')">搜索</el-button>
                      </el-col>
                    </el-row>
                </div>
            </transition>
        </div>
        <div style="margin-top: 10px">
            <el-table
                    :data="rps"
                    stripe
                    border
                    v-loading="loading"
                    element-loading-text="正在加载..."
                    element-loading-spinner="el-icon-loading"
                    element-loading-background="rgba(0, 0, 0, 0.8)"
                    style="width: 100%"
                    :header-row-class-name="tableRowClassName">
                <el-table-column
                        type="selection"
                        width="55">
                </el-table-column>
                <el-table-column
                  prop="id"
                  fixed
                  align="left"
                  label="请求ID"
                  width="90">
                </el-table-column>
                <el-table-column
                        prop="method"
                        label="接口名称"
                        align="left"
                        width="85">
                </el-table-column>
                <el-table-column
                        prop="status"
                        label="状态"
                        align="left"
                        width="85">
                </el-table-column>
                <el-table-column
                  prop="request"
                  width="600"
                  align="left"
                  label="请求参数">
                </el-table-column>
                <el-table-column
                        prop="response"
                        width="600"
                        align="left"
                        label="返回值">
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
                        <el-button @click="showEditRPView(scope.row)" style="padding: 1px" size="mini">编辑</el-button>
                      <el-button @click="dataParse(scope.row)" style="padding: 1px" size="mini" :disabled="showpaser">解析</el-button>
                        <el-button @click="interfaceRP(scope.row)" style="padding: 1px" size="mini" type="primary">发送药联</el-button>
                        <el-button @click="deleteRP(scope.row)" style="padding: 1px" size="mini" type="danger">删除</el-button>
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
                <el-form :model="rp" :rules="rules" ref="rpForm">
                    <el-row>
                        <el-col :span="4">
                          <el-form-item label="接口:" prop="method">
                            <el-select v-model="rp.method" placeholder="请选择接口" size="mini"
                                       style="width: 120px;">
                              <el-option
                                v-for="item in methodoptions"
                                :key="item.value"
                                :label="item.label"
                                :value="item.value">
                              </el-option>
                            </el-select>
                          </el-form-item>
                        </el-col>
                        <el-col :span="4">
                          <el-form-item label="状态:" prop="status">
                            <el-select v-model="rp.status" placeholder="请选择状态" size="mini"
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
                    </el-row>
                  <el-row>
                    <el-col :span="24">
                      <el-form-item label="请求参数:" prop="request">
                        <el-input size="mini" style="width: 90%" prefix-icon="el-icon-edit"
                                  v-model="rp.request" placeholder="请输入请求参数"></el-input>
                      </el-form-item>
                    </el-col>
                  </el-row>
                </el-form>
            </div>
            <span slot="footer" class="dialog-footer">
    <el-button @click="dialogVisible = false">取 消</el-button>
    <el-button type="primary" @click="doAddRP">确 定</el-button>
  </span>
        </el-dialog>
    </div>
</template>

<script>
    export default {
        name: "RegPlan",
        data() {
            return {
                searchValue: {
                    id: null,
                    method: null,
                    status: null,
                    createDT: null
                },
                title: '',
                showAdvanceSearchView: false,
                showpaser: false,
                rps: [],
                loading: false,
                popVisible: false,
                popVisible2: false,
                dialogVisible: false,
                total: 0,
                page: 1,
                keyword: '',
                size: 10,
                statusoptions: [{
                  value: 1,
                  label: '请求完成'
                }, {
                  value: 2,
                  label: '排队中'
                }],
                methodoptions: [{
                  value: 'getPatientInfo',
                  label: '花名册接口'
                }, {
                  value: 'getRegisterPlan',
                  label: '预约计划接口'
                }, {
                  value: 'trackingStatus',
                  label: '状态跟踪'
                }],
                rp: {
                    id: "",
                    method: "",
                    status: "",
                    request: "",
                    response: "",
                    createDT: "",
                    updateDT: ""
                },
                defaultProps: {
                    children: 'children',
                    label: 'name'
                },
                rules: {
                    method: [{required: true, message: '请输入接口名称', trigger: 'blur'}],
                    request: [{required: true, message: '请输入请求参数', trigger: 'blur'}],
                    status: [{required: true, message: '请选择状态', trigger: 'blur'}]
                }
            }
        },
        mounted() {
            this.initRPs();
        },
        methods: {
            exportData() {
                window.open('/rp/basic/export', '_parent');
            },
            dataParse(data) {
              if (data.method == "getRegisterPlan"){
                window.open("/rp/basic/parse/" + data.id, '_parent');
              }else if(data.method == "getPatientInfo"){
                window.open("/rp/basic/parsepi/" + data.id, '_parent');
              }else{
                this.$alert("无法解析！仅支持解析花名册和预约计划接口！")
              }
            },
            emptyRP() {
                this.rp = {
                    id: "",
                    method: "",
                    status: "",
                    request: "",
                    response: "",
                    createDT: null,
                    updateDT: null
                }
            },
          emptySearch() {
            this.searchValue = {
              id: "",
              method: "",
              status: "",
              request: "",
              response: "",
              createDT: null,
              updateDT: null
            }
          },
            showEditRPView(data) {
                // this.initPositions();
                this.title = '编辑接口请求信息';
                this.rp = data;
                this.dialogVisible = true;
            },
            deleteRP(data) {
                this.$confirm('此操作将永久删除【' +data.method + '】接口的编号'+data.id+'的请求数据, 是否继续?', '提示', {
                    confirmButtonText: '确定',
                    cancelButtonText: '取消',
                    type: 'warning'
                }).then(() => {
                    this.deleteRequest("/rp/basic/" + data.id).then(resp => {
                        if (resp) {
                            this.initRPs();
                        }
                    })
                }).catch(() => {
                    this.$message({
                        type: 'info',
                        message: '已取消删除'
                    });
                });
            },
            interfaceRP(data) {
              this.$confirm('此操作将向药联请求【' + data.method + '】接口, 是否继续?', '提示', {
                confirmButtonText: '确定',
                cancelButtonText: '取消',
                type: 'warning'
              }).then(() => {
                this.getRequest("/rp/basic/" + data.id).then(resp => {
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
                this.initRPs();
            },
           doAddRP() {
            if (this.rp.id) {
              this.$refs['rpForm'].validate(valid => {
                if (valid) {
                  this.putRequest("/rp/basic/", this.rp).then(resp => {
                    if (resp) {
                      this.dialogVisible = false;
                      this.initRPs();
                    }
                  })
                }
              });
            } else {
              this.$refs['rpForm'].validate(valid => {
                if (valid) {
                  this.postRequest("/rp/basic/", this.rp).then(resp => {
                    if (resp) {
                      this.dialogVisible = false;
                      this.initRPs();
                    }
                  })
                }
              });
            }
          },
            currentChange(currentPage) {
                this.page = currentPage;
                this.initRPs('advanced');
            },
            showAddRPView() {
                this.emptyRP();
                this.title = '添加记录';
                this.dialogVisible = true;
            },
            tableRowClassName({row, rowIndex}) {
              // console.log(rowIndex)
              if (rowIndex === 0) {
                return 'warning-row';
              } 
              return '';
            },
            initRPs(type) {
                this.loading = true;
                let url = '/rp/basic/?page=' + this.page + '&size=' + this.size;

                if (type && type == 'advanced') {

                  if (this.searchValue.id) {
                    url += '&id=' + this.searchValue.id;
                  }

                  if (this.searchValue.method) {
                    url += '&method=' + this.searchValue.method;
                  }

                  if (this.searchValue.status) {
                    url += '&status=' + this.searchValue.status;
                  }


                  if (this.searchValue.createDTScope) {
                    url += '&createDTScope=' + this.searchValue.createDTScope;
                  }

                }else {
                    url += "&method=" + this.keyword;
                }
                this.getRequest(url).then(resp => {
                    this.loading = false;
                    if (resp) {
                        this.rps = resp.data;
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

     .warning-row th {
      background: #E6F7FF!important
    }
</style>
