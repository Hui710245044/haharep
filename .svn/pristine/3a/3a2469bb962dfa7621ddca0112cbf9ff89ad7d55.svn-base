<template>
    <div :class="['c-price-tip',rowData.adjusted_price===1?'red-background':'green-background']">
        {{rowData.adjusted_price===1?'涨价':'降价'}} = {{rowData.current_cost}}
    </div>
</template>
<style lang="stylus">
    .c-price-tip{
        padding:2px;
        border:1px solid #ddd;
        color:#fff;
    }
    .red-background{
        background-color:red;
    }
    .green-background{
        background-color:green;
    }
</style>
<script>

    export default {
        data() {
            return {}
        },
        props:{
            rowData:{
                required:true,
                type:Object
            }
        },
        components: {}
    }
</script>