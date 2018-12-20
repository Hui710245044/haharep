<template>
    <page class="p-index">
        <search-list @search_list="search_list"
                     :clears="clears"
                     :search-data="searchData"
                     class="mb-sm">
        </search-list>
        <div class="mt-sm mb-sm">
            <permission tag="trendsSelect"
                        class="inline ml-sm p-btn-select"
                        type="primary"
                        :route="apis.url_manual_orders_export"
                        :selects="partAllOptions"
                        @trigger="export_sku">
            </permission>
        </div>
        <data-table :table-data="dataTable"
                    :is-load="loading"
                    :isFirst="isFirst"
                    :cur-width="curWidth"
                    @sort-click="sort_click"
                    @select-change="select_change">
        </data-table>
        <!--导出字段-->
        <export-field :param="export_model_type" v-model="exportVisable" :fields="fields" :templates="templates"
                      @getTemplate="get_templates" title="请选择自定义导出字段" :creat-excel="creat_excel">
        </export-field>
        <!--报表管理弹框-->
        <export-dialog v-model="export_visible"></export-dialog>
        <!--分页-->
        <div class="block">
            <el-pagination
                class="page-fixed"
                @size-change="handleSizeChange"
                @current-change="handleCurrentChange"
                :current-page="page"
                :page-sizes="[20,50,100,200,500]"
                :page-size="pageSize"
                layout="total, sizes, prev, pager, next, jumper"
                :total="total">
            </el-pagination>
        </div>
    </page>
</template>
<style lang="stylus">

</style>
<script>
    import searchList from './searchList.vue';
    import dataTable from './data-table.vue';
    import {api_order_pdd_list,api_order_pdd_status} from'../../.././../api/shopee-thailand-order';
    import {
        url_manual_orders_export,
        api_manual_orders_export
    } from '../../../../api/handwork';
    import {api_goods_export_template} from "../../../../api/product-library";
    import {api_orders_export_title} from '../../../../api/order-local';
    export default{
        permission: {
            url_manual_orders_export,
        },
        page:{
            devinfo:{
                frontEnd:'熊辉',
                backEnd:'翟雪莉',
                createTime:'2018-9-20',
                updateTime:'2018-9-20'
            }
        },
        refresh(){
            this.init();
        },
        data(){
            return{
                isFirst: true,
                firstLoading:true,
                is_batch:"",
                dataTable:[],
                total:0,
                page:1,
                pageSize:50,
                partAllOptions: [
                    {name: "部分导出", value: 0},
                    {name: "全部导出", value: 1}
                ],
                export_visible: false,
                export_model_type: {
                    type: 3
                },
                export_type: '',
                exportVisable: false,
                fields: [],
                templates: [],
                searchData:{
                    salesman: '',
                    site: '',
                    pay_status: '',
                    delivery_status: '',
                    select_time: '',
                    snType:'order_id',
                    snText:'',
                    account_id:'',
                    date_b:(Date.now()-(14*24*60*60*1000)),
                    date_e:Date.now(),
                    status:-1,
                    sort_val:'',
                    sort_type:'',
                },
                clears:{
                    snType:'order_id',
                    date_b:(Date.now()-(14*24*60*60*1000)),
                    date_e:Date.now(),
                },
                loading:false
            }
        },
        mounted(){
            this.init();
        },
        created(){
            if (window.screen.width === 1920) {
                this.curWidth = 180;
            } else if (window.screen.width === 1366) {
                this.curWidth = 0;
            }
            this.get_fields();
        },
        methods:{
            get_fields() {
                this.$http(api_orders_export_title).then(res => {
                    this.fields = res;
                }).catch(code => {
                    this.$message({type: "error", message: code.message || code})
                });
            },
            creat_excel(param) {
                let fields = param.field.join(',');
                let data = '';
                switch (this.export_type) {
                    case 0://部分
                        data = {
                            ids: this.orderIdList.map(row => {
                                if (typeof row === 'object') {
                                    return row.id
                                } else {
                                    return row
                                }
                            }),
                            export_type: this.export_type,
                        };
                        Object.assign(data,this.get_params());
                        return this.order_export(data, {
                            'X-Result-Fields': fields,
                            contentType: 'application/x-www-form-urlencoded'
                        });
                        break;
                    case 1://全部
                        data = this.get_params();
                        this.$set(data, 'export_type', this.export_type);
                        return this.order_export(data, {
                            'X-Result-Fields': fields,
                            contentType: 'application/x-www-form-urlencoded'
                        });
                        break;
                }
            },
            order_export(params, head) {
                return this.$http(api_manual_orders_export, params, head).then(res => {
                    if (res.join_queue === 1) {
                        this.$message({type: "success", message: res.message || res});
                        this.export_visible = true;
                    } else {
                        let url = config.apiHost + 'downloadFile/downExportFile';
                        let params = {
                            file_code: res.file_code,
                            page: this.tableData.page,
                            pageSize: this.tableData.pageSize,
                        };
                        downloadFile({
                            url: url,
                            get: params,
                            fileName: res.file_name,
                            suffix: '.xls',
                        });
                        this.$message({type: "success", message: '导出成功' || res});
                    }
                }).catch(code => {
                    this.$message({type: "error", message: code.message || code});
                });
                return Promise.resolve()
            },
            get_templates(){
                this.$http(api_goods_export_template, {type: 3}).then(res => {
                    res.forEach(row => {
                        row.value = row.name;
                    });
                    this.templates = res;
                }).catch(code => {
                    this.$message({type: "error", message: code.message || code})
                });
            },
            select_change(val){
                this.orderIdList = val;
                },
            export_sku(val){
                if(val.value === 0) return this.$message({
                    type: "warning",
                    message: "请先选择需要导出的数据"
                });
                this.export_type = val.value;
                this.exportVisable = true;
            },
            get_params() {
                let data = window.clone(this.searchData);
                this.$set(data, 'page', this.page);
                this.$set(data, 'pageSize', this.pageSize);
                let curString = data.snText.trim();
                if (curString.length > 0) {
                    let cur = data.snType==='name'? curString.split('\n').map(row=>row.trim()).filter(row => row !== ''):curString.replace(/\s/g,'\n').split('\n').map(row=>row.trim()).filter(row => row !== '');
                    data.snText = JSON.stringify(cur);
                }else{
                    data.snText = '';
                }
                if (!!data.date_b) {
                    data.date_b = datef('YYYY-MM-dd', data.date_b/1000);
                } else {
                    data.date_b = ''
                }
                if (!!data.date_e) {
                    data.date_e = datef('YYYY-MM-dd', data.date_e/1000);
                } else {
                    data.date_e = ''
                }
                return data;
            },
            init(){
                // this.loading=true;
                // this.$http(api_order_pdd_list,this.get_params()).then(res => {
                //     this.dataTable=res.data;
                //     this.isFirst = false;
                //     this.dataTable.forEach((data,i)=>{
                //         this.$set(this.dataTable[i], 'show',false);
                //         this.$set(this.dataTable[i], 'isCheck',false);
                //     });
                //     this.total=res.count;
                //     this.loading=false;
                //     this.firstLoading=false
                // }).catch(code => {
                //     this.$message({
                //         showClose: true,
                //         message: code.message || code,
                //         type: 'error'
                //     });
                //     this.loading=false;
                // })
            },
            //            表格升降序
            sort_click(val){
                switch (val.label){
                    case "支付总额":
                        this.searchData.sort_type="pay_amount";
                        break;
                    case "付款日期":
                        this.searchData.sort_type="confirm_time";
                        break;
                    case "平台发货状态":
                        this.searchData.sort_type="push_status";
                        break;
                    case "下单时间":
                        this.searchData.sort_type="created_time";
                        break;
                    case "最迟发货时间":
                        this.searchData.sort_type="last_ship_time";
                        break;
                }
                this.searchData.sort_val = val.order==='asc'?1:2;
                this.init();
            },
            handleSizeChange(val) {//---------------分页每页显示条数
                this.pageSize=val;
                this.init();
            },
            handleCurrentChange(val) {//------------分页当前页
                this.page=val;
                this.init();
            },
            search_list(){
                this.init();
            },
        },
        computed:{},
        components:{
            searchList,
            dataTable,
            trendsSelect: require('../../../../components/trends-select.vue').default,
            exportField: require("@/components/export-field").default,
            exportDialog: require('../../../report/export-dialog').default,
        }
    }
</script>
