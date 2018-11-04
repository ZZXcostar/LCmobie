<template>
    <div class='contain' >
        <div class="header">
            <div class="userInfo">
                <img src="../../../../static/images/header.png" alt="">
                <div class="text">
                    <p><i class="icon iconfont icon-yonghu"></i>{{userInfo.nickname==null? name : userInfo.nickname}}</p>
                    <p><i class="icon iconfont icon-weibiaoti"></i>{{userInfo.mobile}}</p>
                </div>
            </div>
        </div>
        <div class="title">
            <i class="icon iconfont icon-book1"></i>
            <span>报告列表</span>
        </div>
        <div class="content">
            <img v-for="(item,index) in datainfo" v-if="item.orderDetail.categoryName=='陪签'" src="../../../../static/images/presentation1.png" :alt="item.orderDetail.commodityName" @click="companion(index)">
            <img v-for="(item,index) in datainfo" v-if="item.orderDetail.categoryName=='决算服务'" src="../../../../static/images/presentation2.png" :alt="item.orderDetail.commodityName" @click="supervisor(index)">
            <img v-for="(item,index) in datainfo" v-if="item.orderDetail.categoryName=='监理'" src="../../../../static/images/presentation3.png" :alt="item.orderDetail.commodityName" @click="supervisor(index)">
            <img v-for="(item,index) in datainfo" v-if="item.orderDetail.categoryName=='规划'" src="../../../../static/images/presentation4.png" alt="">
            <img v-for="(item,index) in datainfo" v-if="item.orderDetail.categoryName=='毛坯房验收'" src="../../../../static/images/presentation10.png" :alt="item.orderDetail.commodityName" @click="supervisor(index)">
            <img v-for="(item,index) in datainfo" v-if="item.orderDetail.categoryName=='单次水电验收' || item.orderDetail.categoryName=='水电验收'" src="../../../../static/images/presentation8.png" :alt="item.orderDetail.commodityName" @click="supervisor(index)">
            <img v-for="(item,index) in datainfo" v-if="item.orderDetail.categoryName=='单次巡检'" src="../../../../static/images/presentation5.png" :alt="item.orderDetail.commodityName" @click="supervisor(index)">
            <img v-for="(item,index) in datainfo" v-if="item.orderDetail.categoryName=='精装修验收'" src="../../../../static/images/presentation9.png" :alt="item.orderDetail.commodityName" @click="supervisor(index)">
            <img v-for="(item,index) in datainfo" v-if="item.orderDetail.categoryName=='经典服务'" src="../../../../static/images/presentation7.png" :alt="item.orderDetail.commodityName" @click="supervisor(index)">
            <img v-for="(item,index) in datainfo" v-if="item.orderDetail.categoryName=='全案服务'" src="../../../../static/images/presentation6.png" :alt="item.orderDetail.commodityName" @click="companion(index)">
        </div>
    </div>
</template>
<script>
import { Toast } from 'mint-ui'
import {operatelocalstorage} from '../../../assets/javascript/localstorage_hasdata.js'
export default {
    data(){
        return {
           appid:'',
           datainfo:'',
           userInfo:'',
           name:''
        }
    },
    created(){
        //设置头部文字
        this.$root.$emit('header', '装修管家报告中心');
        var that=this
        // alert(this.$route.query.openId)
        //请求报告数据
        let userInfo=JSON.parse(operatelocalstorage('userinfo',null,'get',null))
        this.userInfo=userInfo
        var phone=userInfo.mobile
        this.$http({
            url: "/api/product/ProjectEstablish/queryListByOrderInfoPhone?phone="+phone,
            method: "post",
            data:{},
        }).then((res) => {
            if(res.data.status==200){
                var data=res.data.info
                for(let i=0,len=data.length;i<len;i++){
                    if(data[i].orderDetail.categoryName==null){
                        if(data[i].orderDetail.serviceType.serName=='陪签服务'){
                            data[i].orderDetail.categoryName='陪签'
                        }else if(data[i].orderDetail.serviceType.serName=='全程监理'){
                            data[i].orderDetail.categoryName='监理'
                        }else{
                            data[i].orderDetail.categoryName=data[i].orderDetail.serviceType.serName
                        }
                    }else{
                        data[i].orderDetail.categoryName=data[i].orderDetail.categoryName
                    }
                    console.log(data[i].orderDetail.categoryName)
                }
                console.log(res.data.info)
                that.datainfo=res.data.info
                that.name=res.data.info[0].name
            }else{
                Toast(res.data.msg);
            }   
        });
    },
    methods:{
        // onSwipeRight(){ //左滑上一页
        //     this.$router.go(-1)
        // },
        supervisor(index){  //跳转到监理报告列表
            let id=this.datainfo[index].id
            var that=this
            this.$http({
                url: "/api/public/entryreport/queryMapByProjectIds",
                method: "post",
                data:[id],
            }).then((res) => {
                var data=res.data.info
                console.log(res)
                if(data.length==0){
                    Toast('此项目未生成节点');
                    // that.$router.go(-1);
                }else{
                    console.log(that.datainfo[index])
                    sessionStorage.setItem('presentationInfo',JSON.stringify(that.datainfo[index]))  
                    that.$router.push('/supervisorList?company=92&id='+id);
                }
            });
        },
        companion(index){  //跳转到陪签报告
            let id=this.datainfo[index].id
            var that=this
            this.$http({   //判断是否有报告
            url: "/api/public/entrysignadditmes/queryByIdss?judge=false",
            method: "post",
            data:[id],
            }).then((res) => {
                if(res.data.status==200){
                    let aa=0
                    for(let i in res.data.info){
                        if(res.data.info[i].list.length!=0){
                            aa++
                        }
                    }
                    // console.log(aa)
                    if(aa==5){
                        sessionStorage.setItem('presentationInfo',JSON.stringify(that.datainfo[index]))
                        that.$router.push('/companionReport?company=92&id='+id);
                    }else{
                        Toast('此项目还未有报告上传')
                    }
                }else{
                    Toast(res.data.msg)
                }
            });
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
.contain .header{
    width: 100%;
    height: 2.2rem;
    background-image: url('../../../../static/images/indexTopbg.png');
    background-size: cover;
    overflow: hidden;
}
.contain .userInfo{
    width: 4.1rem;
    margin-left: 0.78rem;
    margin-top:0.4rem;
    text-align: left
}
.contain .userInfo img{
    width: 1.5rem;
    height: 1.5rem;
    float: left;
}
.contain .userInfo .text{
    float: left;
    font-size: 0.28rem;
    line-height: 0.56rem;
    color: #fff;
    margin-left: 0.3rem;
    padding-top: 0.2rem;
}
.contain .userInfo .text i{
    margin-right: 0.1rem;
}
.contain .title{
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
.contain .title i{
    margin-right: 0.1rem;
    float: left;
    color: #11b786;
    font-size: 0.42rem;
}
.contain .content{
    width: 93%;
    padding: 0.18rem;
    overflow: hidden;
    margin: 0 auto;
}
.contain .content img{
    display: block;
    border-radius: 0.12rem;
    width: 100%;
    box-shadow: 0 0 0.1rem 0.01rem #006446;
    margin-top: 0.25rem;
}
</style>


