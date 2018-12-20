<template>
        <page class="p-index">
            <card-search
                    class="mb-xs"
                    @search="search"
                    :search-data="searchData">
            </card-search>
            <el-row class="mb-xs">
                <permission class="ml-sm"
                            tag="trendsSelect"
                            type="primary"
                            size="mini"
                            :route="apis.url_confirm_export"
                            :selects="partAllOptions"
                            @trigger="export_excel">
                </permission>
            </el-row>
            <table-data
                    @can-export='canExport'
                    :table-data="tableData"
                    :loading="loading"
                    :first-loading="firstLoading"
                    :total="total"
                    @select-check="selectCheck"
                    @size-change="handleSizeChange"
                    @current-change="handleCurrentChange">
            </table-data>
            <export-dialog v-model="visible"></export-dialog>
        </page>
    </template>
    <style lang="stylus">

    </style>
    <script>
        import {api_get_confirm,api_confirm_export,url_confirm_export} from '../../../api/delivery-list'
        export default{
            page:{
                devinfo:{
                    frontEnd:'汤敏',
                    backEnd:'何程',
                    createTime:'2018-7-23',
                    updateTime:'2018-7-23'
                }
            },
            refresh(){
                this.init();
            },
            permission: {
                url_confirm_export
            },
            data(){
                return{
                    searchData:{
                        channel_id:0,
                        warehouse_id:'',
                        carrier_id:'',
                        dateRange:'',
                        date_b:'',
                        date_e:'',
                    },
                    hasData:true,
                    tableData:{
                        list:[],
                        page:1,
                        pageSize:20,
                    },
                    total:1,
                    loading:false,
                    firstLoading: true,
                    visible:false,
                    checkData:[],
                    action:{},
                    export_type:'',
                    partAllOptions:[
                        {name:"部分导出",value:2,action:function(){
                            if(this.checkData.length<=0)return this.$message({type:"warning",message:"请先选择需要导出的数据"});
                            let data = this.deal_time(this.searchData);
                            let params = {
                                ids: JSON.stringify(this.checkData.map(row => {
                                    if (typeof row === 'object') {
                                        return row.id
                                    } else {
                                        return row
                                    }
                                })),
                                export_type: this.export_type,
                                channel_id: data.channel_id,
                                warehouse_id: data.warehouse_id,
                                carrier_id: data.carrier_id,
                                dateRange: data.dateRange,
                                date_b: data.date_b,
                                date_e: data.date_e,
                            };
                            this.batch_export(params);
                        }},
                        {name:"全部导出",value:1,action:function(){
                            if(this.tableData.list.length === 0)return this.$message({type:"warning",message:"列表无数据"});
                            let data = this.deal_time(this.searchData);
                            let params = {
                                ids:'',
                                export_type: this.export_type,
                                channel_id: data.channel_id,
                                warehouse_id: data.warehouse_id,
                                carrier_id: data.carrier_id,
                                dateRange: data.dateRange,
                                date_b: data.date_b,
                                date_e: data.date_e,
                            };
                            this.batch_export(params);
                        }},
                    ],
                }
            },
            mounted(){

            },
            methods:{
                search(){
                    this.init();
                },
                //处理年月日
                deal_time(searchData){
                    //处理年月日 YYYY-MM-dd
                    let data=clone(searchData);
                    if(searchData.date_b){
                        data.date_b=datef('YYYY-MM-dd HH:mm:ss', searchData.date_b/1000);
                    }else {
                        data.date_b='';
                    }
                    if(searchData.date_e){
                        data.date_e=datef('YYYY-MM-dd HH:mm:ss', searchData.date_e/1000);
                    }else {
                        data.date_e='';
                    }
                    return data;
                },
                export_excel(val){
                    this.export_type = val.value;
                    val.action.call(this,val);
                },
                selectCheck(select){
                    this.checkData = select.map(row=>{
                        return row.id;
                    });
                },
                batch_export(data){
                    return this.$http(api_confirm_export, data).then(res=>{
                        this.visible = true;
                        return Promise.resolve();
                    }).catch(code=>{
                        this.$message({type:"error",message:code.message || code});
                    });
                },
                init(){
                    this.loading = true;
                    let data = this.deal_time(this.searchData);
                    this.$set(data,'page',this.tableData.page);
                    this.$set(data,'pageSize',this.tableData.pageSize);
                    this.$http(api_get_confirm,data).then(res=>{
                        this.tableData.list = res.data;
                        this.total = res.count;
                        this.loading = false;
                        this.firstLoading = false
                    }).catch(code=>{
                        this.loading = false;
                        this.firstLoading = false;
                        console.log(code)
                    })
                },
                canExport(flag){
                    this.hasData = flag;
                },
                handleSizeChange(val) {//------------分页
                    this.tableData.page = 1;
                    this.tableData.pageSize = val;
                    this.init();
                },
                handleCurrentChange(val) {//------------分页
                    this.tableData.page = val;
                    this.init();
                },
            },
            components:{
                trendsSelect:require('@/components/trends-select').default,
                exportDialog:require('../export-dialog.vue').default,
                cardSearch:require('./card-search.vue').default,
                tableData:require('./table-data.vue').default,
            }
        }
    </script>
