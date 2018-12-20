<template>
    <page class="p-plan-exploit">
        <search-card @search="init" @enter="init" :params="searchData" :clears="clears">
            <label-buttons class="inline" :buttons="flowBtns"
                           @select="select_flow_path" label="开发流程：" :max="40" :labelWidth="60"></label-buttons>
            <br>
            <label-item :labelWidth="60" label="申请人：" class="inline">
                <el-select class="inline s-width-small"
                           v-sf.create_id v-model="searchData.create_id" filterable clearable default-first-option>
                    <el-option v-for="item in proposer" :value="item.value" :label="item.label"
                               :key="item.value"></el-option>
                </el-select>
            </label-item>
            <el-select class="ml-sm inline s-width-small"
                       v-sf.search_key v-model="searchData.search_key">
                <el-option v-for="item in accountType" :value="item.value" :key="item.value"
                           :label="item.label"></el-option>
            </el-select>
            <el-input class="inline enter-result" v-sf.search_val v-model="searchData.search_val" :placeholder="`请输入${changeLabel}...`"></el-input>
            <label class="ml-sm">申请时间：</label>
            <el-date-picker class="date inline" v-sf.create_time_start v-model="searchData.create_time_start" :picker-options="inputTimeStart"
                            placeholder="开始时间"></el-date-picker>
            <span>--</span>
            <el-date-picker class="date inline mr-sm" v-sf.create_time_end v-model="searchData.create_time_end" :picker-options="inputTimeEnd"
                            placeholder="结束时间"></el-date-picker>
            <!--<el-button class="ml-lg" type="primary" size="mini" @click="init">搜索</el-button>-->
            <!--<el-button size="mini" @click="clear_search">清空搜索</el-button>-->
        </search-card>
        <div class="mt-xs mb-xs ml-sm">
            <el-button type="primary" size="mini" @click="create">创建</el-button>
            <el-button type="primary" size="mini" disabled>批量导出</el-button>
        </div>
        <el-table class="scroll-bar" v-resize="{height:41}" border
                  v-loading="loading" element-loading-text="玩命加载中..."
                  :data="tableList" highlight-current-row>
            <el-table-column
                type="selection"
            ></el-table-column>
            <el-table-column
                inline-template
                label="流程编号">
          <span>
            {{row.code}}
          </span>
            </el-table-column>
            <el-table-column
                inline-template
                width="300"
                label="产品名称">
        <span>
           <ui-tip :content="row.title" :width="98"></ui-tip>
          </span>
            </el-table-column>
            <el-table-column
                inline-template
                label="分类">
        <span>
            {{row.category}}
          </span>
            </el-table-column>
            <el-table-column
                inline-template

                label="审核人">
        <span v-copy>
            {{row.operator}}
          </span>
            </el-table-column>
            <el-table-column
                inline-template
                label="申请人">
        <span v-copy>
            {{row.creator}}
          </span>
            </el-table-column>
            <el-table-column
                inline-template
                label="申请时间">
        <span>
            {{row.create_time}}
          </span>
            </el-table-column>
            <el-table-column
                inline-template
                label="状态">
        <span>
            {{row.process}}
          </span>
            </el-table-column>
            <el-table-column
                label="操作">
                <template slot-scope="scope">
                    <el-button type="text" size="mini" @click="read_goods(scope.row)">查看</el-button>
                    <span v-if="scope.row.is_edit">|</span>
                    <el-button v-if="scope.row.is_edit" type="text" size="mini" @click="edit(scope.row)">编辑</el-button>
                    <span v-if="!scope.row.is_complete && !(scope.row.process_id===-1)">|</span>
                    <el-button v-if="!scope.row.is_complete && !(scope.row.process_id===-1)" type="text" size="mini" @click="dispose_goods(scope.row)">
                        处理
                    </el-button>
                </template>
            </el-table-column>
        </el-table>
        <el-pagination
            class="page-fixed"
            @size-change="handle_size_change"
            @current-change="handle_current_change"
            :current-page=this.searchData.page
            :page-size=this.searchData.pageSize
            :total=this.count
            layout="total, sizes, prev, pager, next, jumper"
            :page-sizes="[20, 50,100, 200, 500]">
        </el-pagination>

        <new-product v-model="newProductVisible" @select-product="select_product"></new-product>

        <create-sku-product v-model="createSkuVisible"
                            :selectProduct="selectProduct"
                            :isEdit="isEdit" :id="id"
                            :isComplete="isComplete"
                            :isDispose="isDispose"
                            :disposeBtn="disposeBtn"
                            :isCreate="isCreate"
                            :fromData="fromData"
                            :status="status"
                            @update-product="update_product"
                            @edit-product="edit_product">
        </create-sku-product>
        <create-success v-model="createProductVisible" :isEdit="isEdit" :fromData="fromData"
                        :isDispose="isDispose" :id="id" :isComplete="isComplete"
                        :disposeBtn="disposeBtn" :isDisposeEdit="isDisposeEdit" :status="status"
                        :selectProduct="selectProduct" @edit-product="edit_product"
                        @add-good="update_product" @dispose-goods="init">

        </create-success>

    </page>
</template>

<style lang="stylus">

</style>
<script>
    import {
        api_get_goods,
        api_goods_log,
        api_goods_process,
        api_read_good,
        api_proposer,
        api_product_person,
        api_get_goods_pre_dev_audit
    } from '../../../api/plan-exploit'
    import {api_processbtn} from '../../../api/product'
    export default{
        page: {},
        refresh(){
            this.init();
        },
        data(){
            return {
                publicForm: {},
                searchData: {
                    page: 1,
                    pageSize: 50,
                    process_id: '',
                    create_id: '',
                    search_key: 'code',
                    search_val: '',
                    create_time_start: '',
                    create_time_end: ''
                },
                clears:{
                    page: 1,
                    pageSize: 50,
                    process_id: '',
                    create_id: '',
                    search_key: 'code',
                    search_val: '',
                    create_time_start: '',
                    create_time_end: ''
                },
                id: '',
                isEdit: false,
                newIntroDialog: false,
                isComplete: false,
                isCreate: false,
                tableList: [],
                flowBtns: [],
                proposer: [],
                accountType: [
                    {label: "流程编号", value: 'code'},
                    {label: "产品名称", value: 'title'}
                ],
                count: 0,
                loading: false,
                selectProduct: {},
                onlyReadData: {},
                newProductVisible: false,
                createProductVisible: false,
                createSkuVisible: false,
                lookEditVisible: false,
                isDispose: false,
                isDisposeEdit: false,
                disposeBtn: [],
                status: '',
                fromData: {},
                inputTimeStart: {
                    disabledDate: (time) => {
                        if (this.searchData.create_time_end) {
                            return time.getTime() > this.searchData.create_time_end;
                        } else {
                            return false
                        }
                    }
                },
                inputTimeEnd: {
                    disabledDate: (time) => {
                        if (this.searchData.create_time_start) {
                            return time.getTime() < this.searchData.create_time_start;
                        } else {
                            return false
                        }
                    }
                }
            }
        },
        filters: {},
        mounted(){
            this.init();
            this.api_processbtn();
            this.get_proposer();
        },
        updated(){

        },
        destroy(){

        },
        methods: {
            init(){
                this.loading = true;
                this.data_format();
                this.$http(api_get_goods, this.searchData).then(res => {
                    this.tableList = res.data;
                    this.count = res.count;
                    this.loading = false;
                }).catch(code => {

                })
            },
            update_product(val, id){
                if (this.searchData.process_id === 0) {
                    let find = this.tableList.find(res => {
                        return res.id === val.id;
                    });
                    if (val.remark) {
                        if (val.code === 'kf_audit_fail') {
                            if (!!find) {
                                find.process = `开发主管审核不通过`;
                            }
                        } else if (val.code === 'kf_audit_success') {
                            if (!!find) {
                                find.process = `待销售主管审核`;
                            }
                        } else if (val.code === 'cancel') {
                            if (!!find) {
                                find.process = `作废`;
                            }
                        } else if (val.code === 'xs_audit_fail') {
                            if (!!find) {
                                find.process = `销售主管审核不通过`;
                            }
                        } else if (val.code === 'xs_audit_success') {
                            if (!!find) {
                                find.process = `待sku查重`;
                            }
                        } else if (val.code === 'sku_audit_fail') {
                            if (!!find) {
                                find.process = `sku查重不通过`;
                            }
                        } else if (val.code === 'sku_audit_success') {
                            if (!!find) {
                                find.process = `sku查重通过`;
                                this.$set(find,'is_complete', 0);
                                this.$set(find, 'is_create_sku', 1);
                                this.$set(find, 'is_edit', 0);
                            }
                        } else if (val.code === 'sku_success') {
                            if (!!find) {
                                find.process = `sku已生成`;
                            }
                        }
                    } else if (val.id) {
                        if (!!find) {
                            Object.assign(find, val);
                        }
                    } else {
                        this.$set(val, 'id', id);
                        this.$set(val, 'is_edit', 1);
                        this.tableList.unshift(val);
                        this.count += 1;
                    }
                } else {
                    let index = this.tableList.findIndex(row => {
                        return row.id === val.id;
                    });
                    this.tableList.splice(index, 1);
                    this.count -= 1;
                }
            },
            //日期整理
            data_format(){
                if (!!this.searchData.create_time_start) {
                    let b = new Date(this.searchData.create_time_start);
                    this.searchData.create_time_start = datef("YYYY-MM-dd", b / 1000);
                } else {
                    this.searchData.create_time_start = "";
                }
                if (!!this.searchData.create_time_end) {
                    let e = new Date(this.searchData.create_time_end);
                    this.searchData.create_time_end = datef("YYYY-MM-dd", e / 1000);
                } else {
                    this.searchData.create_time_end = "";
                }
            },

            create(){
                this.fromData = {
                    category_id: '',
                    dev_platform: '',
                    brand_id: '',
                    title: '',
                    tort_id: '',
                    purchase_price: '',
                    advice_price: '',
                    lowest_sale_price: '',
                    competitor_price: '',
                    gross_profit: '',
                    weight: '',
                    net_weight: '',
                    height: '',
                    length: '',
                    width: '',
                    volume_weight: '',
                    is_packing: '',
                    packing_id: '',
                    unit_id: '',
                    source_url: '',
                    thumb: [],
                    images: [],
                    tags: [],
                    description: '',
                    platform_sale: [
                        {
                            id: 1,
                            name: "ebay",
                            title: "eBay平台",
                            value_id: 1,
                            value: ''
                        },
                        {
                            id: 2,
                            name: "amazon",
                            title: "亚马逊平台",
                            value_id: 1,
                            value: ''
                        },
                        {
                            id: 3,
                            name: "wish",
                            title: "Wish平台",
                            value_id: 1,
                            value: ''
                        },
                        {
                            id: 4,
                            name: "aliExpress",
                            title: "速卖通平台",
                            value_id: 1,
                            value: ''
                        }
                    ],
                    propertie: [],
                    properties: [],
                    developer_note: '',
                    purchase_url: [],
                    audit: 0
                };
                this.isEdit = true;
                this.isDispose = false;
                this.isComplete = false;
                this.isDisposeEdit = false;
                this.newProductVisible = true;
                this.isCreate = true;
                this.status = '创建';
            },
            edit(row){
                this.selectProduct = {title: row.category, value: row.category_id};
                this.isEdit = true;
                this.isComplete = false;
                this.isDispose = false;
                this.isDisposeEdit = false;
                this.createSkuVisible = true;
                this.isCreate = false;
                this.status = `编辑产品：${row.title}信息`;
                this.get_goods_data(row);
            },
            read_goods(row){//查看
                this.selectProduct = {title: row.category, value: row.category_id};
                this.id = row.id;
                this.isCreate = false;
                this.isDispose = false;
                this.isEdit = false;
                this.isDisposeEdit = false;
                this.status = `查看产品：${row.title}信息`;
                if (row.is_complete === 1) {//完结
                    this.isComplete = true;
                    this.createSkuVisible = true;
                    this.get_goods_data(row);
                } else {
                    this.isComplete = false;
                    this.createSkuVisible = true;
                    this.get_goods_data(row);
                }
            },

            dispose_goods(row){//处理
                this.selectProduct = {title: row.category, value: row.category_id};
                this.id = row.id;
                this.isCreate = false;
                this.isDisabled = true;
                this.isEdit = false;
                this.isDispose = true;
                this.status = `处理产品：${row.title}信息`;
                this.get_audit(row.id);
                if (row.is_create_sku === 1) {//通过审核处理
                    this.isComplete = true;
                    this.isDisposeEdit = true;
                    this.createProductVisible = true;
                    this.get_goods_data(row);
                } else {
                    this.isComplete = false;
                    this.isDisposeEdit = false;
                    this.createSkuVisible = true;
                    this.get_goods_data(row);
                }
            },
            get_goods_data(row){
                this.fromData.images = [];
                this.$http(api_read_good, row.id).then(res => {
                    res.gross_profit = String(res.gross_profit);
                    res.net_weight = String(res.net_weight);
                    res.weight = String(res.weight);
                    if(res.warehouse_id === 0){
                        res.warehouse_id = ''
                    }
                    this.fromData = res;
//                    let img =  this.fromData.images;
//                    res.images.forEach(row=>{
//                        img.push(row)
//                    });
//                    Object.assign(this.fromData, res);
//                    this.fromData.images=img;
                    if (this.isDisposeEdit) {
                        this.$set(this.fromData, 'packing_id', '');
                        this.$set(this.fromData, 'packing_back_id', '');
                        this.$set(this.fromData, 'is_photo', '');
                        this.$set(this.fromData, 'photo_remark', '');
                        this.$set(this.fromData, 'undisposed_img_url', '');
                        this.$set(this.fromData, 'is_impower', '');
                        this.$set(this.fromData, 'is_sampling', '');
                        this.$set(this.fromData, 'is_multi_warehouse', '');

                    }
                    console.log(this.fromData);
                })
            },

            get_audit(id){
                this.$http(api_get_goods_pre_dev_audit, {id: id}).then(res => {
                    this.disposeBtn = res;
                })
            },

            get_proposer(){
                this.$http(api_product_person).then(res => {

                    let arr = res.map(row => {
                        return {
                            label: row.realname,
                            value: row.id
                        }
                    });
                    this.proposer = [{label: '全部', value: ''}, ...arr];
                })
            },

            //查询事件
            select_flow_path(index){
                this.searchData.process_id = this.flowBtns[index].value;
                this.init();
            },
            clear_search(){
                this.searchData = {
                    page: 1,
                    pageSize: 50,
                    process_id: '',
                    create_id: '',
                    search_key: 'code',
                    search_val: '',
                    create_time_start: '',
                    create_time_end: ''
                };
                this.init();
            },
            api_processbtn(){
                this.$http(api_goods_process).then(res => {
                    this.flowBtns = res.map(row => {
                        return {
                            name: row.title,
                            value: row.process_id
                        }
                    })
                });
            },

            //分页器事件
            handle_size_change(val){
                this.searchData.pageSize = val;
                this.init();
            },
            handle_current_change(val){
                this.searchData.page = val;
                this.init();
            },

            select_product(val){
                this.selectProduct = val;
                console.log(val);
                this.createSkuVisible = true;
            },
            edit_product(){
                this.newProductVisible = true;
            }
        },
        computed: {
            changeLabel(){
            	if(this.searchData.search_key === 'code'){
            		return `流程编号`;
                }else{
            		return `产品名称`;
                }
            }
        },
        watch: {},
        props: {},
        components: {
            labelItem: require('../../../components/label-item.vue').default,
            labelButtons: require('../../../components/label-buttons.vue').default,
            newProduct: require('./new-product.vue').default,
            editProduct: require("./edit-product.vue").default,
            createSkuProduct: require("./create-sku-product.vue").default,
            createSuccess: require("./create-success.vue").default,
            uiTip: require("../../../components/ui-tip.vue").default,
            searchCard:require('../../../components/search-card.vue').default,
        }
    }
</script>

