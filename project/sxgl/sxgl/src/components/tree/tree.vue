<template>
    <Tree :data="treeData" ref="xzqhTree" :render="renderContent" :load-data="xzqhLoadData" :popper-append-to-body="false" empty-text='暂无数据'></Tree>
</template>
<script>
    import unit from '@/api/index';
    export default {
        props:{
            treeData:{
                type: Array
            },
        },
        data () {
            return {
                // xzqhFlag: [],
            }
        },
        methods: {
            renderContent (h, { root, node, data }) {
                let that = this;
                return h('span', {
                    style: {
                        display: 'inline-block',
                        // width: '100%'
                    },
                    on: {
                        click: (e) => {
                            // console.log( root, node, data);
                            // console.log('data',data.index)
                            // if(data.existChild){
                                if(that.$parent.notSaveFlag){
                                    that.$Message.warning('有尚未保存的数据');
                                    return;
                                }
                                that.$parent.selected = data.label;
                                that.$parent.sjdm =  data.value;
                                that.$parent.getXzqhData();
                                $.each(root,function(idx,val){
                                    that.$set(val.node,'highLight',false)
                                })
                                data.highLight = true;
                            // }else{
                            //     that.$message.error('最末级的行政区划不可点击');
                            // }
                        },
                    }
                }, [
                    h('span',{
                            class:{
                                'tree-til':true,
                                'high-light':data.highLight ? true:false
                            }
                        }, [
                        h('i', {
                            class: {
                                'iconfont': true,
                                'icon-wenjian':data.isFolder ? false:true,
                                'icon-folder_icon':data.isFolder ? true:false
                            },
                            style: {
                                marginRight: '8px',
                                cursor:'pointer',
                            },
                        }),
                        h('span', data.label)
                    ]),
                   
                ]);
            },

            /*
            ** 行政区划加载子项
            */
            xzqhLoadData(item, callback) {
                let _that = this,
                    qhCode = item.value;
                    item.loading = true;
                setTimeout(() => {
                    let obj = {
                        value: qhCode
                    };
                    unit.ajaxMerPost('/znsj-web/xzqh/getXzqhTreeData', obj, function (result) {
                        if (result.flag) {
                            $.each(result.data, function (i, t) {
                                // t.title = t.label;
                                if(t.existChild){
                                    t.children = [];
                                    t.loading = false;
                                }
                            });
                            // item.isFolder = true;
                            item.children = result.data;
                            item.loading = false;
                            _that.$set(item,'expand',true);
                            // item.highLight = true;

                            setTimeout(()=>{
                                _that.$parent.xzqhVal = [];
                                for(let i=1;i<_that.$parent.xzqhFlag.length;i++){
                                    _that.$parent.xzqhVal.push(_that.$parent.xzqhFlag[i].value);
                                }
                            }, 0);
                        } 
                        else {
                            _that.$message.warning('服务端错误');
                        }
                        // item.loading = false;
                        // callback();
                    }, function (result) {
                        _that.$message.warning('服务端错误');
                    }, _that);
                }, 300);
            },
        }
    }
</script>
<style lang="less">
    .ivu-icon-ios-folder-outline,.ivu-icon-ios-paper-outline{
        font-size: 16px;
    }
    .ivu-tree-children span{
        cursor:pointer;
    }
    .tree-til{
        padding: 4px;
        border-radius: 3px;
    }
    .tree-til:hover {
        background-color: #eaf4fe;
    }
    .high-light{
        background-color: #daeaf9;
    }
</style>


