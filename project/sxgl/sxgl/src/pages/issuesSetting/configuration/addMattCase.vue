/*
 * @Author: lhqin 
 * @Date: 2018-10-29 20:22:55 
 * @Last Modified by: kkfan2
 * @Last Modified time: 2019-01-15 19:23:04
 */

<template>
    <div id="addMattCase">
        <!-- 查询条件（基本查询）start -->
        <div class="search-wrap">
            <el-row>
                <el-col :span="6">
                    <label class="mr5">行政区划</label>
                    <div class="inline-block common-width">
                        <Cascader class="inline-block xzqhWt" :data="proData" :load-data="xzqhLoadData" v-model="xzqhVal" change-on-select filterable @on-change="changeXzqh" :transfer="true"></Cascader>
                    </div>
                </el-col>
                <el-col :span="6">
                    <label class="mr5">所属部门</label>
                    <div class="inline-block common-width">
                        <el-select v-model="deptVal" size="small" filterable>
                            <el-option v-for="item in deptData" :key="item.value" :label="item.label" :value="item.value">
                            </el-option>
                        </el-select>
                    </div>
                </el-col>
                <el-col :span="6">
                    <label class="mr5">事项名称</label>
                    <div class="inline-block common-width">
                        <el-input v-model="matterNameVal" :maxlength="100" placeholder="请输入事项名称" size="small" @change="(val)=>changeInput(val,'matterNameVal')"></el-input>
                    </div>
                </el-col>
                <el-col :span="4" class="ml10">
                    <div class="inline-block">
                        <el-button class="mr10 w70" type="primary" size="mini" @click="searchEvt">查询</el-button>
                        <a ref="higSearch" class="higher-search-a text-blue fz14" href="javascript:void(0)" v-bind:class="" @click="higSearchEvt">
                            <span>{{ higSerMsg }}</span>
                            <i class="iconfont icon-xia"></i>
                        </a>
                    </div>
                </el-col>
            </el-row>
        </div>

        <!-- 查询条件（高级查询）start -->
        <div class="search-wrap" v-bind:class="{hide:isHigRow}">
            <el-row>
                <el-col :span="6">
                    <label class="mr5">事项分类</label>
                    <div class="inline-block common-width matt-class">
                        <Select v-model="matterClassifyVal" :transfer="true">
                            <Option v-for="item in matterClaData" :value="item.value" :key="item.value">{{ item.label }}</Option>
                        </Select>
                    </div>
                </el-col>
                <el-col :span="6">
                    <label class="mr5">事项类型</label>
                    <div class="inline-block common-width">
                        <Select v-model="matterTypeVal" :transfer="true">
                            <Option v-for="item in matttypeData" :value="item.value" :key="item.value">{{ item.label }}</Option>
                        </Select>
                    </div>
                </el-col>
                <el-col :span="10">
                    <label class="mr5">事项编码</label>
                    <div class="inline-block common-other-width">
                        <el-input v-model="matterCodeVal" :maxlength="50" placeholder="请输入事项编码" size="small" @change="(val)=>changeInput(val,'matterCodeVal')"/>
                        </el-input>
                    </div>
                </el-col>
            </el-row>
        </div>

        <div class="pr10">

            <!-- 事项列表start -->
            <div class="list-wrap panel-border mt30" id="maskPanel">
                
                <el-table :data="mattCaseCfgData" :row-class-name="getRowClass" tooltip-effect="light" @select="selectChangeParent">
                    <!-- 展开项 -->
                    <el-table-column type="expand" prop="children" label="" width="60">
                        <template scope="scope">
                            <el-table :data="scope.row.childs" tooltip-effect="light" :show-header="false" @select="selectChangeChild">
                                <el-table-column v-if="type=='copy'" align="center" type="selection" width="55" :selectable="computSelectable">
                                </el-table-column>
                                <el-table-column label="事项名称" show-overflow-tooltip>
                                    <template scope="scope">
                                        <a :title="scope.row.matterName" class="mattLink" href="javascript:void(0);" @click="caseCfgEvt($event,scope.row)">{{scope.row.matterName}}</a>
                                    </template>
                                </el-table-column>
                                <el-table-column align="center" label="行政区划" width="110" show-overflow-tooltip>
                                    <template scope="scope">
                                        <span :title="scope.row.adminDivName">{{scope.row.adminDivName}}</span>
                                    </template>
                                </el-table-column>
                                <el-table-column label="所属部门" show-overflow-tooltip>
                                    <template scope="scope">
                                        <span :title="scope.row.deptName">{{scope.row.deptName}}</span>
                                    </template>
                                </el-table-column>
                            </el-table>
                        </template>
                    </el-table-column>
                    <!-- 展开项结束 -->
                    
                    <el-table-column v-if="type=='copy'" align="center" type="selection" width="55" :selectable="computSelectable">
                    </el-table-column>
                    <el-table-column label="事项名称" show-overflow-tooltip>
                        <template scope="scope">
                            <a :title="scope.row.matterName" class="mattLink" href="javascript:void(0);" @click="caseCfgEvt($event,scope.row)">{{scope.row.matterName}}</a>
                        </template>
                    </el-table-column>
                    <el-table-column align="center" label="行政区划" width="110" show-overflow-tooltip>
                        <template scope="scope">
                            <span :title="scope.row.adminDivName">{{scope.row.adminDivName}}</span>
                        </template>
                    </el-table-column>
                    <el-table-column label="所属部门" show-overflow-tooltip>
                        <template scope="scope">
                            <span :title="scope.row.deptName">{{scope.row.deptName}}</span>
                        </template>
                    </el-table-column>
                </el-table>
                <div class="block fr mt20">
                    <el-pagination :total="total" :page-size="pageSize" @size-change="cfgSizeChange" @current-change="cfgCurrentChange" :current-page="currentPage" layout="total, sizes, prev, pager, next, jumper">
                    </el-pagination>
                </div>
                
            </div>
            <!-- 事项列表end -->
        </div>

        <!-- 指南内容 -->
        <div v-if="type=='copy'" class="block footer">
            <el-button type="primary" size="mini" @click="copyEvt">确 定</el-button>
            <el-button size="mini" @click="cancel">取 消</el-button>
        </div>

    </div>

</template>
<script>
import unit from '@/api/index';
export default {
    props:{
        type:{
            type:String
        },
        srcId:{
            type:String
        }
    },
    data() {
        return {
            isHigRow: true,
            higSerMsg: '更多',

            saveXzqh: [], // 暂存行政区划
            saveFlag: true, // 暂存标志

            xzqhVal: [], // 行政区划
            deptVal: '', // 所属部门
            matterNameVal: '', // 事项名称
            matterClassifyVal: '', // 事项分类
            matterTypeVal: '', // 事项类型
            matterCodeVal: '', // 事项编号

            currentPage: 1,
            pageSize: 10,
            total: 0,
            mattCaseCfgData: [],

            proData: [],
            deptData: [],
            matterClaData: [],
            matttypeData: [],
            selectItemParent:[],
            selectItemChild:[]

        };
    },
    methods: {
        changeInput(val,attr){
            this[attr]=val==''?'':val;
        },
        computSelectable:function(row){
            if(row.hasConfig=='1')
               return false;
            else return true;
        },
        /*
        ** 行政区划改变联动部门
        */
        changeXzqh(value, selectedData) {
            let _that = this;
            if (value.length === 0) { // 行政区划清空时部门和机构清空
                _that.deptData = [];
                _that.deptVal = '';
            } else {
                _that.xzqhVal = value;
            }
            if (!this.saveFlag) {
                _that.getDeptData();// 联动部门
            }
        },
        /*
        ** 点击列表中的按钮跳转相应操作
        */
        caseCfgEvt($event, row) {
            this.$router.push({
                path: '/matterSituationConfigInfos',
                query: {
                    id: row.id,
                    name: row.matterName,
                    matteCode: row.matterCode,
                    matteVersion: row.matterVersion
                }
            });
        },
        /*
        ** 展/收列，类的回调
        */
        getRowClass: function (row, rowIndex) {
            if (row.row.childs == null || row.row.childs.length == 0) { // 无子项
                return 'row-expand-cover';
            }
        },
        /*
        ** 切换更多、常规
        */
        higSearchEvt(e) {
            this.isHigRow = this.isHigRow !== true;
            let iEle = $(e.target)
                .parent()
                .find('i');
            this.higSerMsg = this.higSerMsg === '更多' ? '常规' : '更多';
            iEle.hasClass('icon-xia')
                ? iEle.removeClass('icon-xia').addClass('icon-shang')
                : iEle.removeClass('icon-shang').addClass('icon-xia');
        },
        /*
        ** 获取事项配置列表数据
        */
        getConfigData() {
            let _that = this,
                jsonObj = {
                    adminDiv: _that.xzqhVal[_that.xzqhVal.length - 1],
                    deptCode: _that.deptVal,
                    matterClassify: _that.matterClassifyVal === 'all' ? '' : _that.matterClassifyVal,
                    matterCode: _that.matterCodeVal.trim(),
                    matterName: _that.matterNameVal.trim(),
                    matterType: _that.matterTypeVal === 'all' ? '' : _that.matterTypeVal,
                    type: '0',
                    status:'',
                    pageNum: _that.currentPage,
                    pageSize: _that.pageSize
                };
            unit.ajaxObjPost('/qxpz/situation/getSituationList', jsonObj, function (res) {
                if (res.flag == true) {
                    let data = res.data;
                    _that.total = data.total;
                    _that.currentPage = data.pageNum;
                    data = data.rows;
                    $.each(data, function (index, item) {
                        item.matterName =
                            item.matterName === (null || '') ? '--' : item.matterName;
                        item.adminDivName = item.adminDivName === null ? '--' : item.adminDivName;
                        item.deptName = item.deptName === (null || '') ? '--' : item.deptName;
                        item.status = item.status === (null || '') ? '--' : item.status;
                    });
                    _that.mattCaseCfgData = data;
                } else {
                    _that.$message.warning('请求数据失败');
                }
            }, function (res) {
                _that.$message.warning('请求数据失败');
            }, _that);
        },
        /*
        ** 查询
        */
        searchEvt() {
            let _that = this;
            _that.currentPage = 1;
            _that.getConfigData();
        },
        /*
        ** 事项列表每页显示数据量变更
        */
        cfgSizeChange: function (val) {
            let _that = this;
            _that.pageSize = val;
            _that.currentPage = 1;
            _that.getConfigData();
        },
        /*
        ** 事项列表页码变更
        */
        cfgCurrentChange: function (val) {
            let _that = this;
            _that.currentPage = val;
            _that.getConfigData();
        },
        /*
        ** 获取行政区划
        */
        getXzqhTreeData() {
            let _that = this,
                obj = {
                    value: _that.xzqhVal[0]
                };
            unit.ajaxMerPost('/qxpz/commer/getXzqhTreeData', obj, function (res) {
                if (res.flag == true) {
                    $.each(res.data, function (index, item) {
                        item.children = [];
                        item.loading = false;
                    });
                    _that.proData = res.data;
                    _that.proData.unshift({
                        label:'全部',
                        value:''
                    })
                    setTimeout(function () {
                        _that.getDefaultXzqh();
                    }, 0);
                } else {
                    _that.$message.warning('服务端错误');
                }
            }, function (res) {
                _that.$message.warning('服务端错误');
            }, _that);
        },
        /*
        ** 点击行政区划加载子项
        */
        xzqhLoadData(item, callback) {
            let _that = this,
                qhCode = item.value,
                itenLen = item.__value.split(',').length;
            item.loading = true;
            setTimeout(() => {
                let obj = {
                    value: qhCode
                };
                unit.ajaxMerPost('/qxpz/commer/getXzqhTreeData', obj, function (result) {
                    if (result.flag == true) {
                        if (itenLen < 4) {
                            if (result.data.length != 0) {
                                $.each(result.data, function (i, t) {
                                    if (t.existChild) {
                                        t.children = [];
                                        t.loading = false;
                                    }
                                });
                            }
                        }
                        item.children = result.data;
                        if (_that.saveFlag) {
                            setTimeout(function () {
                                _that.xzqhVal = _that.saveXzqh;
                                _that.getDeptData();
                                _that.saveFlag = false;
                            }, 0);
                        }
                    } else {
                        _that.$message.warning('服务端错误');
                    }
                    item.loading = false;
                    callback();
                }, function (result) {
                    _that.$message.warning('服务端错误');
                }, _that);
            }, 300);
        },
        /*
        ** 获取部门字典
        */
        getDeptData() {
            let _that = this,
                obj = {
                    adminDiv: _that.xzqhVal[_that.xzqhVal.length - 1]
                };
            unit.ajaxMerPost('/qxpz/commer/getDeptList.do', obj, function (res) {
                if (res.flag == true) {
                    _that.deptData = res.data;
                    _that.deptData.unshift({
                        label: '全部',
                        value: ''
                    });
                    _that.deptVal = '';
                } else {
                    _that.$message.warning('服务端错误');
                }
            }, function (res) {
                _that.$message.warning('服务端错误');
            }, _that);
        },
        /*
        ** 获取字典值公共方法
        */
        getDictionarys(str) {
            let _that = this,
                obj = {
                    pinYinType: str
                };
            unit.ajaxMerPost('/qxpz/dic/getDictionarys', obj, function (res) {
                if (res.flag == true) {
                    let data = res.data;
                    if (str === 'XZQH') {
                        _that.saveXzqh = [data[0].value, data[1].value]; // 暂存行政区划，供后面调用
                        _that.xzqhVal = [data[0].value, data[1].value];
                        _that.getConfigData();// 事项列表,防止行政区划接口慢，导致获取列表数据不传行政区划
                    } else if (str === 'SXFL') {
                        _that.matterClaData = data;
                        _that.matterClaData.unshift({
                            label: '全部',
                            value: 'all'
                        });
                        _that.matterClassifyVal = 'all';
                    } else if (str === 'SXLX') {
                        _that.matttypeData = data;
                        _that.matttypeData.unshift({
                            label: '全部',
                            value: 'all'
                        });
                        _that.matterTypeVal = 'all';
                    }
                } else {
                    _that.$message.warning('服务端错误');
                }
            }, function (res) {
                _that.$message.warning('服务端错误');
            }, _that);
        },
        /*
        ** 获取默认行政区划
        */
        getDefaultXzqh() {
            // let that = this;
            // unit.ajaxMerPost('/qxpz/commer/curentUserXzqh', {
            //     pinYinType: 'XZQH'
            // }, function (res) {
            //     if (res.flag) {
            //         that.saveXzqh = [];
            //         that.xzqhVal = [];
            //         let data = res.data;
            //         for (let i in data) {
            //             that.xzqhVal.push(data[i].value);
            //             that.saveXzqh.push(data[i].value);
            //         }
            //         that.getConfigData();
            //     } else {
            //         that.$Message.warning('服务端错误');
            //     }
            // }, function (res) {
            //     that.$Message.warning('服务端错误');
            // }, that);
            let that=this;
            that.saveXzqh=[];
            that.xzqhVal = [];
            that.xzqhVal.push('');
            that.saveXzqh.push('');
            that.getConfigData();
        },
        /*
        ** 事项分类字典
        */
        getMatterClaData() {
            let _that = this;
            _that.getDictionarys('SXFL');
        },
        /*
        ** 事项类型字典
        */
        getSxlxData() {
            let _that = this;
            _that.getDictionarys('SXLX');
        },
        /**
         * 多选框选中事件
         */

        selectChangeParent(rows,val){
            // let that = this;
            // that.selecteItem = [];
            // if(val.length >  0) {
            //     for(let i in val) {
            //         that.selecteItem.push(val[i].id);
            //     }
            // }
            this.selectItemParent = rows;
        },

        selectChangeChild(rows,val){
            let index =  rows.indexOf(val),
                isSelect = rows.length && index !== -1 ;
            //选中
            if(isSelect)
                this.selectItemChild.push(val);
            else {
                index=this.selectItemChild.indexOf(val);
                this.selectItemChild.splice(index,1);
            }
        },

        /**
         * 复制
         */

        copyEvt(){
            
            let that=this,
                selectItem = that.selectItemParent.concat(that.selectItemChild);

            if(selectItem.length==0){
                that.$Message.warning('请选择事项');
                return;
            }

            let ids=selectItem.map(item=>item.id);

            let param={
                srcId:this.srcId,
                itemId:ids.join(',')
            };

            unit.ajaxMerPost('/qxpz/situation/copyMatterSituation',param,function(res){
                if(res.flag===true){
                    that.$Message.success('复制成功');
                    that.$emit('refreshList');
                    that.$emit('closeDialog');
                    
                   
                }else{
                    that.$Message.error(res,errMsg);
                }

            },function(res) {
                that.$Message.error("复制失败");
            },this);
            
        },

        /**
         * 取消
         */

        cancel(){
            let that = this;
            that.$emit('closeDialog')
            
        },
        /*
        ** 初始化
        */
        init() {
            let _that = this;
            _that.getXzqhTreeData();// 获取行政区划
            // setTimeout(function () {
            //     _that.getConfigData();// 事项列表
            // }, 300);
            _that.getMatterClaData();// 事项分类字典
            _that.getSxlxData();// 事项类型字典
            // 解决ie兼容性问题 requestAnimationFrame
            unit.solveAnimFrame();
        }
    },
    mounted() {
        this.init();
    }
};
</script>
<style lang="stylus" rel="stylesheet/stylus">
#addMattCase {
    // overflow-y: auto;
    height: 100%;
    padding:0 15px;
    background-color: #fff;
    // padding-bottom:30px;
    .footer {
        margin-top: 70px;
        padding-top:10px;
        background: #fff;
        text-align: right;
        line-height: 50px;
        border-top: 1px solid #ddd;
        button {
            height:38px !important;
            width:60px;
        }
    }
    
    .el-dialog__wrapper {
        .el-dialog {
            width: 75%;

            .el-dialog__header {
                background-color: #409eff;
            }

            .el-dialog__header .el-dialog__title, .el-dialog__headerbtn .el-dialog__close {
                color: #fff;
            }

            .el-dialog__body {
                padding: 10px 20px;
            }
        }
    }

    .el-table__expanded-cell[class*=cell] {
        padding: 0;
        border-bottom: 0!important;
        padding-left: 60px;
    }

    .el-table {
        font-size: 14px;
    }

    .el-table thead td, .el-table thead th {
        padding: 8px 0;
    }

    .el-table tbody td {
        padding: 8px 0;
    }

    .el-dropdown-menu {
        width: 10%;
    }

    .el-button--primary {
        color: #fff;
        background-color: #2d8cf0;
        border-color: #2d8cf0;
    }

    .search-wrap {
        padding-left: 0px;
        margin-top: 18px;
    }

    .list-wrap .ivu-table-body {
        color: #515a6e;
    }

    .ivu-table td.matters-name {
        .ivu-table-cell {
            padding-left: 5px;
            overflow: hidden;
            white-space: nowrap;
            text-overflow: ellipsis;
        }
    }

    .a-color-red {
        a {
            color: #288ff4;
        }
    }

    .row-expand-cover {
        td:first-child .el-table__expand-icon {
            display: none;
        }
    }

    .mattLink {
        color: #409EFF;
    }

    .common-width {
        width: 67%;
    }

    .xzqhWt {
        width:100%;
    }

    .common-other-width {
        width: 40%;
        vertical-align:middle;
    }

    .matt-class {
        .el-select {
            width:100%;
        }
    }

    .status-width {
        width: 67%;
    }

    .mattLink {
        color: #409eff;
    }


}
</style>
