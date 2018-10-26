<template>
    <div class='contain'>
        <div class="box">
            <div class="title">
                <p>业主信息</p>
            </div>
            <div class="text">
                <span>业主姓名：张珍珍</span>
                <span>业主电话：13512311485</span>
                <div><span>验收地址：</span><p> 杭州市西湖区某某街道某某小区XXF802杭州市西湖区某某街道某
              某小区XXF802</p></div>
            </div>
        </div>
         <div class="box">
            <div class="title">
                <p>管家信息</p>
            </div>
            <div class="text">
                <span>管家姓名：贺文志</span>
                <span>管家电话：13512311485</span>
                <div><span>验收时间：</span><p>2018-08-20 12:00</p></div>
            </div>
        </div>
        <div class="box">
            <div class="title">
                <p>报告项目</p>
            </div>
            <div class="project">
               <img src="../../../../static/images/icon2.png" alt="">
               <img src="../../../../static/images/icon2.png" alt="">
               <img src="../../../../static/images/icon2.png" alt="">
               <img src="../../../../static/images/icon2.png" alt="">
            </div>
        </div>
        <div class="box">
            <div class="title">
                <p>温馨提示</p>
            </div>
            <div class="project">
               <p>1.决算服务是家装竣工之后在原工程预算的基础上，结合工程运行过程中的实际情况，由业主与装修公司进行清算的方式，真实反映整个装修工程的最终造价。</p>
               <p>2.业主须知：乳胶漆、墙地保面积等有些复杂的面积按行规的方法计算，不规则的房间面积需要设计师提供CAD方可核算；核查的面积具体根据项目，允许存在一定的误差；现场核查的面积如因行业计算方式不同存在异议，以双方协商为准；涉及到最后决算的价格，审核人员只提供行业参照给予支持，并不能越权代理业主参与商讨及决定。</p>
               <p>3.您的阶段核算服务已完毕，如有疑问可以咨询您的专属管家，联系电话：95059999</p>
            </div>
        </div>
        
        <div class="logo"></div>
    </div>
</template>
<script>
export default {
    data(){
        return {
           
        }
    },
    created(){
        //设置头部文字
        this.$root.$emit('header', '决算报告');
        
    },
    methods:{
        // onSwipeRight(){ //左滑上一页
        //     this.$router.go(-1)
        // },
    }
}
</script>
<style scoped>
.contain{
    overflow: hidden;
    background-color:#f5f5f5; 
}
.contain .box{
    width: 100%;
    box-sizing: border-box;
    margin-bottom: 0.15rem;
    overflow: hidden;
    background: #ffffff;
    text-align: left;
}
.contain .box .title{
    width: 100%;
    color: #333;
    border-bottom: 0.01rem solid #e6e6e6;
    padding:0.23rem 0 0.23rem 0.33rem;
}
.contain .box .title p{
    border-left:0.06rem solid #11b786;
    padding-left:  0.09rem;
    font-size: 0.28rem;
    text-align: left
}
.contain .box .text{
    line-height: 0.35rem;
    color: #666666;
    font-size: 0.24rem;
    padding: 0.1rem 0;
}
.contain .box .text>span{
    margin-left: 0.45rem;
    padding: 0.1rem 0;
    display: block;
    width: 3.05rem;
    float: left;
}
.contain .box .text div>span{
    display: block;
    float: left;
}
.contain .box .text div{
    clear: both;
    margin-left: 0.45rem;
    padding: 0.1rem 0;
    overflow: hidden;
}
.contain .box .text div p{
    width: 5.54rem;
    float: left;
}
.contain .project{
    font-size: 0.24rem;
    padding: 0.2rem 0.45rem;
    box-sizing: border-box;
    line-height: 0.4rem;
    border-bottom: 0.01rem solid #e6e6e6;
    overflow: hidden;
}
.contain .project p{
    margin-bottom: 0.26rem;
}
.contain .project img{
    width: 46%;
    float: left;
    margin: 0.1rem 1%;
}

.contain .logo{
    width: 100%;
    height: 1rem;
    background-image: url('../../../../static/images/logoB.png');
    background-size: 2.97rem 0.48rem;
    background-position: center;
    background-repeat: no-repeat;
}
</style>


