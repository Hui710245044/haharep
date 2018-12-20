import{api_pricing_rules}from '@/api/ebay-kandeng-public-module';
export const ref_price = function (skuList){
    let params = {
        site_code:this.form.list.site_code,
        category_id:this.form.list.primary_categoryid,
        account_id:this.accountId,
        channel_id:1,
        sku_id:skuList.filter(row=>!!row),
    };
    this.$http(api_pricing_rules,params).then(res=>{
        if(!res||(res instanceof Array)){
            this.showRef=false;
            this.$forceUpdate();
            return
        }
        this.showRef=true;
        for(let key in res){
            const find = this.form.reference.find(ref=>{
                return Number(ref.sku_id) === Number(key);
            });
            this.grossProfit = res[key].gross_profit;
            this.reduction=res[key].reduction;
            (!res[key].reduction)&&(!res[key].gross_profit)&&(this.showRef=false);
            if(!find){
                this.form.reference.push(Object.assign(res[key],{
                    channel_id:1,
                    account_id:this.accountId,
                    sku_id:key,
                }))
            }
        }
    }).catch(code=>{
        this.$message({type:"warning",message:code.message||code});
    })
}
export const get_reference = function(sku_id){
    if(!sku_id){
        return '';
    }
    return this.form.reference.find(ref=>{
        return ref.channel_id === 1
            && ref.account_id === this.accountId
            && Number(ref.sku_id) === Number(sku_id);
    });
};

export const caluc_reference_sale_price = function(row){
    let ref = null;
    if(ref = this.get_reference(row.sku_id)){
        return ref.total_price;
    }else{
        return ''
    }
};