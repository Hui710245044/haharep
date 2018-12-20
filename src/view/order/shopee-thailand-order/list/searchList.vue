<template>
    <el-row class="wish-searchList">
        <search-card @search="search_list" :params="searchData" :clears="clears">
            <el-row class="mb-xs">
                <label>站点：</label>
                <el-select v-model="searchData.site"
                           v-sf.site
                           class="inline mr-sm width-sm"
                           filterable>
                    <el-option
                        :key="item.value"
                        v-for="item in selectList"
                        :label="item.label"
                        :value="item.value">
                    </el-option>
                </el-select>
                <label>账号简称：</label>
                <el-select v-model="searchData.account_id"
                           v-sf.account_id
                           class="inline mr-sm width-sm"
                           filterable clearable>
                    <el-option
                        :key="item.value"
                        v-for="item in accoutOptions"
                        :label="item.label"
                        :value="item.value">
                    </el-option>
                </el-select>
                <label>付款状态：</label>
                <el-select v-model="searchData.pay_status"
                           v-sf.pay_status
                           class="inline mr-sm width-sm">
                    <el-option
                        :key="item.value"
                        v-for="item in pay_status"
                        :label="item.label"
                        :value="item.value">
                    </el-option>
                </el-select>
                <label>发货状态：</label>
                <el-select v-model="searchData.delivery_status"
                           v-sf.delivery_status
                           class="inline mr-sm width-sm">
                    <el-option
                        :key="item.value"
                        v-for="item in delivery_status"
                        :label="item.label"
                        :value="item.value">
                    </el-option>
                </el-select>
                <el-select v-model="searchData.snType"
                           v-sf.snType
                           class="inline width-sm">
                    <el-option
                        :key="item.value"
                        v-for="item in snTypeList"
                        :label="item.label"
                        :value="item.value">
                    </el-option>
                </el-select>
                <order-input v-model="searchData.snText"
                             class="inline width-super mr-sm"
                             v-sf.snText
                             @keydown="search_list"
                             placeholder="可批量搜索，Shift+回车换行...">
                </order-input>
                <label>销售员：</label>
                <param-account
                    class="inline s-width-small"
                    ref="paramSale"
                    placeholder="选择/搜索"
                    v-model="searchData.seller_id"
                    v-sf.seller_id
                    :param="{type:'sales',data:{content:''}}"
                    :fixUrl="true"
                    :fixResult="sale_fix_result"
                    type="memberShipSales"
                    url="get|user/:type/staffs">
                </param-account>
            </el-row>
                <el-select v-model="searchData.select_time"
                           v-sf.select_time
                           class="inline width-sm">
                    <el-option
                        :key="item.value"
                        v-for="item in select_time"
                        :label="item.label"
                        :value="item.value">
                    </el-option>
                </el-select>
                <el-date-picker
                    @keyup.enter.native="search_list"
                    type="date"
                    placeholder="开始时间"
                    v-model="searchData.date_b"
                    v-sf.date_b
                    class="inline"
                    :picker-options="pickerOptions"
                    style="width:140px">
                </el-date-picker>&nbsp;--
                <el-date-picker type="date"
                                @keyup.enter.native="search_list"
                                placeholder="结束时间"
                                v-model="searchData.date_e"
                                v-sf.date_e
                                :picker-options="pickerOptions1"
                                class="inline mr-sm"
                                style="width:140px">
                </el-date-picker>
        </search-card>
    </el-row>
</template>
<style lang="stylus">
    .wish-searchList{
        .el-card{
            overflow: inherit;
        }
    }
</style>
<script>
    import {api_account_list} from '../../../../api/order-local';
    export default{
        data(){
            return {
                accoutOptions:[{label:"",value:""}],
                isBatch:false,
                pay_status:[
                    {label: "全部", value: ''},
                    {label: "已付款", value: 'pay'},
                    {label: "未付款", value: 'no_pay'},
                ],
                delivery_status:[
                    {label: "全部", value: ''},
                    {label: "已发货", value: 'fahuo'},
                    {label: "未发货", value: 'meiyou'},
                ],
                select_time: [
                    {id:1,label:"下单时间",value:""},
                    {id:2,label:"支付时间",value:"pay_time"},
                ],
                snTypeList:[
                    {id:1,label:"订单号",value:"order_id"},
                    {id:2,label:"平台订单号",value:""},
                    {id:3,label:"买家ID",value:"name"},
                    {id:4,label:"目的地",value:"phone"},
                    {id:5,label:"追踪单号",value:"tracking_number"},
                    {id:6,label:"sku",value:"sku"}
                ],
                selectList:[],
                pickerOptions:{
                    disabledDate:(time)=>{
                        return time.getTime() >new Date()
                    }
                },
                pickerOptions1:{
                    disabledDate: (time) => {
                        return time.getTime() > new Date().getTime() || time.getTime() < this.searchData.date_b;
                    }
                }
            }
        },
        mounted(){
            this.getAccount();
        },
        methods:{
            sale_fix_result(res){
                return  res.filter(row=>row.job==='sales').map(row=>({
                    value:row.id,
                    label:row.realname
                }))
            },
            getAccount(){
                this.$http(api_account_list,{channel_id:19}).then(res=>{
                    this.selectList = [{label:'全部',value:''},...res.account];
                }).catch(code=>{this.$message({message:code.message||code,type:'error'})})
            },
            search_list(){
                this.$emit('search_list')
            },
        },
        props:{
            searchData:{
                required:true,
                type:Object
            },
            clears:{
                required:true,
                type:Object
            }
        },
        components: {
            searchCard:require('../../../../components/search-card.vue').default,
            orderInput:require('../../../../components/order-input.vue').default,
            paramAccount:require('../../../../api-components/param-account.vue').default,
        }
    }
</script>
