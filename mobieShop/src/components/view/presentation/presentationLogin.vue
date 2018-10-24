<template>
    <div class='presentation' v-touch:right="onSwipeRight">
         <div class='imgs'>
            <div class='style2Login1'></div>              
        </div>
        <div class='form1'>
            <div class="phonecode">
                <i class='icon iconfont icon-yonghu1 font1'></i>
                <input type="tel" placeholder="手机号码" v-model="phone">
                <p class='error'></p>
            </div>
            <div class="code">
                <i class='icon iconfont icon-mima font1'></i>
                <input type="text" class='codeinput' placeholder="验证码" v-model="code">
                <span class="getcode"  @click="getCode">{{time}}</span>
                <p class='error'></p>
            </div>
            <!-- <p class="tishi"><i class='icon iconfont icon-zheng font2'></i> 业主可根据手机号查询订单报告</p> -->
            <p class='opera_quick'>
                <mt-button type="default" class='btn-login button' @click="loginquick">快速登录</mt-button>
            </p>
        </div>
    </div>
</template>
<script>
import {checkClass} from '../../../assets/javascript/checkClass.js'
import { Toast } from 'mint-ui'
import { Indicator } from 'mint-ui';
import {operatelocalstorage} from '../../../assets/javascript/localstorage_hasdata.js'
export default {
    data(){
        return {
            phone:'',
            code:'',
            logorreg:'1',
            time:'获取验证码',
            isAndroid:true,
            phonejson:{
                status:false,
                msg:'手机号错误'
            },
            appid:'',
            openId:'',
        }
    },
    created(){
        this.$root.$emit('header', '登录');
        if(this.$route.query.openId==null){   //第一次进入页面获取openid
            this.getAppId().then(flag=>{  //zhonzhonye 
            if(flag){
                let json={
                    company:92,
                    recommendedTeamId:null,
                    recommendedAdminId:null,
                    presentation:'isPresentation'
                };
                console.log(JSON.stringify(json))
                let url='https://open.weixin.qq.com/connect/oauth2/authorize?appid='+this.appid
                    +'&redirect_uri=http://pay.shhongzhiyun.com/?json='+JSON.stringify(json)+'&response_type=code&scope=snsapi_userinfo&state=STATE';
                    location.href=url;
                }
            });
        }else{ //第二次进入 获取openid存入缓存中（中转页跳过来的那次）
            sessionStorage.setItem('openIdp',this.$route.query.openId)
            this.openId=this.$route.query.openId
        }
    },
    methods:{
        onSwipeRight(){ //左滑上一页
            this.$router.go(-1)
        },
        loginquick(){ //点击登录跳转页面
            var reg = /^1(3|4|5|7|8)\d{9}$/;
            var that=this
            if(reg.test(that.phone)){
                 if(that.logorreg==2){  //有账号登录
                    alert('登录')
                    this.$http({
                        url: "/api/customer/account/quickLogin?mobile="+that.phone+"&code="+that.code,
                        method: "post",
                        header: {
                        'content-type': 'application/json', // 默认值
                        },
                    }).then((res) => {
                        var data = res.data
                        console.log(data)
                        if (data.status == 200){
                            Toast('登录成功！！');
                            this.$router.push('/presentationIndex?company=92');
                        }else{
                            Toast('登录失败！！');
                        }
                    })
                }else if(that.logorreg==1){  //没有账号注册后登录
                    var openid=sessionStorage.getItem('openIdp')
                    this.$http({
                        url: "/api/customer/account/register?doLogin=true&openid=" +openid,
                        method: "post",
                        header: {
                        'content-type': 'application/json', // 默认值
                        },
                        data:{
                            mobile: that.phone,
                            code: that.code,
                            companyId:92,
                        },
                    }).then((res) => {
                        var data = res.data
                        console.log(data)
                        if (data.status == 200){
                            Toast('登录成功！！');
                            this.$router.push('/presentationIndex?company=92');
                        }else{
                            Toast('登录失败！！');
                        }
                    })
                }
            }else{
                Toast('请输入正确的手机号');
            }
        },
        getCode(e){
            var that=this
            var reg = /^1(3|4|5|7|8)\d{9}$/;    //手机号验证正则表达式
            if(reg.test(that.phone)){
                this.$http({
                    url: "/api/customer/account/queryMap",
                    method: "post",
                    header: {
                    'content-type': 'application/json', // 默认值
                    },
                    data: {
                        mobile: that.phone
                    },
                }).then((resp) => {
                    if (resp.data.info.length>0){
                        that.getCodes(that.phone,2)
                        that.logorreg= 2
                    }else{
                        that.getCodes(that.phone,1)
                        that.logorreg= 1
                    }
                })
            }else{
                Toast('亲输入正确的手机号！');
            }
        },
        getCodes(phone,typ){
            var time = this.time;
            var that=this
            if (that.phone) {
                if (time == "获取验证码" || time == "从新发送") {
                    var dd = 61
                    this.$http({
                        url: "/api/customer/resource/sendSmsCode?mobile=" + phone + "&type="+typ,
                        method: "post",
                        header: {
                            'content-type': 'application/json;charset=utf-8', // 默认值
                        },
                    }).then((res) => {
                         if(res.data.status==200){
                             sessionStorage.setItem('phone',that.phone)
                             Toast(res.data.msg);
                         }
                    });
                    var timer = setInterval(function () {
                    if (dd >= 1) {
                        dd -= 1
                        that.time= dd + "S"
                    } else if (dd <= 0) {
                        clearInterval(timer)
                        that.time= "从新发送"   
                    }
                    }, 1000)
                }
            }
        },
        //获取appId
        getAppId(){
            return new Promise((resolve,reject)=>{
                let that=this;
                // let companyid=sessionStorage.getItem('companyId');
                this.$http.get('/api/product/order/weixin/config?companyId=92')
                .then(res=>{
                    if(res.data.status==200){
                        that.appid=res.data.info.appid;
                        resolve(true);
                    }
                    else{
                        resolve(false);
                        Toast(res.data.msg);
                    }
                })
                .catch(err=>{
                    resolve(false);
                    Toast('appid获取失败');
                })
            })
        },
    }
}
</script>
<style scoped>
.presentation .imgs{
    width:100%;
    height:4rem;
    position:relative;
    background-color: #ffffff
}
.presentation .style2Login1{
    width:2.5rem;
    height:1.8rem;
    background-image:url("../../../../static/images/gjlogo.png");
    background-size:cover;
    position:absolute;
    left:50%;
    transform: translateX(-50%);
    bottom:1rem;
}
.presentation{
    height:100vh;
    background-color:#fff; 
}
.presentation .form1{
    background-color: rgba(255,255,255);
    padding:0 1rem; 
}
.presentation .phonecode input,.code input{
    width:100%;
    height: 0.75rem;
    border: 1px solid #c6f4dc;
    border-radius: 0.75rem;
    outline: none;
    font-size:0.3rem;
    box-sizing: border-box;
    margin: 0rem !important;
    height: 0.83rem;
    padding-left: 0.97rem;
    padding-right: 0.3rem;
}
.presentation .code input{
    box-sizing: border-box;
    padding-right: 2rem !important;
}
.presentation .phonecode,.code{
    margin-bottom: 0.18rem;
    position: relative;
    overflow: hidden;
    margin-top: -0.5rem;
}
.presentation .getcode{
    position:absolute;
    font-size:.3rem;
    top:.39rem;
    right:0.07rem;
    background: #11b786;
    height: 0.72rem;
    border-radius: 0.5rem;
    padding: 0rem 0.2rem;
    line-height: 0.72rem;
    color: #fff;
    z-index: 1000;
}
.presentation .tishi{
    font-size: 0.28rem;
    text-align: left;
    position: relative;
    top: -0.2rem;
    left: 0.3rem
}
.presentation .tishi span{
    padding:0rem 0.1rem;
    background:#11b786;
}
.presentation .btn-resign{
    width:100%;
    height: 1.5rem;
    background-color: #fff;
    left: 0;
    right:0;
    font-size: 0.3rem;
    position: absolute;
    bottom: 0rem;
    display: block;
}
.presentation .error{
    height: .3rem;
    font-size: .16rem;
    text-align: left;
    padding: .2rem 0.5rem;
    color: red;
}
.presentation .font1{
    position: absolute;
    /*top: 50%;
    transform: translateY(-50%); */
    font-size: 0.42rem;
    background: #11b786;
    color: #fff;
    left: 0.06rem;
    top: 0.39rem;
    border-radius: 0.42rem;
    padding: 0.15rem;
}
.presentation .font2{
    color: #11b786;
}
.presentation .btn-login{
    width: 100%;
    background: #11b786 !important;
    border-radius: 0.41rem;
    color: #ffffff
}

</style>
<style>
.mint-cell{
    display: inline;
}
.agreement .mint-cell-wrapper{
    width:fit-content;
}
</style>

