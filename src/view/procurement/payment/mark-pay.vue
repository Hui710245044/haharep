<template>
    <div class="mark-pay-class">
        <page-dialog v-model="show" :title="title" width="80%" @close="close">
            <el-table border
                      :data="checkList"
                      style="width: 100%;">
                <el-table-column width="100" label="付款申请单号" prop="pay">
                </el-table-column>
                <el-table-column label="供应商" width="150" inline-template>
                    <div class="p-table-list-td-text" :title="row.super">{{row.super}}</div>
                </el-table-column>
                <el-table-column label="结算方式" width="140" prop="type">
                </el-table-column>
                <el-table-column label="申请时间" prop="time" width="140">
                </el-table-column>
                <el-table-column label="最迟付款时间" width="120" inline-template>
                    <div :class="{'red-color': hasError}">{{row.late | dateFilter}}</div>
                </el-table-column>
                <el-table-column label="币种" width="60" prop="status">
                </el-table-column>
                <el-table-column label="申请付款总额" prop="currency" width="130">
                </el-table-column>
                <el-table-column label="已付款金额" width="" prop="dopay" width="130">
                </el-table-column>
                <el-table-column label="本次支付最大金额" width="150" inline-template>
                    <div>{{row.bigpay}}</div>
                </el-table-column>
                <el-table-column label="本次付款金额" inline-template>
                    <div class="deal-center-pay">
                        <ui-limit-number v-model="row.thispay"
                                         style="width:130px;margin-left:51px;"
                                         @blur="update_price(row)"
                                         :limit="3"
                                         :min="0"
                                         :class="{'limit-input': Number(row.thispay) > Number(row.bigpay)}">
                        </ui-limit-number>
                        <div v-if="Number(row.thispay) > Number(row.bigpay)" class="red">本次付款金额不可大于本次支付最大金额</div>
                    </div>
                </el-table-column>
            </el-table>
            <div slot="footer">
                <el-button size="mini" type="primary" @click.native="sure_pay">确认付款</el-button>
                <el-button size="mini" @click.native="show = false">关闭</el-button>
            </div>
        </page-dialog>
    </div>
</template>

<style lang="stylus">
    .mark-pay-class{
        .red-color{
            color: #f00;
        }
        .p-table-list-td-text{
            white-space: nowrap;
            overflow: hidden;
            text-overflow: ellipsis;
        }
        .limit-input{
            .el-input__inner{
                border: 1px solid #f00;
            }
        }
        .deal-center-pay{
            display: flex;
            justify-content: center;
            flex-direction: column;
            padding: 5px 5px;
        }
    }
</style>

<script>
    export default {
        data(){
            return{
                // hasError: this.checkList.late > 0,
                show: false,
            }
        },
        computed: {
            hasError(){
                if(Number(this.checkList.late) - parseInt(new Date()/1000) < 24*60*60*7){
                    return true;
                }else{
                    return false;
                }
            }
        },
        watch: {
            show(val){
                this.$emit('input', val);
            },
            value(val){
                this.show = val;
            }
        },
        methods: {
            close(){
                this.checkList.thispay = '';
                console.log('sd',this.checkList.thispay);
            },
            sure_pay(){

            },
            update_price(row){
                if(row.thispay < 0){
                    row.thispay = 0;
                }
                row.thispay = Number((row.thispay * 1000) / 1000).toFixed(3);
            }
        },
        filters: {
            dateFilter(val){
               return datef('YYYY-MM-dd', val);
            }
        },
        props: {
            value: {},
            checkList: {
                required: true,
                type: [Array,Object]
            },
            title:{
                required: true,
                type: String,
            },
        },
        components: {
            pageDialog:require('../../../components/page-dialog.vue').default,
            uiLimitNumber:require('../../../components/ui-limit-number').default,

        }
    }
</script>

