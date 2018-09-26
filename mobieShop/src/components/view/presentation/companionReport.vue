<template>
    <div class='contain' v-touch:left="onSwipeLeft">
        <div class="box">
            <div class="title">
                <p>业主信息</p>
            </div>
            <div class="text">
                <span>业主姓名：{{dataInfo.name}}</span>
                <span>业主电话：{{dataInfo.orderDetail.phone}}</span>
                <div><span>验收地址：</span><p> {{dataInfo.orderDetail.detailAddress}}</p></div>
            </div>
        </div>
         <div class="box">
            <div class="title">
                <p>管家信息</p>
            </div>
            <div class="text">
                <span>管家姓名：{{dataInfo.workList.name}}</span>
                <span>管家电话：{{dataInfo.workList.phone}}</span>
                <div><span>验收时间：</span><p>{{dataInfo.workList.createTime}}</p></div>
            </div>
        </div>
        <div class="box" v-if="datalist.additmes">
            <div class="title">
                <p>存在增项项目</p>
            </div>
            <div class="project"  v-if="datalist.additmes.hydropower.length">
                <span>水电工程增项</span>
                <p v-for="item in datalist.additmes.hydropower"><i class="icon iconfont icon-gou"></i>{{item}}</p>
            </div>
            <div class="project" v-if="datalist.additmes.tiler.length">
                <span>泥工工程增项</span>
                <p v-for="item in datalist.additmes.tiler"><i class="icon iconfont icon-gou"></i>{{item}}</p>
            </div>
            <div class="project" v-if="datalist.additmes.waterproof.length">
                <span>防水工程增项</span>
                <p v-for="item in datalist.additmes.waterproof"><i class="icon iconfont icon-gou"></i>{{item}}</p>
            </div>
            <div class="project" v-if="datalist.additmes.carpentry.length">
                <span>木作工程增项</span>
                <p v-for="item in datalist.additmes.carpentry"><i class="icon iconfont icon-gou"></i>{{item}}</p>
            </div>
            <div class="project" v-if="datalist.additmes.paint.length">
                <span>油漆工程增项</span>
                <p v-for="item in datalist.additmes.paint"><i class="icon iconfont icon-gou"></i>{{item}}</p>
            </div>
            <div class="project" v-if="datalist.additmes.rest.length">
                <span>其他工程增项</span>
                <p v-for="item in datalist.additmes.rest"><i class="icon iconfont icon-gou"></i>{{item}}</p>
            </div>
            <div class="project">
                <span>备注</span>
                <p>{{datalist.additmes.remark}}</p>
            </div>
        </div>
        <div class="box" v-if="datalist.materials">
            <div class="title">
                <p>材料工艺项目</p>
            </div>
            <div class="project">
                <p class="pr">1.存在铺贴方式的增项（拼花、斜铺、倒角等) <span class="istrue"><i class="icon iconfont icon-AppIcon"></i>{{datalist.materials.manual==true? '部分':'有'}}</span></p>
                <p class="pr">2.存在原始地面找平费用——非实木地板基础找  <span class="istrue"><i class="icon iconfont icon-AppIcon"></i>{{datalist.materials.detailed==true? '部分':'有'}}</span></p>
            </div>
            <div class="project">
                <span>备注</span>
                <p>{{datalist.materials.rest}}</p>
            </div>
        </div>
        <div class="box" v-if="datalist.suggest">
            <div class="title">
                <p>施工图陪签建议</p>
            </div>
            <div class="project">
                <span>设计图组成齐全度</span>
                <div class="flex">
                    <p v-for="item in datalist.suggest.drawing"><i class="icon iconfont icon-gou"></i>{{item}}</p>
                </div>
            </div>
            <div class="project">
                <span>备注</span>
                <p>{{datalist.suggest.rest}}</p>
            </div>
        </div>
        <div class="box" v-if="datalist.contract">
            <div class="title">
                <p>合同记录</p>
            </div>
            <div class="project" >
                <span>1.合同是否为杭州推荐合同</span>
                <p><i class="icon iconfont icon-AppIcon"></i>{{datalist.contract.payment==true?'自印版':'杭州市推荐版本'}}</p>
            </div>
            <div class="project" >
                <span>2.合同付款方式</span>
                <p><i class="icon iconfont icon-AppIcon"></i>{{datalist.contract.payment}}</p>
            </div>
            <div class="project" >
                <span>2.工期约定</span>
                <p><i class="icon iconfont icon-AppIcon"></i>{{datalist.contract.duration}}</p>
            </div>
            <div class="project">
                <span>备注</span>
                <p>{{datalist.contract.rest}}</p>
            </div>
        </div>
        <div class="box" v-if="datalist.matter">
            <div class="title">
                <p>其他注意事项及疑义</p>
            </div>
            <div class="project">
                <p>{{datalist.matter.remark}}</p>
            </div>
        </div>
        <div class="box" v-if="datalist.contract">
            <div class="title">
                <p>温馨提示</p>
            </div>
            <div class="project" >
                <span>1.合同常规提醒</span>
                <p>①目前杭州市场一般付款方式是4-3-3（最后的30%如果可能最好约定个尾款，一般为5%在安装前/后支付）。</p>
                <p>②约定延期违约金，目前违约金常规一般在50-150元/天（也有装修公司会按百分比计算，建议业主采取对自己有利的方式）；</p>
                <p>③建议在业主不主动要求增加项目且装修公司无恶意漏项下，约定增项不超过总金额的3%-5%；</p>
                <p>④合同须约定如果产生增项，必须业主签字同意；</p>
                <p>⑤如果合同前有优惠承诺，需将优惠条目写入合同附加条款（或作为附件）。</p>
            </div>
            <div class="project" >
                <span>2.温馨提醒</span>
                <p>您的线下陪签服务已完毕，如有疑问可以咨询您的陪签管家，需线下监理服务可以直接微信公众号或淘宝查找“绿城装修管家”下单，已购买线下监理服务的业主需提前2-3天预约，以便管家合理安排时间，及时为您服务，联系电话：</p>
            </div>
            
        </div>
        <div class="logo"></div>
    </div>
</template>
<script>
export default {
    data(){
        return {
            dataInfo:'',
            datalist:''
        }   
    },
    created(){
        //设置头部文字
        var that=this
        this.$root.$emit('header', '装修管家报告中心');
        var data=sessionStorage.getItem("presentationInfo")
        data=JSON.parse(data)
        this.dataInfo=data
        let id=this.$route.query.id
        this.$http({
            url: "/api/public/entrysignadditmes/queryByIdss?judge=false",
            method: "post",
            data:[id],
        }).then((res) => {
            var data=res.data.info
            console.log(data)
            var list={}
            var additmes={}  //增项数据
            additmes.remark=data.EntrySignAdditmes.list[0].remark
            additmes.hydropower=this.listSet(data.EntrySignAdditmes.list[0].hydropower)
            additmes.tiler=this.listSet(data.EntrySignAdditmes.list[0].tiler)
            additmes.paint=this.listSet(data.EntrySignAdditmes.list[0].paint)
            additmes.waterproof=this.listSet(data.EntrySignAdditmes.list[0].waterproof)
            additmes.carpentry=this.listSet(data.EntrySignAdditmes.list[0].carpentry)
            additmes.rest=this.listSet(data.EntrySignAdditmes.list[0].rest)
            list.additmes=additmes
            list.materials=data.EntrySignMaterials.list[0]
            list.suggest=data.EntrySignSuggest.list[0]
            list.suggest.drawing=this.listSet(list.suggest.drawing)
            list.contract=data.EntrySignContract.list[0]
            list.matter=data.EntrySignMatter.list[0]
            console.log(list)
            that.datalist=list
        });
    },
    methods:{
        onSwipeLeft(){ //左滑上一页
            this.$router.go(-1)
        },
        listSet(data){   //将字符串数据转换成数组
            var arr=data.split('+')
            arr.pop()
            return arr
        }
    }
}
</script>
<style scoped>
.contain{
    overflow: hidden;
    background-color:#f5f5f5; 
}
.box{
    width: 100%;
    box-sizing: border-box;
    margin-bottom: 0.15rem;
    overflow: hidden;
    background: #ffffff;
    text-align: left;
}
.box .title{
    width: 100%;
    color: #333;
    border-bottom: 0.01rem solid #e6e6e6;
    padding:0.23rem 0 0.23rem 0.33rem;
}
.box .title p{
    border-left:0.06rem solid #11b786;
    padding-left:  0.09rem;
    font-size: 0.28rem;
    text-align: left
}
.box .text{
    line-height: 0.35rem;
    color: #666666;
    font-size: 0.26rem;
    padding: 0.1rem 0;
}
.box .text>span{
    margin-left: 0.45rem;
    padding: 0.1rem 0;
    display: block;
    width: 3.2rem;
    float: left;
}
.box .text div>span{
    display: block;
    float: left;
}
.box .text div{
    clear: both;
    margin-left: 0.45rem;
    padding: 0.1rem 0;
    overflow: hidden;
}
.box .text div p{
    width: 5.54rem;
    float: left;
}
.project{
    font-size: 0.26rem;
    padding: 0.2rem 0.45rem;
    box-sizing: border-box;
    line-height: 0.4rem;
    border-bottom: 0.01rem solid #e6e6e6;
}
.project i{
    font-size: 0.2rem;
    margin-right:0.1rem;
}
.project p{
    width: 98%;
    margin: 0 auto;
    position: relative;
}
.project .pr{
    box-sizing: border-box;
    padding-right: 1rem;
}
.logo{
    width: 100%;
    height: 1rem;
    background-image: url('../../../../static/images/logoB.png');
    background-size: 2.97rem 0.48rem;
    background-position: center;
    background-repeat: no-repeat;
}
.istrue{
    position: absolute;
    width: 0.9rem;
    display: block;
    text-align: left;
    right: 0px;
    top: 0px;
}
.flex{
    overflow: hidden;
}
.flex p{
    display:block;
    float: left;
    box-sizing: border-box;
    padding-left: 0.25rem;
    width:48%;
}
</style>