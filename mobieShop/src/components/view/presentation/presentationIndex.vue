<template>
    <div class='contain' v-touch:left="onSwipeLeft">
        <div class="header">
            <div class="userInfo">
                <img src="../../../../static/images/header.png" alt="">
                <div class="text">
                    <p><i class="icon iconfont icon-yonghu"></i>{{datainfo[0].name}}</p>
                    <p><i class="icon iconfont icon-weibiaoti"></i>{{datainfo[0].orderDetail.phone}}</p>
                </div>
            </div>
        </div>
        <div class="title">
            <i class="icon iconfont icon-book1"></i>
            <span>报告列表</span>
        </div>
        <div class="content">
            <img v-for="(item,index) in datainfo" v-if="item.orderDetail.serviceStateName=='陪签'" src="../../../../static/images/presentation1.png" alt="" @click="companion(index)">
            <img v-for="(item,index) in datainfo" v-if="item.orderDetail.serviceStateName=='决算'" src="../../../../static/images/presentation2.png" alt="" @click="finalAccounts(index)">
            <img v-for="(item,index) in datainfo" v-if="item.orderDetail.serviceStateName=='监理'" src="../../../../static/images/presentation3.png" alt="" @click="supervisor(index)">
            <img v-for="(item,index) in datainfo" v-if="item.orderDetail.serviceStateName=='规划'" src="../../../../static/images/presentation4.png" alt="">
        </div>
    </div>
</template>
<script>
export default {
    data(){
        return {
           appid:'',
           datainfo:'',
        }
    },
    created(){
        //设置头部文字
        this.$root.$emit('header', '装修管家报告中心');
        var that=this
        // alert(this.$route.query.openId)
        //请求报告数据
        var phone=sessionStorage.getItem('phone')
        console.log(phone)
        this.$http({
            url: "/api/product/ProjectEstablish/queryListByOrderInfoPhone?phone="+phone,
            method: "post",
            data:{},
        }).then((res) => {
            console.log(res.data.info)
            if(res.data.status==200){
                that.datainfo=res.data.info
            }
        });
    },
    methods:{
        onSwipeLeft(){ //左滑上一页
            this.$router.go(-1)
        },
        supervisor(index){  //跳转到监理报告列表
            let id=this.datainfo[index].id
            sessionStorage.setItem('presentationInfo',JSON.stringify(this.datainfo[index]))  
            this.$router.push('/supervisorList?company=92&id='+id);
        },
        companion(index){  //跳转到陪签报告
            let id=this.datainfo[index].id
            sessionStorage.setItem('presentationInfo',JSON.stringify(this.datainfo[index]))
            this.$router.push('/companionReport?company=92&id='+id);
        },
        finalAccounts(index){ //跳转到决算报告
            let id=this.datainfo[index].id
            sessionStorage.setItem('presentationInfo',JSON.stringify(this.datainfo[index]))
            this.$router.push('/finalAccounts?company=92&id='+id);
        },
        
    }
}
</script>
<style scoped>

.contain{
    height:100vh;
    background-color:#fff; 
}
.header{
    width: 100%;
    height: 2.2rem;
    background-image: url('../../../../static/images/indexTopbg.png');
    background-size: cover;
    overflow: hidden;
}
.userInfo{
    width: 4.1rem;
    margin-left: 0.78rem;
    margin-top:0.4rem;
    text-align: left
}
.userInfo img{
    width: 1.5rem;
    height: 1.5rem;
    float: left;
}
.userInfo .text{
    float: left;
    font-size: 0.28rem;
    line-height: 0.56rem;
    color: #fff;
    margin-left: 0.3rem;
    padding-top: 0.2rem;
}
.userInfo .text i{
    margin-right: 0.1rem;
}
.title{
    width: 100%;
    height: 0.83rem;
    border-bottom: 0.02rem solid #e4e4e4;
    line-height: 0.83rem;
    box-sizing: border-box;
    padding-left: 0.27rem;
    font-size: 0.28rem;
    color: #333;
    text-align: left;
}
.title i{
    margin-right: 0.1rem;
    float: left;
    color: #11b786;
    font-size: 0.42rem;
}
.content{
    width: 93%;
    padding: 0.18rem;
    overflow: hidden;
    margin: 0 auto;
}
.content img{
    display: block;
    border-radius: 0.12rem;
    width: 100%;
    box-shadow: 0 0 0.1rem 0.01rem #006446;
    margin-top: 0.25rem;
}
</style>


