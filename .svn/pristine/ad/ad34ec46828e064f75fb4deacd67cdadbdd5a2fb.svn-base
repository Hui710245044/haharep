<template>
    <page class="delivery-index">
        <search-card @search="search" :params="params" :clears="clears">
            <label-item label="仓库：" class="inline" title="请选择仓库">
                <permission tag="selectRemote" :route="apis.url_warehouse" class="s-width-default"
                            :clearable="false" v-sf.warehouse_id v-model="params.warehouse_id" :remote="remote"></permission>
            </label-item>
            <label class="ml-sm" title="请选择供应商">供应商：</label>
            <permission tag="supplier"
                        :route="apis.url_get_supplier"
                        :placeHolder="true"
                        v-sf.supplier_id
                        v-model="params.supplier_id"
                        class="inline s-width-large"></permission>
            <el-select class="ml-sm s-width-small inline"
                       v-sf.orderKey
                       v-model="params.orderKey">
                <el-option :key="order.value"
                           v-for="order in orders"
                           :label="order.label"
                           :value="order.value"
                ></el-option>
            </el-select>
            <order-input v-model="params.orderValue"
                         v-if="isBatch"
                         class="inline width-super mr-sm"
                         @keydown='search'
                         v-sf.orderValue
                         placeholder="可批量搜索，Shift+回车换行..."></order-input>
            <el-input :placeholder="`请输入${changeLabel}...`"
                      v-else
                      class="inline mr-sm"
                      @keydown.enter.native="search"
                      v-sf.orderValue
                      style="width: 200px;"
                      v-model="params.orderValue"></el-input>
        </search-card>

        <goods-classify ref="classifyPart" :leftControllerTitle="leftControllerTitle">
                <!-- 按钮 -->
                <div slot="button-list">
                    <permission tag="trendsSelect"
                                class="inline mr-sm"
                                type="primary"
                                :route="apis.url_safe_export"
                                :selects="partAllOptions"
                                @trigger="export_excel"></permission>
                    <permission style="margin-left: -3px;" class="inline mr-mini" tag="ElButton" :route="apis.url_import_delivery" type="primary"
                                size="mini" @click.native="showImport=true">导入
                    </permission>
                    <permission tag="request-button" :route="apis.url_change_delivery" class="inline" req-key="url_change_delivery" :mintime="200" @click="modify_date">{{modifyDate}}</permission>
                    <permission style="float:left;" tag="request-button" :route="apis.url_save" v-if="buttonShow" req-key="apis_url_save" :mintime="200" @click.native="save_date">保存交期</permission>
                </div>
                <!-- 商品列表树 -->
                <goods-tree slot="goods-tree"
                    @hidden-left="$refs.classifyPart.showLeft(true)"
                    @change-category="node_click">
                </goods-tree>
                <!-- 数据表格 -->
                <el-table
                    slot="goods-tabel"
                    class="scroll-bar"
                    :data="lists"
                    @selection-change="(sels)=>{this.selected=sels}"
                    :row-class-name="row_class_name"
                    v-loading="isLoading"
                    element-loading-text="玩命加载中..."
                    highlight-current-row
                    v-resize="{height:41}">
                    <div slot="empty" class="no-data-reminder">
                        <i></i>
                        {{emptyText}}
                    </div>
                    <el-table-column type="selection" width="35"></el-table-column>
                    <el-table-column label="图片" inline-template width="100">
                        <el-popover
                            placement="right"
                            trigger="hover">
                            <img v-lazy="sku_image(row.thumb)" width="200px" height="200px">
                            <span slot="reference">
                                    <img v-lazy="row.thumb" height="60px" width="60px" style="border:none" v-if="row.thumb">
                                </span>
                        </el-popover>
                    </el-table-column>
                    <el-table-column label="SKU/别名" inline-template width="120">
                        <div>
                            <div>
                                <ui-tip :content="row.sku" :width="98"></ui-tip>
                            </div>
                            <div>
                                <ui-tip :content="row.sku_alias|filterAlias" :width="98"></ui-tip>
                            </div>
                        </div>
                    </el-table-column>
                    <el-table-column label="名称" inline-template>
                        <div>
                            <ui-tip :content="row.name" :width="98"></ui-tip>
                        </div>
                    </el-table-column>
                    <el-table-column label="供应商" inline-template>
                        <div>
                            <ui-tip :content="row.company_name" :width="98"></ui-tip>
                        </div>
                    </el-table-column>
                    <el-table-column label="更新时间" inline-template width="150px">
                        <div>
                            <times :time="row.update_time"></times>
                        </div>
                    </el-table-column>
                    <el-table-column label="安全交期" inline-template width="120">
                        <el-input v-model="row.delivery_days" @input="(val)=>{input(row,val)}" style="width: 90px;"></el-input>
                    </el-table-column>
                </el-table>
        </goods-classify>
        <export-field :param="export_model_type"
                      v-model="exportVisable"
                      :fields="fields" :templates="templates"
                      @getTemplate="get_templates"
                      title="请选择自定义导出字段"
                      :fixparam="fixparam"
                      :creat-excel="creat_excel"></export-field>
        <export-dialog v-model="export_visible"></export-dialog>
        <el-pagination
            class="page-fixed"
            @size-change="handleSizeChange"
            @current-change="handleCurrentChange"
            :current-page="page"
            :page-sizes="[20,50,100,200,500]"
            :page-size="pageSize"
            layout="total, sizes, prev, pager, next, jumper"
            :total="count">
        </el-pagination>
        <import-excel v-model="showImport" @files-success="init"></import-excel>
    </page>
</template>
<style lang="stylus">
    .delivery-index{
        overflow-y: visible;
        .el-card{
            overflow: visible!important;
        }
    }
    .category_title{
        box-sizing: border-box;
        display: block;
        width:100%;
        height:30px;
        line-height: 28px;
        padding-left:10px;
        background:#008BCE;
        border:1px solid #008BCE;
        color:#fff;
        &:hover{
            background:rgb(51, 162, 216);
            cursor: pointer;
        }
    }
    .delivery-border{
        overflow-y: auto;
        border:1px solid #ddd;
    }
</style>
<script>
    import {
        api_get,api_categories, api_warehouse, api_change_delivery, api_save,api_import_delivery,
        url_import_delivery,
        url_warehouse,
        url_get,
        url_change_delivery,
        url_save,
        url_safe_export,
        api_safe_export,
        api_safe_export_fields
    } from '../../../api/procurement-delivery';
    import {api_goods_export_template} from  "../../../api/product-library";
    import {
        url_get_supplier
    } from '../../../api/supplier-quote';
    import {downloadFile} from '../../../lib/http';
    export default{
        permission:{
            url_warehouse,
            url_get_supplier,
            url_get,
            url_save,
            url_change_delivery,
            url_import_delivery,
            url_safe_export
        },
        page:{
            devinfo:{
                frontEnd:'吴楚光;王月如;黎宏珍',
                backEnd:'谭斌',
                createTime:'2017-1-13',
                updateTime:'2017-9-26'
            },
            beforeClose(){
                if(!this.buttonShow){
                    return true
                }else {
                    return   this.$confirm(`您的修改未保存,确定离开此页面吗?`, '提示', {
                        confirmButtonText: '确定',
                        cancelButtonText: '取消',
                        type: 'warning'
                    }).then(() => {
                        return true
                    }).catch(code=>{
                        return false
                    })
                }
            }
        },
        refresh(){
            this.init();
        },
        data(){
            return{
                firstLoading: true,
                leftControllerTitle: "显示产品分类检索",
                isLoading: false,
                showImport: false,
                showHiddenClassify: true,
                treeData:[],
                categories:{},
                orders:[
                    {label:'商品SKU',value:'sku'},
                    {label:'商品名称',value:'name'},
                    {label:'商品SPU',value:'spu'},
                ],
                params:{
                    warehouse_id:0,
                    supplier_id:'',
                    orderKey:'sku',
                    orderValue:'',
                },
                clears:{
                    warehouse_id:0,
                    orderKey:'sku',
                },
                page:1,
                pageSize:50,
                count:0,
                cate_id:0,
                lists:[],
                oldLists:[],
                selected:[],
                partAllOptions:[
                    {name:"部分导出",value:2},
                    {name:"全部导出",value:1}
                ],
                exportVisable:false,
                export_model_type:{
                    type:6
                },
                fields:[],
                templates:[],
                export_visible:false,
                export_type:''
            }
        },
        mounted(){
            this.$http(api_categories).then(res=>{
                this.categories = res;
                for(var i in res){
                    let treeObj = {};
                    if(res[i].hasOwnProperty("child_ids")&&(res[i].child_ids.length>0)){
                        treeObj.label =res[i].title;
                        treeObj.id = res[i].id;
                        treeObj.children = res[i].child_ids.map(row=>{
                            return {
                                id:res[row].id,
                                label:res[row].title
                            }
                        });
                        this.treeData.push(treeObj);
                    }
                }
            });
            this.init();
            this.get_fields();
            this.get_templates();
        },
        methods:{
            fixparam(list){
                return list.map(row=>{
                    return {
                        field_name:row.show_field,
                        field_key:row.field
                    }
                });
            },
            order_export(params){
                return this.$http(api_safe_export,params).then(res=>{
                    if(res.join_queue===1){
                        this.export_visible = true;
                        this.$message({type:"success",message:res.message || res});
                    }else{
                        let url = config.apiHost+'downloadFile/downExportFile';
                        let params = {
                            file_code:res.file_code,
                            page:this.page,
                            pageSize:this.pageSize,
                        };
                        downloadFile({
                            url:url,
                            get:params,
                            fileName:res.file_name,
                            suffix:'.xls',
                        });
                        this.$message({type:"success",message:'导出成功' || res});
                    }
                }).catch(code=>{
                    this.$message({type:"error",message:code.message||code});
                })
                return Promise.resolve()
            },
            get_fields(){
                this.$http(api_safe_export_fields).then(res=>{
                    this.fields=res;
                }).catch(code => {
                    this.$message({type:"error",message:code.message||code})
                });
            },
            get_templates(){
                this.$http(api_goods_export_template,{type:6}).then(res=>{
                    res.forEach(row=>{
                        row.value=row.name;
                    })
                    this.templates=res;
                }).catch(code => {
                    this.$message({type:"error",message:code.message||code})
                });
            },
            export_excel(val){
                if(val.value===2&&this.selected.length<=0)return this.$message({type:"warning",message:"请先选择需要导出的数据"});
                this.export_type = val.value;
                this.exportVisable=true;
            },
            creat_excel(param){
                let data = '';
                switch (this.export_type){
                    case 2://部分
                        data = {
                            ids:JSON.stringify(this.selected.map(row=>{
                                if(typeof row === 'object'){
                                    return row.id
                                }else{
                                    return row
                                }
                            })),
                            export_type:this.export_type,
                            fields:param
                        };
                        Object.assign(data,this.init_params());
                        return this.order_export(data);
                        break;
                    case 1://全部
                        data = this.init_params();
                        this.$set(data,'export_type',this.export_type);
                        this.$set(data,'fields',param);
                        return this.order_export(data);
                        break;
                }
            },
            sku_image(images){
                if(!!images){
                    return images.replace('_60x60.','_200x200.')
                }
                return ""
            },
            remote(callback){
                this.$http(api_warehouse).then(res=>{
                    callback([...res.map(row=>{return {label:row.name,value:row.id}})]);
                });
            },
            //                过滤供应商
            fix_params({page,pageSize,keyword}){
                return {
                    page:page,
                    pageSize:pageSize,
                    content:keyword,
                };
            },
            handleSizeChange(pageSize){
                this.pageSize = pageSize;
                this.init();
            },
            handleCurrentChange(page){
                this.page = page;
                this.init();
            },
            node_click(cate){
                this.cate_id = cate;
                this.init();
            },
            all(){
                this.cate_id = 0;
                this.init();
            },
            init_params(){
                let data = {page:this.page,pageSize:this.pageSize};
                if(this.params.orderValue.trim() !== ''){
                    data.snType = this.params.orderKey;
                    if(this.isBatch){
                        data.snText = JSON.stringify(this.params.orderValue.split('\n').map(row=>row.trim()).filter(row=>row!==''));
                    }else{
                        data.snText = this.params.orderValue.trim();
                    }
                }
                if(this.params.warehouse_id > 0){
                    data.warehouse_id = this.params.warehouse_id;
                }
                if(this.cate_id > 0){
                    data.category_id = this.cate_id;
                }
                this.params.supplier_id&&(data.supplier_id = this.params.supplier_id);
                return data
            },
            init(){
                let data = this.init_params();
                this.isLoading = true;
                this.$http(api_get, data).then(res=>{
                    this.lists = res.data.map(row=>{
                        row.old_delivery_days = row.delivery_days;
                        return row;
                    });
                    this.count = parseInt(res.count);
                    this.isLoading = false;
                    this.firstLoading = false
                }).catch(code=>{
                    this.isLoading = false;
                    console.log(code);
                });
            },
            input(row, val){
                val&&(row.delivery_days = parseInt(val));
            },
            search(){
                this.init();
            },
            clear_search(){
                this.supplier_id='';
                this.warehouse_id = 0;
                this.orderKey = 'sku';
                this.orderValue = '';
                this.init();
            },
            modify_date(){
                if(this.selected.length > 0){
                    this.$prompt('初始化统一交货周期为：', '安全交期快速初始化', {
                        confirmButtonText: '确定',
                        cancelButtonText: '取消',
                        inputPattern: /^[\d]{1,}$/,
                        inputErrorMessage: '请输入数字'
                    }).then(({ value }) => {
                        this.$http(api_change_delivery, this.selected.map(row=>row.id), value).then(res=>{
                            this.$message({
                                type:'success',
                                message:res.message
                            });
                            this.selected.forEach(row=>{
                            	this.$set(row,"delivery_days",value);
                            });
                            this.operate_save(this.selected);
                        }).catch(code=>{
                            this.$message({
                                type:'error',
                                message:code.message || code
                            });
                        }).finally(()=>{
                            setTimeout(()=>{
                                this.$reqKey('url_change_delivery',false)
                            },);
                        });
                    }).catch(() => {
                        this.$message({
                            type:'info',
                            message:'已取消！'
                        });
                    }).finally(()=>{
                        setTimeout(()=>{
                            this.$reqKey('url_change_delivery',false);
                        },);
                    });
                }else{
                    this.$reqKey('url_change_delivery',false);
                    this.$message({
                        type: 'warning',
                        message: '请先选择批量设置项！'
                    });
            }
            },
            operate_save(change){
                let timestamp = Date.parse(new Date());
                timestamp = timestamp / 1000;
                change.forEach(out => {
                    let find = this.lists.find(row=>{
                        return row.id === out.id;
                    });
                    if(!!find){
                        find.update_time = timestamp;
                        find.old_delivery_days = out.delivery_days;
                        Object.assign(find,out)
                    }
                })
            },
            save_date(){
                let change = [];
                this.lists.forEach(row=>{
                    if(row.old_delivery_days !== row.delivery_days){
                        change.push({id:row.id,delivery_days:row.delivery_days});
                    }
                });
                if(change.length<=0){
                    this.$reqKey('apis_url_save',false);
                    this.$message({type:"warning",message:`当前数据无更改`});
                    return;
                }
                this.$http(api_save, change).then(res=>{
                    this.$message({
                        type:'success',
                        message:res.message
                    });
                    this.operate_save(change);
                    return Promise.resolve();
                }).catch(code=>{
                    this.$message({
                        type:'error',
                        message:code.message.replace('delivery_days','安全交期') ||code
                    });
                }).finally(()=>{
                    setTimeout(()=>{
                        this.$reqKey('apis_url_save',false);
                    },200);
                });
            },
            row_class_name(row){
                if(row.old_delivery_days !== row.delivery_days){
                    return 'row-change';
                }else{
                    return '';
                }
            }
        },
        filters:{
            filterAlias(val){
                return Array.isArray(val)?val.join(','):val
            },
        },
        computed:{
            emptyText() {
              return this.firstLoading?'等待请求数据中...':'暂无数据'
            },
            isBatch(){
                return this.params.orderKey==='sku'||this.params.orderKey==='spu'
            },
            buttonShow(){
                if(this.lists.length>0){
                    let cur = this.lists.find((row)=>{
                        return row.old_delivery_days !==row.delivery_days
                    })
                    if(cur){
                        return true
                    }else{
                        return false
                    }
                }else{
                    return false;
                }
            },
            modifyDate(){
                let length = this.selected.length;
                if(length > 0){
                    return `批量设置交期(${length})`;
                }else{
                    return `批量设置交期`;
                }
            },
            classifyBoxSpan(){
                return this.showHiddenClassify ? 21:24;
            },
            showHIddenClassifyName(){
                return this.showHiddenClassify ? `隐藏产品分类检索`:`显示产品分类检索`;
            },
            changeLabel(){
            	let find = this.orders.find(res=>{
            		return res.value === this.params.orderKey;
                });
            	if(!!find){
            		return find.label;
                }
            }
        },
        components:{
            tree:require('../../../components/tree.vue').default,
            labelItem:require('../../../components/label-item.vue').default,
            selectRemote:require('../../../components/select-remote.vue').default,
            treeCategories:require('../../../components/tree-categories'),
            supplier:require('../../../api-components/supplier.vue').default,
            uiTip:require('../../../components/ui-tip.vue').default,
            searchCard:require('../../../components/search-card.vue').default,
            importExcel: require('./import-excel.vue').default,
            goodsClassify: require('../../../components/goods-classify.vue').default,
            goodsTree: require('../../../components/goods-tree.vue').default,
            trendsSelect:require('../../../components/trends-select').default,
            exportField:require("@/components/export-field").default,
            exportDialog:require('../../report/export-dialog').default,
            orderInput:require("@/components/order-input.vue").default,
        }
    }
</script>
