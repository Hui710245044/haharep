<template>
    <page-dialog v-model="visible"
                 class="p-server-management"
                 :title="action.title"
                 height="300px"
                 :close-on-click-modal="false"
                 size="large">
        <div class="mb-sm">
            <el-button @click="add" type="primary" size="mini" class="ml-sm">添加</el-button>
            <el-button @click="batch_delete" type="primary" size="mini" v-if="is_batch">删除</el-button>
        </div>
        <ui-table
                v-if="manageData.length > 0"
                v-model="checkAll"
                @check="check(checkAll)"
                :heads="heads">
            <template v-resize="{height:200}">
                <tr :class="[row.status===2?'bg-red':'']" v-for="(row, index) in manageData" :key="index">
                    <td>
                        <el-checkbox v-model="row.isCheck" @change="changeOne"></el-checkbox>
                    </td>
                    <!--user_id-->
                    <td key="user">
                        <scroll-select v-model="row.user" class="inline"
                                       textAlign="left"
                                       @select-user="selectUser"
                                       ref="creater"
                                       :remote="urlcreater"
                                       :index="index"
                                       :fix-params="fix_params_account"
                                       :fixResult="fix_result_account">
                        </scroll-select>
                    </td>
                    <!--username-->
                    <td>
                        <el-input v-model="row.username"></el-input>
                    </td>
                    <!--操作-->
                    <td>
                        <el-button
                                @click="delete_account(index)"
                                type="text"
                                class="operate">
                            删除
                        </el-button>
                    </td>
                </tr>
            </template>
        </ui-table>
        <div slot="footer" class="dialog-footer">
            <request-button req-key="mangeSure" @click="sure">保 存</request-button>
            <el-button size="mini" @click.native="visible = false">取 消</el-button>
        </div>
        <el-progress type="line"
                     :text-inside="true"
                     class="percent"
                     v-if="percentVisible"
                     :percentage="percent"
                     :status="percentStatus">
        </el-progress>
    </page-dialog>
</template>
<style lang="stylus">
    .p-server-management{
        .percent{
            z-index: 999;
            background-color: rgba(0,0,0,0.8);
            position: absolute;
            top:0;
            right:0;
            bottom:0;
            left:0;
            margin:auto;
        }
        .el-progress__text{
            color: white;
        }
        .el-progress-bar{
            width:50%;
        }
        .el-progress-bar__outer{
            width: 50%;
            height: 20px !important;
            position: absolute;
            top:0;
            right:0;
            bottom:0;
            left:0;
            margin:auto;
        }
    }
</style>
<script>
    import {api_post_account, api_get_user} from '../../../api/server-management'
    export default {
        data(){
            return {
                visible: false,
                checkAll:false,
                userList:[],
                heads:[
                    {isCheck:true,width:2},
                    {label:'系统用户',width:8,isRequired:true},
                    {label:'服务器登录用户名',width:8,isRequired:true},
                    {label:'操作',width:7},
                ],
                percentVisible:false,
                percent:0,
                percentStatus:'',
                urlcreater:'get|user',
                is_batch:false,
            }
        },
        mounted(){},
        methods: {
            //表头的 全选框
            check(val){
                this.manageData.forEach(data => {
                    data.isCheck = val
                });
                this.changeOne();
            },
            changeOne(){
                if (this.manageData.length > 0) {
                    this.checkAll = !this.manageData.find(data => !data.isCheck);
                } else {
                    this.is_batch = false;
                }
                for(let i = 0; i <　this.manageData.length; i++){
                    if(this.manageData[i].isCheck){
                        this.is_batch = true;
                        break;
                    } else {
                        this.is_batch = false;
                    }
                }
            },
            delete_account(index){
                this.manageData.splice(index, 1);
                this.changeOne();
            },
            batch_delete(){
                this.manageData.forEach((row, index)=>{
                    if(row.isCheck){
                        this.manageData.splice(index,1);
                    }
                });
                this.changeOne();
            },
            add(){
                let account = {
                    user:{
                        user_id:'',
                        user_label:''
                    },
                    username:'',
                    isCheck:false
                };
                this.manageData.push(account);
            },
            sure(){
                this.percent = 0;
                this.percentStatus = '';
                this.percentVisible = true;
                let timer = setInterval(()=>{
                    if(this.percent < 90){
                        this.percent++ ;
                    } else if(this.percent >= 90 && this.percent < 99){
                        clearInterval(timer);
                        timer = setInterval(()=>{
                            this.percent++;
                            if(this.percent >= 99){
                                clearInterval(timer);
                                this.percent = 99;
                            }
                        },500)
                    }
                }, 25);
                let user_data = this.manageData.map(row=>{
                    if(row.user.value && row.username){
                        return {
                            user_id:row.user.value,
                            username:row.username,
                        }
                    } else {
                        this.$message({type:"warning",message:"请填写完整的信息"});
                    }
                });
                let params = {
                    server_id:this.action.server_id,
                    user_data:user_data
                };
                this.$http(api_post_account, params).then(res=>{
                    clearInterval(timer);
                    let timer2 = setInterval(()=>{
                        this.percent++;
                        if(this.percent >= 100){
                            clearInterval(timer2);
                            this.percentStatus = 'success';
                            setTimeout(()=>{
                                this.percentVisible = false;
                                this.visible = false;
                                this.$message({type:"success",message:res.message||res});
                            }, 500);
                        }
                    }, 10);
                }).catch(code=>{
                    clearInterval(timer);
                    this.percentStatus = 'exception';
                    setTimeout(()=>{
                        this.percentVisible = false;
                        this.$message({type:'error',message:code.message||code});
                    }, 500);
                }).finally(()=>{
                    setTimeout(() => {
                        this.$reqKey('mangeSure', false);
                    }, 300)
                })
            },
            //账号参数
            fix_params_account({page,pageSize,keyword}){
                return {
                    page:page,
                    pageSize:pageSize,
                    snText:keyword||"",
                    snType:"realname"
                };
            },
            //账号结果
            fix_result_account(res){
                return {
                    options: res.data.map(row => {
                        return {
                            label: row.realname,
                            value: row.id,
                            job_number: row.job_number
                        }
                    }),
                    page: res.page,
                    count: res.count,
                }
            },
            selectUser(item, index){
                let row = this.manageData[index];
                row.username = `rondaful${item.job_number}`;
                this.manageData.splice(index, 1, row);
            }
        },
        computed: {},
        watch: {
            visible(val){
                this.$emit('input',val);
            },
            value(val) {
                this.visible = val;
            },
        },
        props: {
            value:{},
            manageData:{},
            action:{}
        },
        components: {
            pageDialog:require("../../../components/page-dialog.vue").default,
            uiTable:require("../../../components/ui-table.vue").default,
            scrollSelect:require('../../../components/scroll-select.vue').default
        },
    }
</script>
