<template>
    <div class="purchase-settlement-data-table">
        <!--<el-checkbox v-model="selectall">所有页全选</el-checkbox>-->
        <div class="mb-sm mt-sm">
            <permission tag="request-button" :route="apis.url_finance_pay"
                        class="ml-sm"
                        req-key="url_finance_pay"
                        :mintime="200"
                        :request="pay_all" :disabled="selects.length === 0 || paymentStatus==1">
                批量审核通过
            </permission>
            <permission tag="trendsSelect"
                        class="inline ml-xs"
                        type="primary"
                        :route="apis.url_purchase_apply_export"
                        :selects="partAllOptions"
                        @trigger="export_excel"></permission>
        </div>
        <el-table
            :data="tableData"
            v-loading="loading" element-loading-text="玩命加载中..."
            highlight-current-row
            v-resize="{height:41}"
            style="width: 100%"
            @selection-change="select_change">
            <div slot="empty" class="no-data-reminder">
                <i></i>
                {{emptyText}}
            </div>
            <el-table-column
                type="selection"
                width="35">
            </el-table-column>
            <el-table-column label="采购单号" min-width="60" inline-template>
                <div style="cursor: pointer;">
                    <permission tag="span" :route="apis.url_publish_look_detail" @click="look_(row)"
                                style="color: #69f;" title="点击可查看采购详情">{{row.purchase_order_code}}
                    </permission>
                </div>
            </el-table-column>
            <el-table-column label="外部流水号" prop="external_number"></el-table-column>
            <el-table-column label="采购员" min-width="40" prop="purchaser"></el-table-column>
            <el-table-column label="供应商" min-width="130" inline-template>
                <div style="cursor: pointer;">
                    <permission tag="span" :route="apis.url_look_supplier" @click="look_supplier(row)"
                                style="color: #69f;" title="点击可查看供应商详情">{{row.supplier}}
                    </permission>
                </div>
            </el-table-column>
            <el-table-column label="结算方式" min-width="50" inline-template>
                <span>{{row.balance_text}}</span>
            </el-table-column>
            <el-table-column label="采购单状态" min-width="65" inline-template>
                <span>{{row.purchase_order_status_text}}</span>
            </el-table-column>
            <el-table-column label="本次付款" min-width="45" inline-template>
                <div>{{row.currency_code}} {{row.apply_amount|filterAmount}}</div>
            </el-table-column>
            <el-table-column label="付款状态" min-width="45" inline-template>
                <span>{{row.payment_status_text}}</span>
            </el-table-column>
            <el-table-column label="采购类型" min-width="45" inline-template>
                <span>{{row.purchase_type|typeFilter}}</span>
            </el-table-column>
            <el-table-column label="申请时间" inline-template min-width="60" show-tooltip-when-overflow>
                <times :time="row.create_time"></times>
            </el-table-column>
            <el-table-column label="操作" inline-template v-if="paymentStatus==0">
                <div>
                    <permission tag="span" :route="apis.url_finance_pay" class="operate" @click="mark_pay(row)">审核通过
                    </permission>
                    <permission tag="span" :route="apis.url_finance_pay"> |</permission>
                    <permission tag="span" :route="apis.url_finance_pay" class="operate" @click="cancel_pay(row)">取消付款
                    </permission>
                </div>
            </el-table-column>
        </el-table>
        <look-list v-model="lookVisable" :lookData="lookData" :edit="false" :listId="listId"
                   @save-logistics="save_logistics"></look-list>
        <edit-lookover v-model="dialog" :isLook="true" :look-edit-form="lookEditForm"
                       :title="editLookTitle"></edit-lookover>
    </div>
</template>
<style lang="stylus">
    .purchase-settlement-data-table {
        .el-dropdown .el-button-group {
            display: inline-block;
            position: relative;
            top: -1px;
        }
        .box-label {
            box-sizing: border-box;
            width: 100%;
            color: #5e6d82;
            font-size: 1.25rem;
            padding: 0 21px 10px;
            display: inline-block;
            border-bottom: 2px solid #dee5ee;
            margin-bottom: 20px;
        }
        .el-table__body-wrapper {
            overflow-x: hidden;
            overflow-y: auto;
        }
    }
</style>
<script>
    import {
        api_cancel_pay,
        url_finance_list,
        url_purchase_apply_export,
        api_purchase_apply_export
    } from "../../../api/finance-purchase"
    import {api_finance_pay, url_finance_pay} from "../../../api/Payment"
    import {api_look_detail, url_publish_look_detail} from "../../../api/purchase"
    import {api_look_supplier, url_look_supplier} from "../../../api/assert-sup"
    import lookList from "../../procurement/purchase/look-list.vue"
    import editLookover from '../suppliers/assert-sup/edit-lookover.vue';
    import {downloadFile} from '../../../lib/http';

    export default {
        permission: {
            url_finance_pay,
            url_publish_look_detail,
            url_look_supplier,
            url_finance_list,
            url_purchase_apply_export
        },
        data() {
            return {
                selects: [],
                lookVisable: false,
                lookData: {},
                listId: 0,
                dialog: false,
                editLookTitle: '',
                lookEditForm: {},
                partAllOptions: [
                    {
                        name: "部分导出", value: 2, action: function () {
                            if (this.selects.length <= 0) return this.$message({
                                type: "warning",
                                message: "请先选择需要导出的数据"
                            });
                            let params = {
                                ids: JSON.stringify(this.selects.map(row => {
                                    if (typeof row === 'object') {
                                        return row.id
                                    } else {
                                        return row
                                    }
                                })),
                                export_type: 2,
                            };
                            Object.assign(params,this.init_params());
                            this.order_export(params);
                        }
                    },
                    {
                        name: "全部导出", value: 1, action: function () {
                            let params = this.init_params();
                            this.$set(params, 'export_type', 1);
                            this.order_export(params);
                        }
                    },
                ],
            }
        },
        methods: {
            order_export(params) {
                return this.$http(api_purchase_apply_export, params).then(res => {
                    if (res.status === 1) {
                        let params = {
                            page: this.searchData.page,
                            pageSize: this.searchData.pageSize,
                            file_code: res.file_code
                        };
                        let url = config.apiHost + 'downloadFile/downExportFile';
                        downloadFile({
                            url: url,
                            get: params,
                            fileName: res.file_name,
                            suffix: '.xls',
                        })
                    } else {
                        this.$message({type: 'error', message: res.message || res});
                    }
                    return Promise.resolve();
                }).catch(code => {
                    this.$message({type: "error", message: code.message || code});
                });
            },
            export_excel(val) {
                val.action.call(this, val);
            },
            init_params() {
                let params = window.clone(this.searchData);
                if (this.searchData.date_b) {
                    params.date_b = datef("YYYY-MM-dd", this.searchData.date_b / 1000)
                } else {
                    params.date_b = "";
                }
                if (this.searchData.date_e) {
                    params.date_e = datef("YYYY-MM-dd", this.searchData.date_e / 1000)
                } else {
                    params.date_e = "";
                }
                params.payment_status = this.paymentStatus;
                return params
            },
            //check 选择
            select_change(val) {
                this.selects = val;
            },
            //点击批量付款
            pay_all() {
                if (this.selects.length !== 0) {
                    let ids = this.selects.map(item => {
                        return item.id
                    });
                    let orderCode = this.selects.map(item => {
                        return item.purchase_order_id
                    });
                    orderCode = orderCode.join(',');
                    ids = ids.join(",");
                    return this.$confirm(`此操作为批量审核通过勾选的数据, 确认此操作吗?`, '提示', {
                        confirmButtonText: '确定',
                        cancelButtonText: '取消',
                        type: 'warning'
                    }).then(() => {
                        this.pay({id: ids, status: 1});
                        this.selects = [];
                    }).catch(() => {
                        this.$message({
                            type: 'info',
                            message: '已取消操作'
                        });
                    }).finally(() => {
                        setTimeout(() => {
                            this.$reqKey('url_finance_pay', false)
                        }, 200);
                    });
                } else {
                    this.$reqKey('url_finance_pay', false);
                }
            },
            cancel_pay(row) {//取消付款
                this.$confirm(`您将为采购单${row.purchase_order_code}取消付款, 确认此操作吗?`, '提示', {
                    confirmButtonText: '确定',
                    cancelButtonText: '取消',
                    type: 'warning'
                }).then(() => {
                    this.Nopay({id: row.id, status: 3}, row.id)
                }).catch(() => {
                    this.$message({
                        type: 'info',
                        message: '已取消操作'
                    });
                });
            },
            // 取消付款 的付款接口 api_cancel_pay
            Nopay(data) {
                return this.$http(api_cancel_pay, data).then(res => {
                    this.$message({
                        type: 'success',
                        message: res.message || res
                    });
                    this.$emit("reflash");
                    return Promise.resolve();
                }).catch(code => {
                    this.$message({
                        type: 'error',
                        message: code.message || code
                    })
                })
            },
            //审核通过
            mark_pay(row) {
                this.$confirm(`此操作为采购单${row.purchase_order_code}审核通过, 确认此操作吗?`, '提示', {
                    confirmButtonText: '确定',
                    cancelButtonText: '取消',
                    type: 'warning'
                }).then(() => {
                    this.pay({id: row.id, status: 1})
                }).catch(() => {
                    this.$message({
                        type: 'info',
                        message: '已取消审核'
                    });
                });
            },
            //付款接口
            pay(data) {
                return this.$http(api_finance_pay, data).then(res => {
                    this.$message({
                        type: 'success',
                        message: res.message || res
                    });
                    this.$emit("reflash");
                    return Promise.resolve();
                }).catch(code => {
                    this.$message({
                        type: 'error',
                        message: code.message || code
                    })
                })
            },
            save_logistics(id) {
                this.$http(api_look_detail, id).then(res => {
                    res.expect_arrive_date = datef('YYYY-MM-dd HH:mm:ss', res.expect_arrive_date)
                    this.lookData = res;
                }).catch(code => {
                    this.$message({
                        type: 'error',
                        message: code.message || code
                    })
                })
            },
            //点击采购单号 查看
            look_(row) {
                this.lookVisable = true;
                this.lookData = {};
                this.$set(this, "listId", row.purchase_order_id);
                this.$set(this.lookData, "shipping_cost", 0);
                this.$http(api_look_detail, row.purchase_order_id).then(res => {
                    res.expect_arrive_date = datef('YYYY-MM-dd HH:mm:ss', res.expect_arrive_date)
                    this.lookData = res;
                }).catch(code => {
                    this.$message({
                        type: 'error',
                        message: code.message || code
                    })
                })
            },
            /*------查看供应商*/
            look_supplier(row) {
                this.dialog = true;
                this.editLookTitle = `查看供应商: ${row.supplier} 信息`;
                this.$http(api_look_supplier, row.supplier_id).then(res => {
                    this.lookEditForm = res;
                }).catch(code => {
                    this.$message({message: code.message || code, type: 'error'});
                });
            }

        },
        filters: {
            statusFilter(val) {
                switch (val) {
                    case 0:
                        return "等待到货";
                        break;
                    case 1:
                        return "部分到货等待剩余";
                        break;
                    case 2:
                        return "部分到货不等待剩余";
                        break;
                    case 3:
                        return "全部到货";
                        break;
                    case 4:
                        return "已作废";
                        break;
                    case 5:
                        return "未申请付款";
                        break;
                    case 6:
                        return "已申请付款";
                        break;
                    case 7:
                        return "已取消付款";
                        break;
                    case 8:
                        return "已付款";
                        break;
                    case 9:
                        return "部分付款";
                        break;
                }
            },
            paymentFilter(val) {
                switch (val) {
                    case 0:
                        return "待审核";
                        break;
                    case 1:
                        return "已审核";
                        break;
                }
            },
            typeFilter(val) {
                switch (val) {
                    case 0:
                        return "样品";
                        break;
                    case 1:
                        return "采购";
                        break;
                    case 2:
                        return "补货";
                        break;
                }
            },
            dataFilter(val) {
                return datef('YYYY-MM-dd HH:mm:ss', val);
            },
            filterAmount(val){
                if(Number(val)>0){
                    return Number(val).toFixed(3);
                }
            }
        },
        computed: {
            emptyText() {
                return this.firstLoading?'等待请求数据中...':'暂无数据'
            }
        },
        watch: {},
        props: {
            tableData: {
                required: true,
                type: Array
            },
            paymentStatus: {},
            loading: {
                required: true,
                type: Boolean
            },
            searchData: {},
            firstLoading: {
                required: true,
                type: Boolean
            }
        },
        components: {
            lookList,
            editLookover,
            uiTips: require("../../../components/ui-tips.vue").default,
            times: require('../../../components/times.vue').default,
            trendsSelect: require('../../../components/trends-select').default,
        }
    }
</script>
