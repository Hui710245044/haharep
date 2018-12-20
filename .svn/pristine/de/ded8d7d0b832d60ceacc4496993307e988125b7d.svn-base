<template>
    <div class="c-table-data">
        <el-table
                class="scroll-bar"
                v-resize="{height:41}"
                :data="tableData.lists"
                v-loading="loading"
                element-loading-text="玩命加载中..."
                highlight-current-row
                @selection-change="handle_selection_change">
            <div slot="empty" class="no-data-reminder">
                <i></i>
                {{emptyText}}
            </div>

            <el-table-column
                    type="selection"
                    width="50">
            </el-table-column>

            <el-table-column label='规则名称' prop="rule_name">

            </el-table-column>

            <el-table-column label='规则有效期'>
                <template slot-scope="scope">
                    <span>{{scope.row.start_time}}至{{scope.row.end_time}}</span>
                </template>
            </el-table-column>

            <el-table-column label='是否启用'>
                <template slot-scope="scope">
                    <el-switch v-model="scope.row.status === 0 ? true : false"
                               onText="启用"
                               offText="停用"
                               @change="change_status(scope.row)">
                    </el-switch>
                </template>
            </el-table-column>

            <el-table-column label='创建人' prop="username"></el-table-column>

            <el-table-column label='创建时间'>
                <template slot-scope="scope">
                    <span>{{scope.row.created_time}}</span>
                </template>
            </el-table-column>

            <el-table-column label="操作">
                <template slot-scope="scope">
                    <el-button type="text" @click.native="edit(scope.row)">编辑</el-button>
                    <span> | </span>
                    <el-button type="text" @click.native="delete_rule(scope.row)">删除</el-button>
                </template>
            </el-table-column>
        </el-table>
        <el-pagination
                class="page-fixed"
                @size-change="size_change"
                @current-change="current_change"
                :current-page="tableData.page"
                :page-sizes="[20, 50, 100, 200,500]"
                :page-size=tableData.pageSize
                layout="total, sizes, prev, pager, next, jumper"
                :total="tableData.total">
        </el-pagination>
    </div>
</template>
<style lang="stylus">

</style>
<script>

    export default {
        data() {
            return {}
        },
        methods:{
            edit(row){//编辑
                this.$emit('edit',row)
            },
            delete_rule(row){//删除
                this.$emit('delete-rule',row)
            },
            change_status(row){
                this.$emit('change-status', row)
            },
            handle_selection_change(val) {
                this.$emit('handle-selection-change',val)
            },
            size_change(size){
                this.$emit('size-change',size);
            },
            current_change(page){
                this.$emit('current-change',page);
            },
        },
        computed:{
            emptyText() {
                return this.firstLoading?'等待查询数据中...':'暂无数据'
            }
        },
        props:{
            tableData:{
                type:Object,
                required:true,
            },
            loading:{
                type:Boolean,
                required:true,
            },
            firstLoading:{
                type:Boolean,
                required:true,
            }
        },
        components: {}
    }
</script>