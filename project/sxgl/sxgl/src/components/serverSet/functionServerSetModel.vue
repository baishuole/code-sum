/*
 * @Author: qijiang 
 * @Date: 2018-10-29 10:52:06 
 * @Last Modified by: qijiang
 * @Last Modified time: 2019-01-28 19:36:59
 */
<template>
    <div id="dataServerSetModel">
        <div class="clearfix data-template-main">
            <div class="left-wrap fl">
                <div class="template-search">
                    <el-row>
                        <el-col :span="14">
                            <div class="inline-block common-width">
                                <el-input v-model="serviceName" :maxlength="10" size="small" @change="changeServiceName"></el-input>
                            </div>
                        </el-col>
                        <el-col :span="2" class="left-13">
                            <div class="template-search-btn" @click="searchBtn(2)">
                                <i class="iconfont icon-sousuo"></i>
                            </div>
                        </el-col>
                        <el-col :span="2" class="jiahao">
                            <i class="iconfont icon-jiahao1" @click="showTree"></i>
                        </el-col>
                        <el-col :span="2" class="jianhao">
                            <i class="iconfont icon-jianhao" @click="closeTree"></i>
                        </el-col>
                    </el-row>
                </div>
                <RadioGroup v-model="radioSelect" @on-change="changeRadio">
                    <Tree ref="treeNode" :data="dataTemplateTree" :render="renderContent"></Tree>
                </RadioGroup>
            </div>
            <p class="template-line"></p>
            <div class="right-wrap fr">
                <p class="no-data" v-show="shouNoData">
                    <i class="iconfont icon-gantanhao"></i>
                    请在左侧选择一个服务
                </p>
                <div class="list-wrap" v-show="!shouNoData">
                    <div class="server-info-wrap">
                        <p class="server-info-title font-min" v-if="param.name != ''">
                            <span class="title-handle">要素名称：</span>
                            <span class="body-handle">{{param.name}}</span>
                        </p>
                        <div class="server-info-main font-min">
                            <p class="server-info-item">
                                <span class="title-handle">服务描述：</span>
                                <span class="body-handle">{{infoDetail.serviceName}}</span>
                            </p>
                            <p class="server-info-item">
                                <span class="title-handle">服务编码：</span>
                                <span class="body-handle">{{infoDetail.serviceCode}}</span>
                                <span class="title-handle">版本号：</span>
                                <span class="body-handle">{{infoDetail.serviceVersion}}</span>
                                <span class="title-handle">返回格式：</span>
                                <span class="body-handle">{{infoDetail.retFormat}}</span>
                                <span class="title-handle">请求方式：</span>
                                <span class="body-handle">{{infoDetail.reqFormat}}</span>
                            </p>
                            <p class="server-info-item">
                                <span class="title-handle">请求参数：</span>
                            </p>
                        </div>
                        <div class="table-form">
                            <el-table ref="inputTable" :data="infoDetail.inputList" tooltip-effect="light" @selection-change="inputChangeFun">
                                <el-table-column type="selection" width="55">
                                </el-table-column>
                                <el-table-column label="参数">
                                    <template scope="scope">
                                        <span :title="scope.row.paramCode">{{scope.row.paramCode}}</span>
                                    </template>
                                </el-table-column>
                                <el-table-column label="要素名称" v-if="param.isMult == 1">
                                    <template scope="scope">
                                        <el-select v-model="scope.row.bindId" size="small" filterable :disabled="scope.row.disabled" @change="selectChange">
                                            <el-option v-for="item in ysmcData" :key="item.value" :label="item.label" :value="item.value" :title="item.label">
                                            </el-option>
                                        </el-select>
                                    </template>
                                </el-table-column>
                                <el-table-column label="类型">
                                    <template scope="scope">
                                        <span :title="scope.row.paramType">{{scope.row.paramType}}</span>
                                    </template>
                                </el-table-column>
                                <el-table-column label="必选">
                                    <template scope="scope">
                                        <span :title="scope.row.isReqiredTxt">{{scope.row.isReqiredTxt}}</span>
                                    </template>
                                </el-table-column>
                                <el-table-column label="描述">
                                    <template scope="scope">
                                        <span :title="scope.row.paramDesc">{{scope.row.paramDesc}}</span>
                                    </template>
                                </el-table-column>
                            </el-table>
                        </div>
                        <div class="server-info-main font-min">
                            <p class="server-info-item">
                                <span class="title-handle">返回参数：</span>
                            </p>
                        </div>
                        <div class="nav-wrap clearfix" v-if="param.isMult == 1 && infoDetail.outputList && infoDetail.outputList.length > 0 && infoDetail.outputList[0].outputList.length != 0">
                            <Icon type="ios-arrow-back" @click="scrollLast" v-if="showBtn" />
                            <div class="sub-wrap clearfix" :style="{width: subWrapWid}">
                                <div class="sub-item" :style="{width: subWid}">
                                    <span class="switch-default clearfix" v-for="(views ,index) in infoDetail.outputList" :class="{cur:iscur==index}" :title="views.templateName">
                                        <span ref="navs" @click="navClick(index)" class="ell font-max" :title="views.tableName">{{views.tableName}}</span>
                                        <i @click="delTree(views)" class="iconfont icon-fork"></i>
                                    </span>
                                </div>
                            </div>
                            <Icon type="ios-arrow-forward" @click="scrollNext" v-if="showBtn" />
                        </div>
                        <div class="table-form" v-for="(views ,index) in infoDetail.outputList" v-show="iscur==index">
                            <el-table ref="outputTable" :data="views.outputList" tooltip-effect="light" @selection-change="(p)=>outputChangeFun(p, index)">
                                <el-table-column type="selection" width="55">
                                </el-table-column>
                                <el-table-column label="参数">
                                    <template scope="scope">
                                        <span :title="scope.row.paramCode">{{scope.row.paramCode}}</span>
                                    </template>
                                </el-table-column>
                                <el-table-column label="类型">
                                    <template scope="scope">
                                        <span :title="scope.row.paramType">{{scope.row.paramType}}</span>
                                    </template>
                                </el-table-column>
                                <el-table-column label="必选">
                                    <template scope="scope">
                                        <span :title="scope.row.isReqiredTxt">{{scope.row.isReqiredTxt}}</span>
                                    </template>
                                </el-table-column>
                                <el-table-column label="描述">
                                    <template scope="scope">
                                        <span :title="scope.row.paramDesc">{{scope.row.paramDesc}}</span>
                                    </template>
                                </el-table-column>
                            </el-table>
                        </div>
                    </div>

                </div>
            </div>
        </div>
        <div class="footer" v-if="showGoBtn">
            <el-button size="small" @click="closeBtn" v-if="param.templateType==2">上一步</el-button>
            <el-button type="primary" size="small" @click="sureBtn">确定</el-button>
            <el-button size="small" @click="closeBtn" v-if="param.templateType!=2">取消</el-button>
        </div>
    </div>
</template>
<script>
import unit from "@/api";   // 公共工具方法
export default {
    props: {
        showGoBtn: { //是否显示上一步、下一步按钮
            type: Boolean,
            default: true
        },
        param: {
            type: Object
        }
    },
    data() {
        return {
            subWid: '100%',
            subWrapWid: '96%',
            iscur: 0, //当前选中列表
            distance: 0, //左右移动距离
            outputChangeId: [],  //返回参数复选框选中id,保存用
            inputChangeId: [],  //请求参数复选框选中id,保存用
            shouNoData: true, //空数据标志位
            showBtn: false,   //是否显示按钮
            serviceName: '',  //搜索值、模板名称
            dataTemplateTree: [],//树结构列表
            //服务信息详情
            infoDetail: {
                inputList: [],   //请求参数
                outputList: [],  //返回参数
                serviceName: '', //数据模板名称/服务名
                serviceCode: '', //服务编码
                serviceVersion: '', //版本号
                retFormat: '', //返回格式
                reqFormat: ''  //请求方式
            },
            firstFlag: true,  //第一次进入参数
            inputSelectIndex: 0, //请求参数选中的index
            ysmcData: [],  //要素名称数据源
            radioSelect: '' //树结构-单选框选中值
        }
    },
    methods: {
        //修复ie记忆问题
        changeServiceName(val) {
            this.serviceName = val == '' ? '' : val;
        },
        //下拉框选中获取数据
        selectChange(val) {
            let arr = [];
            for (let i = 0; i < this.infoDetail.inputList.length; i++) {
                if (this.infoDetail.inputList[i].bindId && !this.infoDetail.inputList[i].disabled) {
                    this.infoDetail.inputList[i].tempAttrId = this.infoDetail.inputList[i].bindId;
                    arr.push(this.infoDetail.inputList[i]);
                }
            }
            if (arr.length != 0) {
                this.getOutList(arr);
            }
        },
        //计算导航滚动宽度
        cupWidth() {
            let len = this.infoDetail.outputList.length,  //导航个数
                distanceRight = 0,   //距右边距离
                allWid = $('.nav-wrap')[0].offsetWidth,  //容器宽度
                subWrapWid = 0,
                subWid = 0,
                $dom = $('.sub-wrap .switch-default');  //每个导航
            for (let i = 0; i < len; i++) {
                subWid += $dom[i].offsetWidth + 2;
                distanceRight += 5;
            }

            if (subWid > allWid) {
                this.showBtn = true;
                subWrapWid = allWid - 36;
            } else {
                this.showBtn = false;
                subWrapWid = allWid;
            }
            if (subWid < 276) {
                subWid = 276;
            }
            this.subWrapWid = subWrapWid + 'px';
            this.subWid = subWid + distanceRight - 2 + 'px';
        },
        //导航点击切换
        navClick(index) {
            this.iscur = index;
        },
        //删除树复选框选中的数据
        delTree(views) {
            for (let i = 0; i < this.infoDetail.inputList.length; i++) {
                if (this.infoDetail.inputList[i].paramCode == views.inputParamCode) {
                    this.$refs.inputTable.toggleRowSelection(this.infoDetail.inputList[i], false);
                    this.infoDetail.inputList[i].bindId = '';
                }
            }
        },
        //向左滚动
        scrollLast() {
            this.distance -= 50;
            $('.sub-wrap').scrollLeft(this.distance);
            if (this.distance < 0) {
                this.distance = 0;
            }
        },
        //向右滚动
        scrollNext() {
            let maxWid = this.subWid.substr(0, this.subWid.length - 2) - this.subWrapWid.substr(0, this.subWrapWid.length - 2);
            this.distance += 50;
            $('.sub-wrap').scrollLeft(this.distance);
            if (this.distance > maxWid) {
                this.distance = maxWid;
            }
        },
        //关闭树结构
        closeTree() {
            this.dataTemplateTree[0].expand = false;
        },
        //请求参数复选框数据
        inputChangeFun(val) {
            let arr = [],
                that = this;
            if (this.infoDetail.inputList && this.infoDetail.inputList.length > 0) { //全部不可选择
                for (let i = 0; i < this.infoDetail.inputList.length; i++) {
                    this.infoDetail.inputList[i].disabled = true;
                }
            }

            if (val.length > 0) { //选中的可选择
                for (let i = 0; i < val.length; i++) {
                    val[i].disabled = false;
                }
            }

            //单服务配置,只能选择一个
            if (that.param.isMult == 0) {
                that.inputChangeId = JSON.parse(JSON.stringify(val));
            } else {  //多服务配置
                for (let i = 0; i < this.infoDetail.inputList.length; i++) {
                    $(val).each(function (index, obj) {
                        if (that.infoDetail.inputList[i].paramCode == obj.paramCode && obj.bindId) {
                            obj.tempAttrId = obj.bindId;
                            arr.push(obj);
                        }
                    });
                }
                if (arr.length != 0) {
                    this.getOutList(arr);
                } else {
                    this.infoDetail.outputList = [{
                        outputList: []
                    }];
                }
                that.inputChangeId = arr;
            }

        },
        //多功能服务配置获取输出list
        getOutList(arr) {
            let _that = this,
                jsonObj = {
                    dataTempId: _that.param.dataTemplateId,
                    funType: _that.param.templateType,
                    inputParam: arr,
                    linkId: _that.param.linkId,
                    serverCode: _that.serviceCode,
                    serverVersion: _that.serviceVersion
                };
            unit.ajaxObjPost('/znsj-web/funtion/server/clickAttr', jsonObj, function (res) {
                if (res.flag == true) {
                    if (res.data.length == 0) {  //返回值为空时，展示暂无数据
                        res.data[0] = {
                            outputList: []
                        }
                    }
                    if (!_that.infoDetail.outputList) { //值为空时，赋值空数组
                        _that.infoDetail.outputList = [];
                    }
                    let arr = [],
                        outFlag = 0,
                        bindFlag = 0;
                    for (let k = 0; k < _that.outputChangeId.length; k++) {
                        for (let m = 0; m < _that.infoDetail.outputList[k].outputList.length; m++) {
                            bindFlag = 0;
                            $(_that.outputChangeId[k]).each(function (index, obj) {
                                if (obj.paramCode == _that.infoDetail.outputList[k].outputList[m].paramCode && obj.bind) {
                                    _that.infoDetail.outputList[k].outputList[m].bind = true;
                                } else {
                                    bindFlag++;
                                }
                            });
                            if (bindFlag == _that.outputChangeId[k].length) {
                                _that.infoDetail.outputList[k].outputList[m].bind = false;
                            }
                        }
                    }
                    _that.outputChangeId = [];

                    if (_that.infoDetail.outputList.length != 0) {  //返回参数有值时，保持之前的状态
                        for (let i = 0; i < res.data.length; i++) {  //循环赋值，主要是保持用户选中的状态不被刷新
                            outFlag = 0;
                            for (let j = 0; j < _that.infoDetail.outputList.length; j++) {
                                if (res.data[i].id == _that.infoDetail.outputList[j].id && res.data[i].inputParamCode == _that.infoDetail.outputList[j].inputParamCode && res.data[i].tableName == _that.infoDetail.outputList[j].tableName) {
                                    arr[i] = _that.infoDetail.outputList[j];
                                    _that.outputChangeId[i] = JSON.parse(JSON.stringify(arr[i].outputList));
                                    j = _that.infoDetail.outputList.length;
                                } else {
                                    outFlag++;
                                }
                            }
                            if (outFlag == _that.infoDetail.outputList.length) {
                                arr[i] = res.data[i];
                                _that.outputChangeId[i] = [];
                            }
                        }
                        _that.infoDetail.outputList = arr;
                    } else {//返回参数无值时，直接赋值
                        _that.infoDetail.outputList = res.data;
                    }

                    if (res.data.length != 0 && _that.iscur >= res.data.length) {
                        _that.iscur = res.data.length - 1;
                    }
                    setTimeout(() => {
                        //处理返回参数选中
                        for (let i = 0; i < _that.infoDetail.outputList.length; i++) {
                            for (let j = 0; j < _that.infoDetail.outputList[i].outputList.length; j++) {
                                if (_that.infoDetail.outputList[i].outputList[j].bindId || _that.infoDetail.outputList[i].outputList[j].bind) {
                                    _that.$refs.outputTable[i].toggleRowSelection(_that.infoDetail.outputList[i].outputList[j], true);
                                }
                            }
                        }
                        _that.cupWidth();
                    }, 200);
                } else {
                    _that.$Message.warning(res.errMsg || '请求数据失败');
                }
            }, function (res) {
                _that.$Message.warning(res.data.errMsg || '请求数据失败');
            }, _that);
        },
        //返回参数复选框数据
        outputChangeFun(val, index) {
            for (let i = 0; i < this.infoDetail.outputList.length; i++) {
                if (index == i) {
                    let arr = JSON.parse(JSON.stringify(val));
                    $(arr).each(function (index, obj) {
                        obj.bind = true;
                    });
                    this.outputChangeId[i] = arr;
                } else if (!this.outputChangeId[i]) {
                    this.outputChangeId[i] = [];
                }
            }
        },
        //展开树结构
        showTree() {
            this.dataTemplateTree[0].expand = true;
        },
        //下拉树渲染
        renderContent(h, { root, node, data }) {
            let radioFlag = null;
            if (this.firstFlag && this.param.templateType != '2') {
                radioFlag = true;
                if (data.serviceCode + ',' + data.serviceVersion == this.radioSelect || this.param.isMult == 1 || this.radioSelect == '') {
                    radioFlag = false;
                }
            } else {
                radioFlag = false;
            }
            let str = [
                h('Radio', {
                    attrs: {
                        label: data.serviceCode + ',' + data.serviceVersion,
                        disabled: radioFlag
                    },
                    style: {
                        marginRight: '8px'
                    }
                }, [
                        h('span', data.title),
                    ])
            ],
                that = this,
                styleSet = {
                    style: {
                        display: 'inline-block',
                        width: 'auto'
                    }
                };

            if (data.leaf || node.nodeKey == 0) {  //有子项的样式以及图标设置
                str = [
                    h('Icon', {
                        props: {
                            type: 'ios-folder-outline'
                        },
                        style: {
                            marginRight: '8px'
                        }
                    }),
                    h('span', data.title)
                ];
            }
            return h('span', styleSet, [
                h('span', str),
                h('span', {
                    style: {
                        display: 'inline-block',
                        float: 'right',
                        marginRight: '32px'
                    }
                })
            ]);
        },
        /*
        ** 请求左边树的数据
        */
        getTreeData() {
            let _that = this,
                jsonObj = {
                    dataTemplateId: _that.param.dataTemplateId,
                    linkId: _that.param.linkId,
                    selectIds: _that.param.tempAttrId,
                    funType: _that.param.templateType,
                    serviceName: _that.serviceName.trim()
                };
            unit.ajaxMerPost('/znsj-web/funtion/server/tree', jsonObj, function (res) {
                if (res.flag == true) {
                    if (res.data.serverCode) {
                        _that.radioSelect = res.data.serverCode + ',' + res.data.serverVersion;
                        _that.serviceCode = res.data.serverCode;
                        _that.serviceVersion = res.data.serverVersion;
                        _that.getDetailByTempId(res.data.serverCode, res.data.serverVersion);
                    }
                    let data = _that.formatData(res.data.funcTree);
                    //赋值
                    _that.dataTemplateTree = data;
                } else {
                    _that.$Message.warning(res.errMsg || '请求数据失败');
                }
            }, function (res) {
                _that.$Message.warning(res.data.errMsg || '请求数据失败');
            }, _that);
        },
        /*
        ** 递归处理树形数据
        */
        formatData(data) {
            for (let i = 0; i < data.length; i++) {
                data[i].expand = true
                data[i].title = data[i].name;
                if (data[i].children.length > 0) {
                    data[i].children = this.formatData(data[i].children);
                }
            }
            return data;
        },
        /*
        ** 获取详情数据
        */
        getDetailByTempId(serviceCode, serviceVersion) {
            let _that = this,
                jsonObj = {
                    selectIds: _that.param.tempAttrId,
                    dataTemplateId: _that.param.dataTemplateId,
                    linkId: _that.param.linkId,
                    serviceCode: serviceCode,
                    serviceVersion: serviceVersion,
                    funType: _that.param.templateType,
                    isMult: _that.param.isMult
                };
            unit.ajaxObjPost('/znsj-web/funtion/server/funcDetails', jsonObj, function (res) {
                if (res.flag == true) {
                    _that.shouNoData = false;

                    //处理多选请求参数input置灰不可选
                    if (_that.param.isMult == 1 && res.data.inputList && res.data.inputList.length > 0) {
                        for (let i = 0; i < res.data.inputList.length; i++) {
                            res.data.inputList[i].disabled = true;
                        }
                    }
                    //处理单配置为数组
                    if (_that.param.isMult == 0) {
                        let arr = JSON.parse(JSON.stringify(res.data.outputList));
                        res.data.outputList = [];
                        res.data.outputList[0] = {
                            outputList: arr
                        };
                    } else {
                        if (!res.data.outputList) {
                            res.data.outputList = [];
                            res.data.outputList[0] = {
                                outputList: []
                            };
                        }
                    }

                    //赋值
                    _that.infoDetail = res.data;
                    setTimeout(() => {
                        //处理请求参数选中
                        for (let i = 0; i < res.data.inputList.length; i++) {
                            if (res.data.inputList[i].bindId) {
                                _that.$refs.inputTable.toggleRowSelection(_that.infoDetail.inputList[i], true);
                            }
                        }

                        if (_that.param.isMult == 0) {  //单配置
                            //处理返回参数选中
                            for (let i = 0; i < res.data.outputList[0].outputList.length; i++) {
                                if (res.data.outputList[0].outputList[i].bindId) {
                                    _that.$refs.outputTable[0].toggleRowSelection(_that.infoDetail.outputList[0].outputList[i], true);
                                }
                            }
                        }

                    }, 200);
                } else {
                    _that.$Message.warning(res.errMsg || '请求数据失败');
                }
            }, function (res) {
                _that.$Message.warning(res.data.errMsg || '请求数据失败');
            }, _that);
        },
        //搜索按钮
        searchBtn() {            ;
            //点击一次，请求一次数据
            this.getTreeData();
        },
        //确定按钮
        sureBtn() {
            let _that = this,
                arr = [],
                jsonObj = {
                    dataTempId: _that.param.dataTemplateId,
                    linkId: _that.param.linkId,
                    serverCode: _that.serviceCode,
                    funType: _that.param.templateType,
                    selectIds: _that.param.tempAttrId,
                    serverVersion: _that.serviceVersion,
                    outputDataType: _that.infoDetail.outputDataType,
                    reqFormat: _that.infoDetail.reqFormat,
                    retFormat: _that.infoDetail.retFormat,
                    serviceCode: _that.infoDetail.serviceCode,
                    serviceName: _that.infoDetail.serviceName,
                    serviceUrl: _that.infoDetail.serviceUrl,
                    serviceVersion: _that.infoDetail.serviceVersion
                };
            if (this.saveDataHandle().length == 0) {
                return;
            }
            jsonObj.funcParam = this.saveDataHandle();
            unit.ajaxObjPost('/znsj-web/funtion/server/selectSave', jsonObj, function (res) {
                if (res.flag == true) {
                    _that.$Message.success('保存成功');
                    _that.$emit('closeDialog');
                } else {
                    _that.$Message.warning(res.errMsg || '请求数据失败');
                }
            }, function (res) {
                _that.$Message.warning(res.data.errMsg || '请求数据失败');
            }, _that);
        },
        //保存数据处理
        saveDataHandle() {
            let outputChangeId = this.outputChangeId,
                inputChangeId = this.inputChangeId;
            if (this.param.isMult == 1) {
                let arr = [];
                for (let i = 0; i < this.infoDetail.inputList.length; i++) {
                    if (this.infoDetail.inputList[i].bindId && !this.infoDetail.inputList[i].disabled) {
                        this.infoDetail.inputList[i].tempAttrId = this.infoDetail.inputList[i].bindId;
                        arr.push(this.infoDetail.inputList[i]);
                    }
                    for (let j = i + 1; j < this.infoDetail.inputList.length; j++) {
                        if (!this.infoDetail.inputList[j].disabled && !this.infoDetail.inputList[i].disabled && this.infoDetail.inputList[i].bindId && this.infoDetail.inputList[i].bindId == this.infoDetail.inputList[j].bindId) {
                            this.$Message.warning('一个请求参数只能被一个数据要素绑定');
                            return;
                        }
                    }
                }

                inputChangeId = arr;
            } else {
                if (inputChangeId.length > 1) {
                    this.$Message.warning('一个请求参数只能被一个数据要素绑定');
                    return [];
                }
            }
            //校验
            if (inputChangeId.length == 0) {
                this.$Message.warning('请选择一个请求参数');
                return [];
            }
            //校验
            if (outputChangeId.length != 0) {
                for (let k = 0; k < outputChangeId.length; k++) {
                    if (outputChangeId[k].length == 0) {
                        this.$Message.warning('请选择一个返回参数');
                        this.$refs.navs[k].click();
                        return [];
                    } else {
                        for (let j = 0; j < outputChangeId[k].length; j++) {
                            if (this.param.isMult == 0) {
                                outputChangeId[k][j].tempAttrId = this.param.tempAttrId;
                            }
                        }
                    }
                }
            } else {
                this.$Message.warning('请选择一个返回参数');
                return [];
            }
            for (let i = 0; i < inputChangeId.length; i++) {
                if (this.param.isMult == 0) {
                    inputChangeId[i].tempAttrId = this.param.tempAttrId;
                }
                inputChangeId[i].outParam = outputChangeId[i];
            }
            return inputChangeId;

        },
        //取消按钮
        closeBtn() {
            if (this.param.templateType == 2) {
                this.$emit('go', 'last');
            } else {
                this.$emit('closeDialog');
            }
        },
        //单选框点击事件
        changeRadio(val) {
            let arr = val.split(',');
            this.serviceCode = arr[0];
            this.serviceVersion = arr[1];
            this.outputChangeId = []
            this.getDetailByTempId(arr[0], arr[1]);
            this.firstFlag = false;
            this.iscur = 0;
        },
        //获取要素名称字典
        getYsmcData() {
            let _that = this,
                jsonObj = {
                    funType: _that.param.templateType,
                    selectIds: _that.param.tempAttrId
                },
                url = '/funtion/server/getAttrDict';
            if (_that.param.templateType == '1') {

            } else if (_that.param.templateType == '2') {
                return;
            }
            unit.ajaxMerPost('/znsj-web' + url, jsonObj, function (res) {
                if (res.flag == true) {
                    _that.ysmcData = res.data;
                } else {
                    _that.$Message.warning(res.errMsg || '请求数据失败');
                }
            }, function (res) {
                _that.$Message.warning(res.data.errMsg || '请求数据失败');
            }, _that);
        },
        //供父事项调用
        parentHandle() {

        }
    },
    mounted() {
        //请求树结构数据
        this.getTreeData();
        this.getYsmcData();
    }
};
</script>
<style lang="less">
@import "../../assets/styles/theme.less";
.v-modal {
    z-index: 999 !important;
}
.el-dialog__body {
    padding: 30px 0;
    padding-top: 0;
}
#dataServerSetModel {
    .data-template-main {
        .ivu-tree-children {
            overflow: visible !important;
            height: auto !important;
            font-size: 14px;
        }
        position: relative;
        border-top: 1px solid #e4e4e4;
        border-bottom: 1px solid #e4e4e4;
        .el-table {
            .el-checkbox {
                margin-left: 0;
            }
            td {
                padding: 8px 0;
            }
        }
        .left-wrap {
            overflow: auto;
            min-height: 600px;
            padding: 20px 10px 0 10px;
            width: 30%;
            .left-13 {
                margin-left: -13px;
                margin-right: 0;
            }
            .jiahao {
                margin-left: 20px;
                height: 32px;
                line-height: 32px;
                i {
                    font-size: 20px;
                    color: #2d8cf0;
                    cursor: pointer;
                }
            }
            .jianhao {
                height: 32px;
                line-height: 32px;
                i {
                    font-size: 20px;
                    color: #2d8cf0;
                    cursor: pointer;
                }
            }
            .common-width {
                width: 100%;
            }
            .template-search-btn {
                position: relative;
                height: 32px;
                width: 32px;
                background-color: #f4f6f9;
                line-height: 32px;
                border-radius: 0 5px 5px 0;
                border: 1px solid #e0e6f1;
                z-index: 99;
                cursor: pointer;
                text-align: center;
                i {
                    font-size: 20px;
                }
            }
            .ivu-tree {
                & > .ivu-tree-children > li > .ivu-checkbox-wrapper {
                    display: none;
                }
                ul {
                    li {
                        width: 98%;
                    }
                }
            }
        }
        .right-wrap {
            padding: 0 10px 15px 10px;
            width: 70%;
            min-height: 600px;
            .no-data {
                height: 600px;
                line-height: 600px;
                text-align: center;
                .icon-gantanhao {
                    color: #137ddf;
                    font-size: 34px;
                    vertical-align: middle;
                }
            }
            .list-wrap {
                .server-info-wrap {
                    .server-info-title {
                        padding: 13px 0;
                        border-bottom: 1px dashed #e0e6f1;
                    }
                    .server-info-main {
                        .server-info-item {
                            padding: 7px 0;
                        }
                    }
                    .title-handle {
                        font-weight: bold;
                    }
                    .body-handle {
                        margin-right: 40px;
                    }
                    .table-form {
                        margin: 5px 0;
                        border: 1px solid #e0e6f1;
                        border-bottom: none;
                        .el-table {
                            border: none;
                        }
                        //未配置
                        .seted {
                            color: #606266;
                        }
                    }
                }
            }
            .nav-wrap {
                height: 40px;
                border-bottom: 1px solid #e4e4e4;
                .sub-wrap {
                    overflow-x: hidden;
                    float: left;
                    height: 40px;
                    .sub-item {
                        height: 100%;
                        font-size: 0px;
                        .switch-default {
                            float: left;
                            margin-right: 5px;
                            background-color: #f4f6f9;
                            height: 40px;
                            line-height: 38px;
                            text-align: center;
                            color: #515a6e;
                            box-sizing: border-box;
                            border: 1px solid #e4e4e4;
                            cursor: pointer;
                            span {
                                float: left;
                                padding: 0 10px;
                                height: 38px;
                                max-width: 250px;
                                font-size: 14px;
                            }
                            i {
                                float: left;
                                padding-right: 10px;
                                font-size: 14px;
                            }
                            &.cur {
                                background-color: #fff;
                                border-bottom: 1px solid #fff;
                            }
                            &:last-child {
                                margin-right: 0px;
                            }
                        }
                    }
                }
                .ivu-icon {
                    float: left;
                    line-height: 36px;
                    font-size: 18px;
                    cursor: pointer;
                    &.ivu-icon-ios-arrow-forward {
                        float: right;
                        position: relative;
                    }
                }
            }
        }
        .template-line {
            position: absolute;
            top: 0;
            bottom: 0;
            left: 30%;
            width: 1px;
            background: #e4e4e4;
        }
    }
    // 弹框按钮样式覆盖
    .footer {
        padding-right: 30px;
        padding-top: 10px;
        background: #fff;
        text-align: right;
        line-height: 50px;
        border: none;
        .el-button {
            padding: 8px 15px;
            width: 120px;
        }
    }
}
</style>
