<template>
    <div class='contain'>
        <div class='imgs'>
            <div v-if='styleone'>
                <div class='logo1'></div>
                <div class='logo2'></div>
            </div>
            <div v-if="styletwo">
                <div v-if='resignflag' :style='style2Login1'></div>
                <div v-if='!resignflag' :style='style2Login2'></div>                
            </div>
            <div v-if="stylethree">
                <div v-if='resignflag' :style='style3Login1'></div>
                <div v-if='!resignflag' :style='style3Login2'></div> 
            </div>
        </div>
        
        <!-- 登录 -->
        <div class='form login' v-show='loginflag'>
            <div class="phone">
                <i class='icon iconfont icon-shouji font'></i>
                <input type="tel" placeholder="手机号码" v-model="phone" key='phone_login' @change='checkphone()' @focus="focus_input" @blur="blur_input">
                <p class='error'>{{phonejson.msg}}</p>
            </div>
            <div class="psw">
                <i class='icon iconfont icon-16suo font'></i>
                <input type="password" placeholder="密码" v-model="psw" @change='checkpsw()' @focus="focus_input" @blur="blur_input">
                <p class='error'>{{pswjson.msg}}</p>
            </div>
            <div class="verificationcode">
                <i class='icon iconfont icon-key font'></i>
                <input type="number" class='codeinput' id='codelogin' placeholder="验证码" v-model="code" @change='checkcode()' @focus="focus_input" @blur="blur_input">
                <p class='error'>{{codejson.msg}}</p>
                <div id='codeimg' class='codeimg font border' @click='yzn'>{{codehas}}</div>
            </div>
            <p class='opera_quick'>
                <mt-button type="default" class='btn-login button' @click="login">登录<i class='icon iconfont icon-xiangyoujiantou'></i></mt-button>
                <router-link to='' class='quicklogin' @click.native='switch_quick'>快速登录</router-link>
            </p>
            <p style='text-align:left;'>
                <!-- <router-link to='' class='forgetpsw'>忘记密码？</router-link> -->
            </p>
            <mt-button type="default" class='btn-resign'  @click.native='switch_resign'>没有账号，立即注册</mt-button>
        </div>
        <!-- 快速登录 -->
        <div class='form loginquick' v-show='loginquickflag'>
            <div class="phone">
                <i class='icon iconfont icon-shouji font'></i>
                <input type="tel" placeholder="手机号码" v-model="phone" @change='checkphone()' @focus="focus_input" @blur="blur_input">
                <p class='error'>{{phonejson.msg}}</p>
            </div>
            <div class="verificationcode">
                <i class='icon iconfont icon-key font'></i>
                <input type="number" class='codeinput' placeholder="验证码" v-model="code" @focus="focus_input" @blur="blur_input">
                <p class='error'></p>
                <router-link :to="linkURL" style='position:absolute;font-size:.3rem;top:.6rem;right:.2rem;'  @click.native='getcode(2)'>{{second}}</router-link>
            </div>
            <p class='opera_quick'>
                <mt-button type="default" class='btn-login button' @click="loginquick">快速登录</mt-button>
                <!-- <router-link to='' class='pswlogin' @click.native='switch_login'>密码登录</router-link> -->
            </p>
            <div class='opera_quicka'  v-show="iswxLogin">
                <p @click="wechatlogin" class="wechatloginP">
                    <img src="./../../../../static/images/weixin.png" style="    width: 0.34rem;">
                    微信登录
                </p>
                <mt-button type="default" class='btn-resign'  @click.native='switch_resign'>没有账号，立即注册</mt-button>
            </div>
            
            
        </div>
        <!-- 注册 -->
        <div class='form login' v-show='resignflag'>
            <div class="phone">
                <i class='icon iconfont icon-shouji font'></i>
                <input type="tel" placeholder="手机号码" v-model="phone" key='phone_login' @change='checkphone()' @focus="focus_input" @blur="blur_input">
                <p class='error'>{{phonejson.msg}}</p>
            </div>
            <div class="verificationcode">
                <i class='icon iconfont icon-key font'></i>
                <input type="number" class='codeinput' placeholder="验证码" v-model="code" @focus="focus_input" @blur="blur_input">
                <p class='error'></p>
                <router-link :to="linkURL"  style='position:absolute;font-size:.3rem;top:.6rem;right:.2rem;'  @click.native='getcode(1)'>{{second}}</router-link>
            </div>
            <div class="psw" v-if='false'>
                <i class='icon iconfont icon-16suo font'></i>
                <input type="password" placeholder="密码" v-model="psw" @change='checkpsw()' @focus="focus_input" @blur="blur_input">
                <p class='error'>{{pswjson.msg}}</p>
            </div>
            <div class="psw" v-if='false'>
                <i class='icon iconfont icon-16suo font'></i>
                <input type="password" placeholder="确认密码" v-model="pswtwice" @change='pswcertain()' @focus="focus_input" @blur="blur_input">
                <p class='error'>{{pswcertainjson.msg}}</p>
            </div>
            
            <div class='agreement' >
                <mt-button type="default" class='btn-login button' @click.native="resign" style='z-index:99;'>注册</mt-button>
                <mt-checklist
                v-if='false'
                    v-model="agreement"
                    :options="['用户协议书']">
                </mt-checklist>
                
                <p class="tit92" v-show="titShow">新用户注册即送100元优惠券</p>
            </div>
            <div class='opera_quicka'  v-show="iswxLogin">
                <p @click="wechatlogin" class="wechatloginP" >
                    <img src="./../../../../static/images/weixin.png" style="    width: 0.34rem;">
                    微信登录
                </p>
                <mt-button type="default" class='btn-resign' @click='switch_login'>已有账号，立即登录</mt-button>
            </div> 
        </div>
        
        <!-- 微信登录绑定手机 -->
         <mt-popup v-model="bindingPhone" popup-transition="popup-fade" class="bindingPhone">
             <div class="bindingPhoneContent">
                 <p class="bindingPhoneTitle">请绑定手机号！</p>
                 <div class="phone">
                    <i class='icon iconfont icon-shouji font'></i>
                    <input type="tel" placeholder="手机号码" v-model="phones" @change='checkphones()' @focus="focus_input" @blur="blur_input" >
                    <p class='error'>{{phonejson.msgs}}</p>
                </div>
                <div class="verificationcode">
                    <i class='icon iconfont icon-key font'></i>
                    <input type="number" class='codeinput' placeholder="验证码" v-model="codes" @focus="focus_input" @blur="blur_input">
                    <p class='error' v-show="bindingPhoneError" style="    position: absolute;">请输入正确的验证码</p>
                    <router-link :to="linkURL" style='position:absolute;font-size:.3rem;top:.6rem;right:.2rem;'  @click.native='getcodes(1)'>{{second}}</router-link>
                </div>
                <p class='opera_quicks' style=" margin-top: .8rem;">
                    <mt-button type="default" class='btn-logins button' @click="bindingPhonelogin" >完成并登录</mt-button>
                </p>
             </div>
            
        </mt-popup>
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
            phones:'',
            psw:'',
            pswtwice:'',
            code:'',
            codes:'',
            codehas:'',
            agreement:['用户协议书'],
            phonejson:{
                status:false,
                msg:'',
                msgs:''
            },
            pswjson:{
                status:false,
                msg:''
            },
            pswcertainjson:{
                status:false,
                msg:''
            },
            codejson:{
                status:false,
                msg:''
            },
            loginflag:false,
            loginquickflag:true,
            resignflag:false,
            titShow:false,
            second:'获取验证码',
            to:'',
            clientHeight:0,
            in_resolve:false,
            bindingPhone:false,
            bindingPhoneError:false,
            openId:'',
            companyid:null,
            style2Login2:{
                width:'100%',
                height:'2rem',
                backgroundImage:"url(" + require("../../../../static/images/style2-login1.png") + ")",
                backgroundSize:'cover',
                position:'absolute',
                bottom:'1rem',
                
            },
            style2Login1:{
                width:'1.8rem',
                height:'1.8rem',
                backgroundImage:"url(" + require("../../../../static/images/style2-resign.png") + ")",
                backgroundSize:'cover',
                position:'absolute',
                top:0,
                bottom:0,
                left:0,
                right:0,
                margin:'auto',
                backgroundPositionX:'50%'
            },
             style3Login2:{
                width:'80%',
                left:'10%',
                height:'3.95rem',
                backgroundImage:"url(" + require("../../../../static/images/style3-login1.png") + ")",
                backgroundSize:'cover',
                position:'absolute',
                bottom:'1rem',
                
            },
            style3Login1:{
                width:'80%',
                left:'10%',
                height:'3.95rem',
                backgroundImage:"url(" + require("../../../../static/images/style3-login1.png") + ")",
                backgroundSize:'cover',
                position:'absolute',
                bottom:'1rem',
            },
            styletwo:false,
            styleone:true,
            stylethree:false,
            isAndroid:true,
            wHeight:0,
            linkURL:'',
            iswxLogin:true,
        }
    },
    mounted(){
        this.yzn();
        // this.isXT();
        // this.getCommid()
		window.onresize = () => {
            return (() => {
                let pageHeight = window.innerHeight
                if (pageHeight < 530) {
                    this.iswxLogin = false // 隐藏
                    console.log('隐藏')
                } else {
                    this.iswxLogin = true // 显示
                    console.log('显示')
                }
            })()
        }
    },
    created(){
        this.wHeight = window.innerHeight;
        let companyid=this.$route.query.company==null?this.$route.query.companyId:this.$route.query.company;
       
        if(companyid!=null){
            sessionStorage.setItem('companyId', companyid);
            this.companyid=companyid;
        }
        else{
            this.companyid=sessionStorage.getItem('companyId');;
        }
        if(sessionStorage.getItem('companyId')==92){
            this.styletwo=true;
            this.styleone=false;
            this.stylethree=false;
            this.titShow =true;
        }else if(sessionStorage.getItem('companyId')==114){
           // alert(114)
            this.styletwo=false;
            this.styleone=false;
            this.stylethree=true;
        }
        let openid=this.$route.query.openId;
        this.openId=openid;
        // console.log(this.$route.query.data)
            let newCompanydata = this.$route.query.data;
            if(newCompanydata != undefined){
             let newParameters = decodeURIComponent(newCompanydata)
             // Toast("授权成功,请登录");
             newParameters = JSON.parse(newParameters)
             console.log(newParameters)
                    if(newParameters != undefined || newParameters != null){
                    this.openId= newParameters.openId
                    localStorage.setItem('openId',this.openId)
                    sessionStorage.setItem('openId',this.openId)
                }
            }

          let linkURL = window.location.href;
          let linkURLs = linkURL.split('login');
          this.linkURL = '/login'+linkURLs[1]
        // if(openid==null){
        //     Toast('获取openid失败');
        // }
        // else{
        //     this.openId=openid;
        // }
        var u = navigator.userAgent;
        var isAndroid = u.indexOf('Android') > -1 || u.indexOf('Adr') > -1; //android终端
        var isiOS = !!u.match(/\(i[^;]+;( U;)? CPU.+Mac OS X/); //ios终端
        this.isAndroid=isAndroid;
    },
    methods:{
        focus_input(e){
            if(this.isAndroid){
                if(this.clientHeight==0){
                    this.clientHeight=document.body.clientHeight;
                }
                document.body.style.height=this.clientHeight+100+'px';
                if(e.target.getAttribute('id')=='codelogin'){
                    document.body.style.height=this.clientHeight+180+'px';
                    e.target.scrollIntoView(true);
                }
            }
        },
        blur_input(e){
            if(this.isAndroid){
                document.body.style.height=this.clientHeight+'px';
            }
        },
        // 登录
        login(){
            if(this.phonejson.status&&this.pswjson.status&&this.codejson.status){
                if(this.in_resolve){
                    return;
                }
                Indicator.open('Loading...');
                let that=this;
                let data='';
                // let openId=sessionStorage.getItem('openId');
                if(this.openId==''){
                    data='mobile='+this.phone+'&password='+this.psw;
                }
                else{
                    data='mobile='+this.phone+'&password='+this.psw+'&openId='+this.openId;
                }
                this.in_resolve=true;
                this.$http({
                    url: '/api/customer/account/login?'+data,
                    method: 'POST',
                    // 设置请求头
                    headers: {
                        'Content-Type': 'application/x-www-form-urlencoded'
                    }
                })
                .then(res => {
                    Indicator.close();
                    console.log(res);
                    var msg = res.data.msg
                    if (msg !== '登录成功') {
                        Toast(res.data.info);
                        that.in_resolve=false;
                    } else {
                        let data=res.data.info;
                        // let json={

                        // };
                        let isActivity = that.$route.query.isActivity;
                         let openId = sessionStorage.getItem('openId')
                        // alert(isActivity)
                                if(isActivity != undefined && openId ==null|| openId == undefined){
                                 //   alert(isActivity)
                                        if(isActivity == 114){
                                            Toast({
                                                message: '正在为你跳转请稍后...',
                                                iconClass: 'icon icon-success',
                                                duration: 500
                                            });
                                            let memberId = data.id;
                                            let phone = that.phone;
                                            let openIds = that.openId
                                            window.location.href = "http://www.house178.com/qbfc/coupon/service/index?memberId="+memberId+"&phone="+phone+"&openId="+openIds
                                    }
                                }
                        console.log(data)
                        operatelocalstorage('userinfo',JSON.stringify(data),'set',300);
                        // sessionStorage.setItem('userinfo', JSON.stringify(data));
                        // this.$store.commit('login',res.data.info)
                        // let userinfo = res.data
                        // sessionStorage.setItem('userinfo', JSON.stringify(data));
                        
                        setTimeout(() => {
                            Toast({
                                message: '登录成功正在为你跳转请稍后...',
                                iconClass: 'icon icon-success',
                                duration: 500
                            });
                            let fromgo=sessionStorage.getItem('fromgo');
                            // let fromgo='/order'
                            
                            if(fromgo==null){
                                if(that.companyid!=null){
                                    this.$router.push('/index?company='+that.companyid);
                                }
                                else{
                                    this.$router.push('/index');
                                }
                                // location.href='http://www.shhongzhiyun.com';
                            }
                            else{
                                that.$router.push(fromgo);
                                sessionStorage.removeItem('fromgo');
                            }
                            // this.$router.push('/index');
                            that.in_resolve=false;
                        }, 1000);
                    }
                    
                })
                .catch(err => {
                    Indicator.close();
                    console.log("错误");
                    console.log(err);
                    that.in_resolve=false;
                })
            }
            else{
                this.checkphone();
                this.checkpsw();
                this.checkcode();
            }
        },
        // 快速登录
        loginquick(){
            if(this.in_resolve){
                return;
            }
            let that = this
            if(this.phonejson.status){
                if(this.code==''){
                    Toast('请输入验证码');
                }
                else{
                    let that=this;
                    let data='';
                    // let openId=sessionStorage.getItem('openId');
                    if(this.openId==''){
                        data='mobile='+this.phone+'&code='+this.code;
                    }
                    else{
                        data='mobile='+this.phone+'&code='+this.code+'&openId='+this.openId;
                    }
                    this.in_resolve=true;
                    Indicator.open('Loading...');
                    this.$http.post('/api/customer/account/quickLogin?'+data)
                    .then(function(response){
                        // Toast(response.data.msg);
                        
                        if(response.data.status == 200){
                            let data=response.data.info;

                           let isActivity = that.$route.query.isActivity;
                         let openId = sessionStorage.getItem('openId')
                        // alert(isActivity)
                                if(isActivity != undefined && openId ==null|| openId == undefined){
                                 //   alert(isActivity)
                                        if(isActivity == 114){
                                            Toast({
                                                message: '正在为你跳转请稍后...',
                                                iconClass: 'icon icon-success',
                                                duration: 500
                                            });
                                            let memberId = data.id;
                                            let phone = that.phone;
                                            let openIds = that.openId
                                            window.location.href = "http://www.house178.com/qbfc/coupon/service/index?memberId="+memberId+"&phone="+phone+"&openId="+openIds
                                    }
                                }

                            operatelocalstorage('userinfo',JSON.stringify(data),'set',300);
                            // sessionStorage.setItem('userinfo', JSON.stringify(data));
                            // that.$store.commit('login',response.data.info)
                            // this.cleardata();
                            // this.$store.commit('login',res.data)
                            setTimeout(() => {
                                Toast({
                                    message: '登录成功正在为你跳转请稍后...',
                                    iconClass: 'icon icon-success',
                                    duration: 500
                                });
                                let fromgo=sessionStorage.getItem('fromgo');
                              //  console.log(fromgo)
                                if(fromgo==null){
                                    if(that.companyid!=null){
                                        that.$router.push('/index?company='+that.companyid);
                                    }
                                    else{
                                        that.$router.push('/index');
                                    }
                                }
                                else{
                                    that.$router.push(fromgo);
                                    sessionStorage.removeItem('fromgo');
                                }
                                // that.$router.push('/index');
                                that.in_resolve=false;
                            }, 1000);
                        }
                        else{
                            Toast(response.data.msg);
                            that.in_resolve=false;
                        }
                        Indicator.close();
                    })
                    .catch(function(response){
                        Indicator.close();
                         Toast('登录失败');
                         that.in_resolve=false;
                    });
                }
            }
            else{
                this.checkphone();
            }
        },
        //微信登录
        wechatlogin(){
            if(this.in_resolve){
                return;
            }
            let that=this;
            let data='';
            if(this.openId==''){
               Toast('请在微信打开此页面');
            }
            else{
                data= this.openId;
                this.in_resolve=true;
                Indicator.open('Loading...');
                this.$http.post('/api/customer/account/loginByOpenId?openId='+data)
                .then(function(response){
                    
                    if(response.data.status == 200){
                        let data=response.data.info;

                        let isActivity = that.$route.query.isActivity;
                        let openId = sessionStorage.getItem('openId')
                            if(isActivity != undefined && openId ==null|| openId == undefined){
                                    if(isActivity == 114){
                                        Toast({
                                            message: '正在为你跳转请稍后...',
                                            iconClass: 'icon icon-success',
                                            duration: 500
                                        });
                                        let memberId = data.id;
                                        let phone = that.phone;
                                        let openIds = that.openId
                                        window.location.href = "http://www.house178.com/qbfc/coupon/service/index?memberId="+memberId+"&phone="+phone+"&openId="+openIds
                                }
                            }

                        operatelocalstorage('userinfo',JSON.stringify(data),'set',300);
                        setTimeout(() => {
                            Toast({
                                message: '登录成功正在为你跳转请稍后...',
                                iconClass: 'icon icon-success',
                                duration: 500
                            });
                            let fromgo=sessionStorage.getItem('fromgo');

                            if(fromgo==null){
                                if(that.companyid!=null){
                                    that.$router.push('/index?company='+that.companyid);
                                }
                                else{
                                    that.$router.push('/index');
                                }
                            }else{
                                that.$router.push(fromgo);
                                sessionStorage.removeItem('fromgo');
                            }
                            that.in_resolve=false;
                        }, 1000);
                    }else if(response.data.status == 300){
                        that.bindingPhone = true;
                        that.in_resolve=false;
                        that.phones = '';
                        that.codes = '';
                    }else{
                        Toast(response.data.msg);
                        that.in_resolve=false;
                    }
                    Indicator.close();
                })
                .catch(function(response){
                    Indicator.close();
                        Toast('登录失败');
                        that.in_resolve=false;
                });
            }
        },
        //微信注册
        wechatResign(){
            this.bindingPhone = true;
            this.in_resolve=false;
            this.phones = '';
            this.codes = '';
        },
        //微信绑定手机并登录
        bindingPhonelogin(){
            if(this.in_resolve){
                return;
            }
            let that = this
            if(this.phonejson.status){
                if(this.codes==''){
                    alert('请输入验证码');
                }
                else{
                    let that=this;                 
                    let recommendedTeamId=this.$route.query.recommendedTeamId;
                    let recommendedAdminId=this.$route.query.recommendedAdminId;
                    let openId = this.openId
                    let data={
                        mobile:that.phones,
                        // password:that.psw,
                        code:that.codes,
                        openId:this.openId,
                        companyId:this.companyid,
                        recommendedAdminId:recommendedAdminId,
                        recommendedTeamId:recommendedTeamId
                    };
                    this.in_resolve=true;
                    Indicator.open('Loading...');
                    this.$http.post('/api/customer/account/register?doLogin=true&loginIfExist=true&openId='+openId,data)
                    .then(function(response){
                        
                        if(response.data.status == 200){
                            let data=response.data.info;

                           let isActivity = that.$route.query.isActivity;
                         let openId = sessionStorage.getItem('openId')
                        // alert(isActivity)
                                if(isActivity != undefined && openId ==null|| openId == undefined){
                                 //   alert(isActivity)
                                        if(isActivity == 114){
                                            Toast({
                                                message: '正在为你跳转请稍后...',
                                                iconClass: 'icon icon-success',
                                                duration: 500
                                            });
                                            let memberId = data.id;
                                            let phone = that.phones;
                                            let openIds = that.openId
                                            window.location.href = "http://www.house178.com/qbfc/coupon/service/index?memberId="+memberId+"&phone="+phone+"&openId="+openIds
                                    }
                                }

                            operatelocalstorage('userinfo',JSON.stringify(data),'set',300);
                            //家博注册领取优惠券
                            if(that.companyid == 92){
                                that.$http.post('/api/product/coupon/customer/mall/insert?number=1&mobile='+that.phones,["b6b60f8c-dc17-11e8-a50c-fa163edc8ab6"]).
                                then(function(res){
                                    if(res.data.status==200){
                                         Toast('领取成功');
                                    }else if(res.data.status==300){
                                        Toast('领取失败');
                                    }
                                })
                                .catch(function(response){
                                    console.log(response);
                                })
                            }
                            setTimeout(() => {
                                Toast({
                                    message: '登录成功正在为你跳转请稍后...',
                                    iconClass: 'icon icon-success',
                                    duration: 500
                                });
                                let fromgo=sessionStorage.getItem('fromgo');
                              //  console.log(fromgo)
                                if(fromgo==null){
                                    if(that.companyid!=null){
                                        that.$router.push('/index?company='+that.companyid);
                                    }
                                    else{
                                        that.$router.push('/index');
                                    }
                                }
                                else{
                                    that.$router.push(fromgo);
                                    sessionStorage.removeItem('fromgo');
                                }
                                // that.$router.push('/index');
                                that.in_resolve=false;
                            }, 1000);
                        }
                        else{
                            alert(response.data.msg);
                            that.in_resolve=false;
                        }
                        Indicator.close();
                    })
                    .catch(function(response){
                        Indicator.close();
                         alert('登录失败');
                         that.in_resolve=false;
                    });
                }
            }
            else{
                this.checkphone();
            }
        },
        
        // 注册
        resign(){
            if(this.phonejson.status){
                if(this.code==''){
                    Toast('请输入验证码');
                }
                else if(this.agreement.length==0){
                    Toast('请确认服务条款');
                }
                else{
                    // if(this.openId==''){
                    //     Toast('请授权后再注册！');
                    //     return;
                    // }
                    let that=this;                    
                    let recommendedTeamId=this.$route.query.recommendedTeamId;
                    let recommendedAdminId=this.$route.query.recommendedAdminId;
                    let openId = this.openId
                    let data={
                        mobile:that.phone,
                        // password:that.psw,
                        code:that.code,
                        openId:this.openId,
                        companyId:this.companyid,
                        recommendedAdminId:recommendedAdminId,
                        recommendedTeamId:recommendedTeamId
                    };
                    Indicator.open('Loading...');
                    this.$http.post('/api/customer/account/register?doLogin=true&openId='+openId,data)
                    .then(function(res){
                         Indicator.close();
                        console.log(res);
                        var msg = res.data.msg
                        if(res.data.status==200){
                            let data=res.data.info;

                         let isActivity = that.$route.query.isActivity;
                         let openId = sessionStorage.getItem('openId')
                            //钱报活动跳转判断
                            if(isActivity != undefined && openId ==null|| openId == undefined){
                                //   alert(isActivity)
                                    if(isActivity == 114){
                                        Toast({
                                            message: '正在为你跳转请稍后...',
                                            iconClass: 'icon icon-success',
                                            duration: 500
                                        });
                                        let memberId = data.id;
                                        let phone = that.phone;
                                        let openIds = that.openId
                                        window.location.href = "http://www.house178.com/qbfc/coupon/service/index?memberId="+memberId+"&phone="+phone+"&openId="+openIds
                                }
                            }
                           //保存用户信息
                            operatelocalstorage('userinfo',JSON.stringify(data),'set',300);
                            //家博注册领取优惠券
                            if(that.companyid == 92){
                                that.$http.post('/api/product/coupon/customer/mall/insert?number=1&mobile='+that.phone,["b6b60f8c-dc17-11e8-a50c-fa163edc8ab6"]).
                                then(function(res){
                                    if(res.data.status==200){
                                         Toast('领取成功');
                                    }else if(res.data.status==300){
                                        Toast('领取失败');
                                    }
                                })
                                .catch(function(response){
                                    console.log(response);
                                })
                            }
                            //登录注册后跳转
                            setTimeout(() => {
                                Toast({
                                    message: '登录成功正在为你跳转请稍后...',
                                    iconClass: 'icon icon-success',
                                    duration: 500
                                });
                                let fromgo=sessionStorage.getItem('fromgo');
                                
                                if(fromgo==null){
                                    if(that.companyid!=null){
                                        that.$router.push('/index?company='+that.companyid);
                                    }
                                    else{
                                        that.$router.push('/index');
                                    }
                                }
                                else{
                                    that.$router.push(fromgo);
                                    sessionStorage.removeItem('fromgo');
                                }
                                that.in_resolve=false;
                            }, 1000);
                        }else{
                            Toast(res.data.msg);
                            that.in_resolve=false;
                        }
                    })
                    .catch(function(response){
                        Toast('注册失败');
                    })
                }
            }
            else{
                this.checkphone();
                // this.checkpsw();
                // this.pswcertain();
            }
        },
        // 获取验证码
        getcode(type){
            var that=this
            console.log('aaaaaa')
            if(this.second=='获取验证码'){
                if(this.phonejson.status){
                    let that=this;
                    this.$http.post('/api/customer/resource/sendSmsCode?mobile='+that.phone+'&type='+type)
                    .then(function(response){
                        Toast(response.data.msg);
                        that.second=60;
                        that.countDown();
                    })
                    .catch(function(response){
                        Toast('验证码获取失败');
                    });
                }
                else{
                    Toast('请输入正确的手机号');
                    this.checkphone();
                }
            }
            
        },
        //微信绑定手机验证码
        getcodes(type){
            var that=this
            if(this.second=='获取验证码'){
                if(this.phonejson.status){
                    let that=this;
                    this.$http.post('/api/customer/resource/sendSmsCode?mobile='+that.phones+'&type='+type)
                    .then(function(response){
                       // Toast(response.data.msg);
                        that.second=60;
                        that.countDown();
                    })
                    .catch(function(response){
                        alert('验证码获取失败');
                    });
                }
                else{
                    this.bindingPhoneError = true
                    this.checkphone();
                }
            }
        },
        // 倒计时
        countDown(){
            let time=setInterval(()=>{
                if(this.second==0){
                    clearInterval(time);
                    this.second='获取验证码';
                }
                else{
                    this.second=this.second-1;
                }
            },1000);
        },
        switch_quick(){
            this.loginflag=false;
            this.loginquickflag=true;
            this.resignflag=false;
            this.cleardata();
        },
        switch_login(){
            // this.loginflag=true;
            this.loginquickflag=true;
            this.resignflag=false;
            this.cleardata();            
        },
        switch_resign(){
            this.loginflag=false;
            this.loginquickflag=false;
            this.resignflag=true;
            this.cleardata();    
        },
        // 清空数据
        cleardata(){
            this.phone='';
            this.phones='';
            this.psw='';
            this.pswtwice='';
            this.code='';
            this.codes='';
            this.phonejson.status=false;
            this.phonejson.msg='';
            this.phonejson.msgs='';
            this.pswjson.status=false;
            this.pswjson.msg='';
            this.codejson.status=false;
            this.codejson.msg='';
        },
        // 手机号验证
        checkphone(){
            let result=checkClass.checkTel(this.phone);
            this.phonejson.status=result.flag;
            this.phonejson.msg=result.flag?'':result.error;
        },
        checkphones(){
            let result=checkClass.checkTel(this.phones);
            this.phonejson.status=result.flag;
            this.phonejson.msgs=result.flag?'':result.error;
        },
        // 密码验证
        checkpsw(){
            let result=checkClass.checkNumAndLetters(this.psw);
            this.pswjson.status=result.flag;
            this.pswjson.msg=result.flag?'':result.error;
        },
        // 验证码验证
        checkcode(){
            this.codejson.status=this.code.toLocaleLowerCase()==this.codehas.toLocaleLowerCase();
            this.codejson.msg=this.codejson.status?'':'验证码错误';
            if(!this.codejson.status){
                this.yzn();
            }
        },
        // 密码确认
        pswcertain(){
            let flag=this.pswtwice==this.psw;
            this.pswcertainjson.status=flag;
            this.pswcertainjson.msg=flag?'':'两次密码不一致';
        },
        getAllLetter() {
            var letterStr = "a,b,c,d,e,f,g,h,i,j,k,l,m,n,o,p,q,r,s,t,u,v,w,x,y,z,A,B,C,D,E,F,G,H,I,J,K,L,M,N,O,P,Q,R,S,T,U,V,W,X,Y,Z";
            return letterStr.split(",");
        },
        /**生成一个随机数**/
        randomNum(min, max) {
            return Math.floor(Math.random() * (max - min) + min);
        },
        yzn() {
            let letterlist=this.getAllLetter();
            let [num1,num2,num3,num4]=[this.randomNum(0,51),this.randomNum(0,9),this.randomNum(0,51),this.randomNum(0,51)];
            this.codehas=letterlist[num1]+num2+letterlist[num3]+letterlist[num4];
        },
    }
}
</script>
<style scoped>
.imgs{
    width:100%;
    height:4rem;
    position:relative;
}
.logo1{
    width:2rem;
    height:.6rem;
    position:absolute;
    left:1rem;
    top:1.5rem;
    background-image:url("/static/images/icon1.png");
    background-size: cover;
}
.logo2{
    width:4rem;
    height:3.7rem;
    position:absolute;
    right:0.3rem;
    top:.5rem;
    background-image:url("/static/images/icon2.png");
    background-size: cover;
}
.contain{
    /* position: fixed; */
    height:100vh;
    /* width: 100%; */
    background: #fff;
}
.form{
    background-color: rgba(255,255,255,.9);
    padding:.2rem 1rem 0 1rem; 
    /* position: relative; */
}
.form .icon{
    color:rgb(39, 162, 242);
    font-size:.45rem;
    position: absolute;
    top:.53rem;
}
.form input{
    text-indent: .7rem;
    /* padding-left: .7rem; */
}
.phone,.psw,.verificationcode{
    margin-top: -.3rem;
    position: relative;
}
.phone input,.psw input,.verificationcode input{
    width:100%;
    height: 0.75rem;
    border-bottom: 0.01rem solid #c4c4c4;
    outline: none;
    font-size:0.3rem;
}
.verificationcode input{
    /* width: 2rem; */
    margin-top: .4rem;
}
.verificationcode .codeimg{
    width: 2rem;
    height: 0.6rem;
    line-height: .6rem;
    color: rgb(39, 162, 242);
    font-size: .4rem;
    font-style: italic;
    letter-spacing: .1rem;
    right: .1rem;
    bottom:.8rem;
    position: absolute;
    border: 0.01rem solid rgb(39, 162, 242);
    border-radius:20px;
    float: left;
}
.opera_quick{
    font-size: 0.3rem;
    margin-top: .2rem;
    height:.8rem;
}
.tit92{font-size: 0.24rem;
    margin-top: 0.35rem;
    left: 2.2rem;
    position: absolute;
    color: #edc581;}
.opera_quicks{
    font-size: 0.3rem;
    margin-top: .4rem;
    height:.8rem;
}
.bindingPhone{width: 85%;}
.bindingPhoneContent{padding: 0.8rem 0.8rem;}
.bindingPhoneTitle{
        font-size: 0.34rem;
    text-align: left;
    margin-bottom: 0.5rem;
}
.bindingPhoneContent .phone i {
    color: rgb(39, 162, 242);
    font-size: .45rem;
    position: absolute;
    top: .53rem;
}
.bindingPhoneContent .phone input {
    text-indent: .7rem;
}
.bindingPhoneContent .verificationcode i {
    color: rgb(39, 162, 242);
    font-size: .45rem;
    position: absolute;
    top: .53rem;
}
.bindingPhoneContent .verificationcode input {
    text-indent: .7rem;
}
.forgetpsw{
    font-size: .3rem;
    line-height: .8rem;
    color: rgb(39, 162, 242);
    
}
.pswlogin{
    font-size: .3rem;
    line-height: .8rem;
    float: left;
}
.quicklogin{
    float: left;
    line-height: .8rem;
}
.btn-login{
    width: 60%;
    border-radius: 1rem !important;
    margin: 0rem  auto;
    height: .9rem;
    line-height: .8rem;
    text-align: center;
    font-size: .35rem;
    color: #fff;
    border-radius: 5px;
    background:rgb(39, 162, 242);
    /* float: right; */
}
.btn-login .icon{
    color:#fff;
    position: absolute;
    top:0;
}
.btn-logins{
    width: 60%;
    border-radius: 1rem !important;
    margin: 0rem  auto;
    height: .9rem;
    line-height: .8rem;
    text-align: center;
    font-size: .35rem;
    color: #fff;
    border-radius: 5px;
    background:rgb(39, 162, 242);
    /* float: right; */
}

.error{
    height: .2rem;
    font-size: .3rem;
    text-align: left;
    padding: .2rem 0;
    color: red;
}
.agreement{
    margin-top:.2rem;
    font-size: .3rem;
    line-height: .35rem;
}
/* .agreement label{
    float: right;
} */
.agreement input[type='checkbox']{
    width:.35rem;
    height:.35rem;
    background-color: #fff;
    appearance: none;
    border:1px solid rgb(39, 162, 242);
    margin-right: .2rem;
}
.opera_quicka{
    width: 100%;
    position: fixed;
    left: 0;
    right: 0;
    bottom: 0rem;
    height: 2rem;
}
.wechatloginP{
    font-size: 0.32rem;
    width: 100%;
    height: 0.5rem;
    left: 0;
    right: 0;
    position: absolute;
    top: 0rem;
    display: block;
}
.btn-resign{
    width:100%;
    height: 1.5rem;
    background-color: #fff;
    left: 0;
    right:0;
    font-size: 0.3rem;
    position: absolute;
    bottom: 0px;
    /* margin-top: 1rem; */
    /* margin-left: -1rem; */
    display: block;
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

