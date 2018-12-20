<template>
    <page-dialog title="操作日志" v-model="visible" size="large" :close-on-click-modal="false">
        <el-table :data="tableData" border highlight-current-row class="scroll-bar">
            <el-table-column label="操作人">
                <template slot-scope="scope">
                    <span>{{scope.row.user && scope.row.user.realname}}</span>
                </template>
            </el-table-column>
            <el-table-column label="操作时间">
                <template slot-scope="scope">
                    <span>{{scope.row.create_time}}</span>
                </template>
            </el-table-column>
            <el-table-column label="操作状态">
                <template slot-scope="scope">
                    <span>{{scope.row.status}}</span>
                </template>
            </el-table-column>
            <el-table-column label="操作事项">
                <template slot-scope="scope">
                    <span v-html="scope.row.log"></span>
                </template>
            </el-table-column>
            <el-table-column label="失败原因">
                <template slot-scope="scope">
                    <span>{{scope.row.message}}</span>
                </template>
            </el-table-column>
        </el-table>

        <div class="log_page">
            <el-pagination
                @size-change="handle_size_change"
                @current-change="handle_current_change"
                :current-page=searchData.page
                :page-sizes="[20, 50,100, 200, 500]"
                :page-size=searchData.pageSize
                layout="total, sizes, prev, pager, next, jumper"
                :total=total>
            </el-pagination>
        </div>
        <div slot="footer">
            <el-button size="mini" @click="visible=false">关闭</el-button>
        </div>

    </page-dialog>
</template>

<style lang="stylus">
    .log_page{
        text-align: right;
    }

</style>
<script>
    import {api_amazon_logs} from '../../../../api/amazon-publish-list';
    export default{
        data(){
            return {
                visible: false,
                searchData:{
                    page:1,
                    pageSize:50,
                    amazon_listing_id:''
                },
                total:0,
                tableData:[]
            }
        },
        created(){

        },
        filters: {},
        mounted(){

        },
        updated(){

        },
        destroy(){

        },
        methods: {
            init(){

                this.searchData.amazon_listing_id = this.amazonListingId;
                this.$http(api_amazon_logs, this.searchData).then(res=>{
                    this.tableData = res.data;
                    this.total = res.count;
                })
            },
            handle_size_change(val){
                this.searchData.pageSize = val;
                this.init();
            },
            handle_current_change(val){
                this.searchData.page = val;
                this.init();
            }
        },
        computed: {},
        watch: {
            value(val){
                this.visible = val;
                if(val){
                    this.init();
                }
            },
            visible(val){
                this.$emit('input', val)
            }
        },
        props: {
            value:{},
            amazonListingId:{
                require:true
            }
        },
        components: {
            pageDialog: require('../../../../components/page-dialog.vue').default
        }
    }
</script>

