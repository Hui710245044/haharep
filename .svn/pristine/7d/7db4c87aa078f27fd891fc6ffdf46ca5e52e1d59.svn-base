<template>
    <page>
        <el-row :gutter="8" class="p-send-rules">
            <el-col :span="5">
                <el-card class="card_left" v-resize="{height:66}">
                    <div slot="header" class="clearfix">
                        <label>触发时间</label>
                    </div>
                    <div v-for="(data, index) in triggerList" class="triggerList" @click="click_trigger(data)">
                        <i style="color:green" class="el-icon-caret-right" v-if="data.isShow"></i>{{data.trigger_rule_str}}
                        <span v-if="data.count">（{{data.count}}）</span>
                    </div>
                </el-card>
            </el-col>
            <el-col :span="19">
                <el-card>
                    <label-buttons label="是否启用：" :buttons="userStatus" @select="changeSelect" class="inline"></label-buttons>
                </el-card>
                <div class="function">
                    <permission tag="ElButton" :route="apis.url_add_send_rules" type="primary" @click.native="add" size="mini">添加</permission>
                    <permission tag="ElButton" :route="apis.url_sort_send_rules" v-if="isChange" type="primary" @click.native="save" size="mini">确定</permission>
                </div>
                <data-table @change-total="change_total" @change="isChange=true" @lookup="lookup" :tables="tables" :loading="loading"></data-table>
                <el-pagination
                        class="page-fixed"
                        @size-change="handleSizeChange"
                        @current-change="handleCurrentChange"
                        :current-page=initParams.page
                        :page-sizes="[20, 50, 100, 200,500]"
                        :page-size=initParams.pageSize
                        layout="total, sizes, prev, pager, next, jumper"
                        :total=tables.total>
                </el-pagination>
            </el-col>
        </el-row>
        <rule-dialog @add-update="add_update" :mdfid="mdfid" v-model="show" :title-name="titleName"></rule-dialog>
    </page>
</template>
<style lang="stylus">
    .p-send-rules{
        .card_left{
            .clearfix{
                color: #fff;
                text-align: center;
            }
            .el-card__header{
                background:#008BCE;
            }
            .el-card__body{
                padding: 0;
                overflow-y:auto;
            }
            .triggerList{
                line-height: 26px;
                text-align: center;
                border-bottom: 1px solid #e0e6ed;
                &:hover{
                    cursor: pointer;
                    background: rgb(219, 235, 215);
                }
            }
        }
        .function{
            padding: 5px 0 5px 10px;
        }
    }
</style>
<script>
    import {
        api_send_rules_tree,
        api_send_rules_list,
        api_sort_send_rules,
        url_add_send_rules,
        url_sort_send_rules
    } from '../../../api/send-rules'
    export default{
        permission: {
            url_add_send_rules,
            url_sort_send_rules
        },
        page:{},
        refresh(){
            this.init();
            this.triggerFun();
        },
        data(){
            return{
                triggerList:[],
                userStatus:[
                    {name:"已启用",status:0},
                    {name:"已停用", status:1}
                ],
                initParams:{
                    page:1,
                    pageSize:50,
                    status:0,
                    trigger_rule:''
                },
                titleName:'',
                loading:true,
                //--------------------------------------------
                show:false,
                mdfid:0,
                tables:{
                    total:0,
                    lists:[]
                },
                isChange:false,

            }
        },
        mounted(){
            this.init();
        },
        methods:{
            init_status_btn(){
                let userBtn = this.userStatus;
                this.userStatus = [];
                this.$nextTick(()=>{
                    this.userStatus = userBtn;
                });
            },
            //是否启用
            changeSelect(index){
                this.initParams.status = this.userStatus[index].status
                this.init()
            },
            triggerFun(){//-------------------触发时间列表
                this.$http(api_send_rules_tree).then(res=>{
                    this.triggerList = res;
                    this.triggerList.unshift({trigger_rule:0,trigger_rule_str:'全部',isShow:true});
                }).catch(code=>{
                    this.$message({
                        type:'error',
                        message:code.message||code,
                    })
                })
            },
            init(){//-----------------list列表
                this.loading = true;
                this.$http(api_send_rules_list,this.initParams).then(res=>{
//                    res.data = res.data.sort((s1,s2) =>{
//                        return s1.sort > s2.sort;
//                    });
                    this.tables.lists = res.data;
                    this.tables.lists.forEach(data =>{
                        this.$set(data,'isTurn',false);
                        this.$set(data,'number',1);
                        data.status = !data.status;
                    });
                    this.tables.total = res.count;
                    this.loading = false;
                }).catch(code=>{
                    this.$message({
                        type:'error',
                        message:code.message||code,
                    })
                })
            },
            add_update(val,id,action){
                console.log('val',val);
                console.log('111', this.initParams.trigger_rule);
            	if(this.initParams.trigger_rule !== val.trigger_rule && action !== 'add'&&this.initParams.trigger_rule !== ''&&this.initParams.trigger_rule !== 0){
            	    console.log('return');
            		return ;
                }
            	let data = {
            		id:id,
            		title:val.name,
                    trigger_rule_str:val.trigger_rule_str,
                    action:`发送站内信`,
                    status:!val.status,
                };
            	let find = this.tables.lists.find(row=>{
            		return row.id === id;
                });
            	let status = this.tables.lists.findIndex(row=>{
            		return row.status !== status;
                });
            	console.log('find',find);
            	if(!!find){
                    let index = this.tables.lists.findIndex(row=>{
                        return row.id === id;
                    });
                    this.tables.lists.splice(index,1,data);
                    if(find.status !== !val.status){
                        this.tables.lists.splice(index,1);
                    }
                }else{
            	    console.log(111)
                    this.tables.lists.push(data);
                    this.tables.total +=1;
                }
            },
            save(){//-------------排序保存
                let sort = this.tables.lists.map((row,index) =>{
                    return {id:row.id,sort:index}
                });
                this.$http(api_sort_send_rules, {sort}).then(res =>{
                    this.$message({
                        type:'success',
                        message:res.message||res
                    });
                    this.isChange = false;
                    this.init()
                }).catch(code=>{
                    this.$message({
                        type:'error',
                        message:code.message||code
                    });
                })
            },
            click_trigger(data){//-----------触发时间
                this.triggerList.forEach(item =>{
                    this.$set(item,'isShow',false);
                });
                data.isShow = true;
                this.initParams.trigger_rule = data.trigger_rule;
                this.init()

            },
            change_total(){
                this.tables.total -= 1;
            },
            handleSizeChange(size){
                this.initParams.pageSize = size;
                this.init()
            },
            handleCurrentChange(page){
                this.initParams.page = page;
                this.init()
            },
            //----------------------------------------------------------------
            add(){//----------添加
                this.show = true;
                this.mdfid = 0;
                this.titleName = '添加站内信/评价自动发送规则';
            },
            reload(){
                this.init();
            },

            lookup(itemid,title){//------------查看编辑
                this.mdfid = itemid;
                this.show = true;
                this.titleName = `查看编辑规则：${title} 信息`;
            },
        },
        watch:{
            show(val){
                if(!val){
                    this.mdfid = 0;
                }
            }
        },
        components:{
            dataTable:require('./data-table.vue').default,
            ruleDialog:require('./rule-dialog.vue').default,
            labelButtons:require('../../../components/label-buttons.vue').default,
            labelItems:require('../../../components/label-items.vue').default,
            labelItem:require('../../../components/label-item.vue').default,
            selectRemote:require('../../../components/select-remote.vue').default,
        }
    }
</script>
