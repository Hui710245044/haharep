<template>
    <div class="c-published-draft-search">
        <search-card @search="search_list" @enter="search_list"
                     :params="searchData" :clears="clearsData">
            <label-item label="">
                <el-select v-model="searchData.type" class="s-width-small mr-xs"
                           v-sf.type default-first-option filterable clearable>
                    <el-option
                            v-for="res in typeList"
                            :label="res.label"
                            :value="res.value"
                            :key="res.value"
                    ></el-option>
                </el-select>
                <el-input class='width-super' v-sf.content v-model="searchData.content"></el-input>
            </label-item>
            <label-item class="ml-sm" label="账号简称：">
                <el-select v-model="searchData.account_id" class="s-width-small"
                           v-sf.account_id default-first-option filterable clearable>
                    <el-option
                            v-for="res in accountList"
                            :label="res.account_name"
                            :value="res.account_id"
                            :key="res.account_id"
                    ></el-option>
                </el-select>
            </label-item>
            <label-item class="ml-sm mr-sm" label="时间：">
                <el-date-picker
                        v-sf.time_start
                        v-model="searchData.time_start"
                        class="date" type="date"
                        placeholder="开始日期">
                </el-date-picker>
                <span>&nbsp;--&nbsp;</span>
                <el-date-picker
                        v-sf.time_end
                        v-model="searchData.time_end"
                        class="date" type="date"
                        placeholder="结束日期">
                </el-date-picker>
            </label-item>

        </search-card>
    </div>
</template>
<style lang="stylus">

</style>
<script>

    export default {
        data() {
            return {
                typeList:[
                    {label:'本地SPU',value:'spu'},
                    {label:'本地SKU',value:'sku'},
                    {label:'平台SKU',value:'publish_sku'},
                    {label:'刊登标题',value:'title'},
                    {label:'UPC',value:'upc'},
                ],
            }
        },
        methods:{
            search_list(){
                this.$emit('search-list');
            },

        },
        props:{
            searchData:{
                type:Object,
                required:true
            },
            clearsData:{
                type:Object,
                required:true
            },
            accountList:{
                type:Array,
                required:true
            }
        },
        components: {
            searchCard:require('@/components/search-card.vue').default,
            labelItem:require('@/components/label-item.vue').default,
        }
    }
</script>