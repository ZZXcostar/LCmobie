<template>
    <div id="invite-result">
        <div class="result-top" ></div>
        <div class="result-explain font" ref="changeText">{{msg}}</div>
        <div class="result-bottom">
            <img class='ImgBox' :src='wxSrc' />
            <p>扫描二维码,前往商城</p>
        </div>
        <div class='backcover' @click.self="closebackcover" v-if='showbackcover'>
            <div class='coupon_cover'>
                  <!-- <ul class="couponList" >
                    <li v-for="(item,index) in coupon" :key="index">
                       <p class='money'><span style='font-size:1.4rem;'>{{item.money}}</span>元</p>
                       <p class='condition'>单笔订单满{{item.condition}}元使用</p>
                    </li>
                  </ul> -->
                <div class='btn' @click="getcoupin"></div>
            </div>
        </div>
    </div>
</template>
<script>
import { Toast } from "mint-ui";
import { MessageBox } from "mint-ui";
export default {
  data() {
    return {
      bgSrc: require("./invite-result.png"),
      wxSrc: "http://www.shhongzhiyun.com/api/static/weixin/78.jpg",
      msg: "",
      coupon: [
        {
          id: "1cb882f9-b1a2-11e8-968b-00163e02c66a",
          money: 400,
          condition: 2000
        },{
          id: "3c8ccb95-b1a2-11e8-968b-00163e02c66a",
          money: 600,
          condition: 3000
        },{
          id: "e78f6090-b1a1-11e8-968b-00163e02c66a",
          money: 100,
          condition: 500
        }
      ],
      showbackcover: true,
      phone: null
    };
  },
  created() {
    let companyId = this.$route.query.companyId;
    let phone = this.$route.query.phone;
    this.phone = phone;
    switch (companyId) {
      case "78": {
        this.wxSrc = "http://www.shhongzhiyun.com/api/static/weixin/78.jpg";
        break;
      }
      case "79": {
        this.wxSrc = "http://www.shhongzhiyun.com/api/static/weixin/79.jpg";
        break;
      }
      case "92": {
        this.wxSrc = "http://www.shhongzhiyun.com/api/static/weixin/92.jpg";
        break;
      }
      case "114": {
        this.wxSrc = "http://www.shhongzhiyun.com/api/static/weixin/114.jpg";
        break;
      }
      default: {
        this.wxSrc = "http://www.shhongzhiyun.com/api/static/weixin/78.jpg";
        break;
      }
    }
  },
  mounted() {
    this.changeText();
  },
  watch: {
    showbackcover(curVal, oldVal) {
      let that = this;
      if (curVal == false) {
        if (this.msg == "领取成功") {
          const htmls = `是否要跳转首页`;
          MessageBox.confirm("", {
            message: htmls,
            title: "",
            showConfirmButton: true,
            showCancelButton: true,
            cancelButtonClass: "cancelButton",
            confirmButtonClass: "confirmButton",
            confirmButtonText: "跳转",
            cancelButtonText: "不跳转"
          })
            .then(action => {
              if (that.$route.query.companyId != null) {
                that.$router.push(
                  "/index?company=" + that.$route.query.companyId
                );
              } else {
                that.$router.push("/index");
              }
            })
            .catch(err => {
              if (err == "cancel") {
              }
            });
        }
      }
    }
  },
  methods: {
    changeText() {
      let that = this;
      this.msg = this.$router.history.current.query.text;
    },
    // 领取优惠券
    getcoupin() {
      let that = this;
      let couponIdArr = []
      this.coupon.forEach(e => {
        couponIdArr.push(e.id)
      });
      this.$http
        .post(
          "/api/product/coupon/customer/mall/insert?mobile=" +
            this.phone,couponIdArr
        )
        .then(res => {
          if (res.data.status == 200) {
            Toast("领取成功！");
            that.closebackcover();
            //     MessageBox.confirm('是否要跳转首页').then(() => {
            //    if(that.$route.query.companyId!=null){
            //         that.$router.push('/index?company='+that.$route.query.companyId);
            //     }
            //     else{
            //         that.$router.push('/index');
            //     }
            // });
          } else {
            Toast(res.data.msg);
            that.closebackcover();
          }
        })
        .catch(err => {
          Toast("领取失败");
          console.log(err);
        });
    },
    // 关闭覆盖层
    closebackcover() {
      this.showbackcover = false;
    }
  }
};
</script>
<style lang="less">
html,
body {
  height: 100%;
}
</style>
<style lang="less" scoped>
.backcover {
  width: 100%;
  height: 100%;
  display: flex;
  // justify-content:center;
  // flex-direction:column ;
  position: absolute;
  top: 0;
  right: 0;
  background: rgba(0, 0, 0, 0.5);
  .coupon_cover {
    width: 94%;
    height: 7.5rem;
    background-image: url(/static/images/coup.png);
    background-size: cover;
    position: absolute;
    left: 0;
    right: 0;
    top: 4rem;
    margin: auto;
    color: #fff;
    .money {
      font-size: 1rem;
      position: absolute;
      top: 0.5rem;
      left: 0.6rem;
    }
    .condition {
      font-size: 0.3rem;
      position: absolute;
      top: 2.1rem;
      left: 0.6rem;
    }
    .btn {
      width: 4rem;
      height: 1rem;
      position: absolute;
      left: 1.6rem;
      top: 5.9rem;
    }
  }
}
.ImgBox {
  width: 3rem;
  height: 3rem;
  margin: auto;
  background-size: 100% 100%;
  background-repeat: no-repeat;
  background-position: 50%;
}
#invite-result {
  .result-top {
    height: 5rem;
    background-size: 100% 100%;
    background-repeat: no-repeat;
    background-position: center center;
    background-image: url("/static/images/style1-inviteresult.png");
  }
  .result-explain {
    width: 5rem;
    font-size: 0.35rem;
    margin: 0.8rem auto;
    font-weight: bolder;
    color: #60c5fd;
    letter-spacing: 0.01rem;
  }
  .result-bottom {
    div {
      width: 3rem;
      height: 3rem;
      margin: auto;
      background-size: 100% 100%;
      background-repeat: no-repeat;
      background-position: center center;
    }
    p {
      letter-spacing: 0.02rem;
      font-size: 0.3rem;
      margin-top: 0.5rem;
      opacity: 0.6;
    }
  }
}
</style>
