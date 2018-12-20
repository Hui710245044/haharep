<template>
    <page class="p-index-member" ref="page">
        <search :searchData="searchData" @search="search"></search>
        <el-row  class="ml-sm mb-xs mt-xs">
            <el-button type="primary" size="mini" @click="add">添加</el-button>
            <request-button :request="del_all">删除</request-button>
            <el-button type="primary" size="mini" @click.native="export_all">全部导出</el-button>
            <el-button type="primary" size="mini" @click.native="batch_edit">批量编辑</el-button>
        </el-row>
        <member-edit ref="member"  :is-add="isAdd" :batch-edit="batchEdit" :title="title" :dev-data="devData" v-model="memVisable"  @refresh="search" @batch-update="batch_update"></member-edit>
        <ui-table :has-data="tableData.length>0"
                  v-model="checkAll" :first-loading="firstLoading"
                  @check="changeAll" :body-height="41"
                  v-loading="loading" element-loading-text="玩命加载中..."
                  :heads="[
                    {isCheck:true,width:2},
                    {label:'平台',width:15},
                    {label:'账号简称',width:15},
                    {label:'销售员',width:15},
                    {label:'仓库类型',width:15},
                    {label:'客服',width:10},
                    {label:'添加时间',width:15},
                    {label:'操作',width:10},
                  ]">
            <tbody >
            <template v-for="(item, i) in tableData">
                <tr  v-for="(trs ,index) in item.info" @click="row_high_light(i)" :class="{active:item.isHighLight}">
                    <td :rowspan=" item.info.length"  v-if="index===0"><el-checkbox v-model="item.isCheck" @change="changeOne"></el-checkbox></td>
                    <td :rowspan=" item.info.length"  v-if="index===0"><span v-copy>{{item.channel_name}}</span></td>
                    <td :rowspan=" item.info.length"   v-if="index===0"> <span v-copy>{{item.code}}</span></td>
                    <td v-copy>{{trs.seller_name}}</td>
                    <td >{{trs.warehouse_type|typeFilter}}</td>
                    <td  :rowspan=" item.info.length"  v-if="index===0"> <span v-copy>{{item.customer_name}}</span></td>
                    <td  :rowspan=" item.info.length" v-if="index===0" > <times :time="item.create_time"></times></td>
                    <td  :rowspan=" item.info.length" v-if="index===0" >
                        <div >
                            <permission tag="span" :route="apis.url_member_edit" class="operate" @click="edit(item, index)">编辑</permission>
                            |&nbsp;<permission tag="span" :route="apis.url_member_del" class="operate" @click="del_one(item)">删除</permission>
                        </div>
                    </td>
                </tr>
            </template>
            </tbody>
        </ui-table>
        <el-pagination
            class="page-fixed"
            @size-change="handle_size_change"
            @current-change="handle_current_change"
            :current-page="searchData.page"
            :page-sizes="[20, 50, 100, 200,500]"
            :page-size="searchData.pageSize"
            layout="total, sizes, prev, pager, next, jumper"
            :total="total">
        </el-pagination>
    </page>
</template>
<style lang="stylus">
    .p-index-member{
        .active{
            background-color: rgba(173,227,255, 0.5);
        }
    }
</style>
<script>
    import {api_member_list,
        api_member_edit,
        api_member_del,
        api_member_batch,
        api_channels,
        api_product_account,
        api_export_member_ship_data,
        api_get_member_log,
        url_member_edit,
        url_member_del} from "../../../api/member"
    import {mapActions, mapGetters} from 'vuex';
    import {downloadFile} from '@/lib/http';
    export default{
        permission:{
            url_member_edit,
            url_member_del
        },
        page:{},
        refresh(){
            this.init();
        },
        data(){
            return {
                searchData:{
                    snType:"sales",
                    snText:'',
                    channel_id:"",
                    account_type: 1,
                    account_id:'',
                    account_code: '',
                    page:1,
                    pageSize:50,
                },
                total:0,
                loading:false,
                firstLoading: true,
                tableData:[],
                checkAll:false,
                memVisable:false,
                channel:[],
                customerList:[],
                isAdd:false,
                title:'',
                devData:[],
                editIndex:'',
                batchEdit: false
            }
        },
        mounted(){
//            this.init()
            this.get_channel()
        },
        computed: {
            ...mapGetters({accounts: 'params/channelAccount'}),
        },
        methods: {
            ...mapActions({load: 'params/setChannelAccount'}),
            init(){
                let data=window.clone(this.searchData);
                if(data.account_code){
                    let splitCode = this.split_search(data.account_code);
                    data.account_id = splitCode&&splitCode.length?splitCode.map(row=>row.trim()):'';
                }
                delete data.account_code;
                this.loading=true;
                this.$http(api_member_list,data).then(res=>{
                    this.loading=false;
                    this.firstLoading=false;
                    this.total=res.count;
                    this.tableData=res.data.map(row=>{
                        row.isCheck=false;
                        this.$set(row, 'isHighLight', false);
                        return row;
                    });
                }).catch(code=>{console.log(code)})
            },
            split_search(data){
                let curString = data.replace(new RegExp(' ','gm'),'\n');
                let cur = curString.split('\n').filter(row=>!!row);
                return cur
            },
            changeOne(){
                if(this.tableData.length>0){
                    this.checkAll=!this.tableData.find(data=>!data.isCheck);
                }
            },
            handle_size_change(val){
                this.searchData.pageSize=val;
                this.init()
            },
            handle_current_change(val){
                this.searchData.page=val;
                this.init()
            },
            changeAll(val){
                this.tableData.forEach(row=>{
                    row.isCheck=val;
                })
            },
            get_channel(){
                this.channel=[];
                this.$http(api_channels).then(res=>{
                    this.channel=[{value:"",label:"全部"},...res];
                }).catch(code=>{
                })
            },
            add(){
                this.isAdd=true;
                this.batchEdit=false;
                this.title="添加";
                this.devData=[];
                this.memVisable=true;
            },
            edit(row, index){
                this.editIndex = index;
                this.$http(api_member_edit,row.id).then(res=>{
                    delete res[0].channel_name;
                    delete res[0].account_name;
                    delete res[0].customer_name;
                    res[0].info.forEach(item => {
                        delete item.seller_name;
                    });
                    let channel=this.channel.find(it=>{
                        return it.value===res[0].channel_id;
                    });
                    res[0].warehouse=[
                        {label:"全部",value:0,isCheck:false},
                        {label:"本地",value:1,isCheck:false},
                        {label:"海外",value:2,isCheck:false},
                    ];
                    res.forEach(item=>{
                        if(this.accounts(`channel${item.channel_id}`).length){
                            item.param=[];
                        }else {
                            this.$http(api_product_account, {channel_id:item.channel_id}).then(ress => {
                                this.load({type:`channel${item.channel_id}`,channels:ress.account})
                                item.param=[];
                            }).catch(code => {
                                this.$message({type:"error", message: code.message || code});
                            });
                        }
                        item.type=`channel${item.channel_id}`;
                    });
                    this.isAdd=false;
                    this.batchEdit=false;
                    this.title=`编辑账号：${row.code} 绑定信息`
                    this.devData=res;
                    this.memVisable=true;
                }).catch(code=>{
                    console.log(code)
                })
            },
            del_all(){
                let data=[];
                let title=[]
                this.tableData.forEach(row=>{
                    if(row.isCheck){
                        data.push(row.id)
                        title.push(row.account_id)
                    }
                })
                if(data.length===0){
                    this.$message({type:"warning",message:"请选择删除账号"})
                    return false;
                }
                return this.$confirm(`您将删除该${title.join(",")}的绑定关系, 确认此操作吗?`, '提示', {
                    confirmButtonText: '确定',
                    cancelButtonText: '取消',
                    type: 'warning'
                }).then(() => {
                    return this.$http(api_member_batch,{data:data}).then(res=>{
                        this.$message({type:"success",message:res.message||res})
                        this.init()
                    }).catch(code=>{
                        this.$message({message:code.message||code,type:'error'})
                    })
                }).catch(() => {
                    this.$message({
                        type: 'info',
                        message: '已取消删除'
                    });
                });
            },
            del_one(item){
                this.$confirm(`您将删除${item.code}的绑定关系, 确认此操作吗?`, '提示', {
                    confirmButtonText: '确定',
                    cancelButtonText: '取消',
                    type: 'warning'
                }).then(() => {
                    this.$http(api_member_del,item.id).then(res=>{
                        this.$message({type:"success",message:res.message||res})
                        this.init()
                    }).catch(code=>{
                        this.$message({message:code.message||code,type:'error'})
                    })
                }).catch(() => {
                    this.$message({
                        type: 'info',
                        message: '已取消删除'
                    });
                });
            },
            export_all(){
                this.$http(api_export_member_ship_data).then(res=>{
                    let url = config.apiHost+'downloadFile/downExportFile';

                    let params = {
                        file_code:res.file_code
                    };
                    downloadFile({
                        url:url,
                        get:params,
                        fileName:res.file_name,
                        suffix:'.zip',
                    });
                }).catch(code => {
                    this.$message({type:"error",message:code.message||code})
                });
            },
            search(){
                this.init()
            },
            batch_update(){
                this.init()
            },
            row_high_light(index){
                this.tableData.forEach(row=>{
                     row.isHighLight = false;
                });
                this.tableData[index].isHighLight = true;
            },
            batch_edit(){
                let arr = this.tableData.filter(row =>row.isCheck);
                if(arr.length === 0){
                    this.$message({type: 'warning', message: '请选择需要编辑的账号'});
                    return
                }
                this.devData = [];
                this.memVisable = true;
                this.devData = arr.map(row=>{
                    let channel=this.channel.find(it=>it.value===row.channel_id);
                    if(this.accounts(`channel${row.channel_id}`).length){
                        row.param=[];
                    }else {
                        this.$http(api_product_account, {channel_id:row.channel_id}).then(ress => {
                            this.load({type:`channel${row.channel_id}`,channels:ress.account})
                            row.param=[];
                        }).catch(code => {
                            this.$message({type:"error", message: code.message || code});
                        });
                    }
                    row.type=`channel${row.channel_id}`;
                    let arr = row.info.map(item => {
                        return {
                            id: item.id,
                            seller_id: item.seller_id,
                            warehouse_type: item.warehouse_type
                        }
                    });
                    let warehouse = [
                        {label:"全部",value:0,isCheck:false},
                        {label:"本地",value:1,isCheck:false},
                        {label:"海外",value:2,isCheck:false},
                    ];
                    this.$set(row,'warehouse',warehouse);
                    row.warehouse.forEach(item => {
                        let find = row.info.find(obj=>{
                            return obj.warehouse_type === item.value;
                        });
                        if(!!find){
                            item.isCheck = true;
                        }
                    });
                    return {
                        isCheck:true,
                        channel_id: row.channel_id,
                        account_id: row.account_id,
                        info: arr,
                        customer_id: row.customer_id,
                        id:row.id,
                        type:row.type,
                        param:row.param,
                        warehouse: row.warehouse
                    }
                });
                this.title='批量编辑';
                this.isAdd = false;
                this.batchEdit = true
            }
        },
        filters: {
            typeFilter(val){
                switch (val){
                    case 0:
                        return "全部";
                    case 1:
                        return "本地" ;
                    case 2:
                        return "海外"
                }
            }
        },
        watch: {},
        props: {},
        components: {
            memberEdit:require("./member-edit.vue").default,
            search:require("./search.vue").default,
            uiTable:require("../../../components/ui-table.vue").default,
            requestButton:require('../../../global-components/request-button').default
        }
    }
</script>

