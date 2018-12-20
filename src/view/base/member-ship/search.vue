<template>
    <div class="p-search-member">
        <search-card @search="search" @enter='search' :params="searchData" :clears="clears">
                <label-item label="平台：">
                    <el-select v.sf.channel_id v-model="searchData.channel_id"
                               class="inline s-width-default"
                               @change="change_channelid">
                        <el-option
                                v-for="item in channels"
                                :key="item.value"
                                :label="item.label"
                                :value="item.value">
                        </el-option>
                    </el-select>
                </label-item>
                <label-item label="账号简称：" class="ml-sm">
                    <el-select v-model="searchData.account_type" v-sf.searchType
                               @change="change_search_type"
                               class="inline s-width-mini"
                               default-first-option>
                        <el-option
                                v-for="res in searchTypeList"
                                :label="res.label"
                                :value="res.value"
                                :key="res.value"
                        ></el-option>
                    </el-select>
                    <param-account
                            v-if="searchData.account_type === 1"
                            class="s-width-large ml-mini"
                            ref="paramAccount"
                            :disabled="searchData.channel_id===''"
                            :placeholder="typeHolder"
                            v-sf.account_id
                            :refresh="true"
                            v-model="searchData.account_id"
                            :param="accountParam"
                            :fixResult="account_fix_result"
                            type="memberShipAccount"
                            url="get|orders/account">
                    </param-account>
                    <order-input v-model="searchData.account_code"
                                 v-if="searchData.account_type === 2&&searchData.channel_id !== ''"
                                 @keydown="search"
                                 class="inline s-width-large ml-mini"
                                 placeholder="空格分隔可批量搜索"></order-input>
                    <el-input disabled class="inline s-width-large ml-mini" v-if="searchData.account_type === 2&&searchData.channel_id === ''" placeholder="请先选择平台"></el-input>
                </label-item>
                <label-item label="" class="ml-sm">
                    <el-select v-sf.snType v-model="searchData.snType"
                               @change="change_type"
                               class="inline width-xs" >
                        <el-option
                                v-for="item in option"
                                :key="item.value"
                                :label="item.label"
                                :value="item.value">
                        </el-option>
                    </el-select>
                    <param-account
                            v-if="searchData.snType==='sales'"
                            class="s-width-large ml-mini"
                            ref="paramSale"
                            v-sf.snText
                            v-model="searchData.snText"
                            :param="{type:'sales',data:{content:''}}"
                            :fixUrl="true"
                            :fixResult="sale_fix_result"
                            type="memberShipSales"
                            url="get|user/:type/staffs">
                    </param-account>
                    <param-account
                            v-if="searchData.snType==='customer'"
                            class="s-width-large ml-mini"
                            ref="paramCustomer"
                            v-sf.snText
                            v-model="searchData.snText"
                            :param="{type:'customer',data:{content:''}}"
                            :fixUrl="true"
                            :fixResult="sale_fix_result"
                            type="memberShipCustomer"
                            url="get|user/:type/staffs">
                    </param-account>
                </label-item>
            <div class="mr-sm inline"></div>
        </search-card>

    </div>
</template>
<style lang="stylus">
    .p-search-member{
    .el-card{
        overflow: inherit;
    }
        .el-select__tags{
            height: 30px!important;
            width: 180px
            overflow-y: auto;
            >span{
                white-space: inherit;
            }
        }
    }
</style>
<script>
    import {api_channels,api_product_account} from "../../../api/member"
    import {api_product_person,} from "../../../api/product-class"
    export default{
        data(){
            return {
                channels:[],
                searchTypeList:[
                    {label:'选择',value: 1},
                    {label:'文本',value: 2},
                ],
                option:[{label:"销售员",value:"sales"},{label:"客服",value:"customer"}],
                accountParam:{
                    channel_id:'',
                },
                clears:{
                    snType:"sales",
                    snText:'',
                    channel_id:"",
                    account_type: 1,
                    account_id:'',
                    account_code: '',
                    page:1,
                    pageSize:50,
                },
                // refresh: true
            }
        },
        created(){
            this.$http(api_channels).then(res=>{
                this.channels=[{value:"",label:"全部"},...res];
            }).catch(code=>{
                console.log(code.message||code)
            });
        },
        computed: {
            isDisable(){
                if(this.searchData.channel_id){
                    return false
                }else {
                    return true
                }
            },
            typeHolder(){
                if(this.searchData.channel_id === ''){
                    return '请先选择平台'
                }else{
                    return '输入搜索更多'
                }
            }
        },
        methods: {
            change_search_type(){
                this.searchData.account_id = '';
                this.searchData.account_code = '';
            },
            account_fix_result(res){
                    return res.account
            },
            sale_fix_result(res){
            return  res.map(row=>{
                        return {
                            value:row.id,
                            label:row.realname
                        }
                    })
            },
            //过滤 账户
            change_type(){
                this.searchData.snText='';
                if(this.searchData.snType==='sales'){
                    this.$refs.paramSale.init();
                }else {
                    this.$refs.paramCustomer.init();
                }
            },
            change_channelid(){
                // this.refresh = true;
                this.searchData.account_id='';
                this.accountParam.channel_id=this.searchData.channel_id;
                this.$refs.paramAccount.init()
            },
            // change_refresh(){
            //     this.refresh = false
            // },
            search(){
                this.$emit("search");
            }
        },
        filters: {},
        watch: {},
        props: {
            searchData:{
                required:true,
                type:Object
            }
        },
        components: {
            paramAccount:require("../../../api-components/param-account.vue").default,
            searchCard:require('@/components/search-card.vue').default,
            orderInput:require("@/components/order-input.vue").default,
            labelItem:require("@/components/label-item.vue").default,
        }
    }
</script>
