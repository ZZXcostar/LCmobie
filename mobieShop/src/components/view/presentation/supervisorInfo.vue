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
         <div class="box" v-if="dataInfo.workList">
            <div class="title">
                <p>管家信息</p>
            </div>
            <div class="text">
                <span>管家姓名：{{dataInfo.workList.name}}</span>
                <span>管家电话：{{dataInfo.workList.phone}}</span>
                <div><span>验收时间：</span><p>{{dataInfo.workList.createTime}}</p></div>
            </div>
        </div>
        <div class="box">
            <mt-navbar class="nevbar" v-model="selected">
                <mt-tab-item id="1">合格</mt-tab-item>
                <mt-tab-item id="2">不合格</mt-tab-item>
            </mt-navbar>
            <mt-tab-container v-model="selected" class="isimg">
                <mt-tab-container-item id="1">
                    <mt-cell :title="'检测说明：'+datalist.entryReportStandards[0].instructions" />
                    <img v-if="selected==2" v-for="item in datalist.entryReportStandards[0].imgs" @click="clickImg($event)" :src="item" alt="">
                </mt-tab-container-item>
                <mt-tab-container-item id="2">
                    <mt-cell :title="'检测说明：'+datalist.entryReportStandards[0].instructions" />
                    <mt-cell :title="'施工隐患：'+datalist.entryReportStandards[0].hdanger" />
                    <mt-cell :title="'解决方案：'+datalist.entryReportStandards[0].solution" />
                    <mt-cell :title="'解决方法：'+datalist.entryReportStandards[0].solutionf" />
                    <img v-if="selected==2" v-for="item in datalist.entryReportStandards[0].imgs" @click="clickImg($event)" :src="item" alt="">
                </mt-tab-container-item>
            </mt-tab-container>
            
        </div>
        <big-img v-if="showImg" @clickit="viewImg" :imgSrc="imgSrc"></big-img>
        <div class="logo"></div>
    </div>  
</template>
<script>
import { Navbar, TabItem } from 'mint-ui';
import { Toast } from 'mint-ui'
// Vue.component(Navbar.name, Navbar);
// Vue.component(TabItem.name, TabItem);
import BigImg from './bigImg.vue';
export default {
  data() {
    return {
        selected: '',
        dataInfo:'',
        datalist:'',
        showImg:false,
        imgSrc: ''
    };
  },
  created() {
    //设置头部文字
    var that=this
    this.$root.$emit("header", this.$route.query.name);
    var data=sessionStorage.getItem("presentationInfo")
    data=JSON.parse(data)
    this.dataInfo=data
    let id=this.$route.query.id
    console.log(data)
    this.$http({
            url: "/api/public/entryreport/queryByIds",
            method: "post",
            data:[id],
        }).then((res) => {
            var data1=res.data.info.list[0]
            console.log(data1)
            if(res.data.status!=200){
              Toast(res.data.msg);
            }else{
              data1.entryReportStandards[0].imgs=that.listSet(data1.entryReportStandards[0].imgs)
              for(let i in data1.entryReportStandards[0]){
                if(data1.entryReportStandards[0][i]==null){
                  data1.entryReportStandards[0][i]='无'
                }
              }
              if(data1.entryReportStandards[0].isService==0){
                that.selected='2'
              }else if(data1.entryReportStandards[0].isService==1 || data1.entryReportStandards[0].isService==2){
                that.selected='1'
              }
              that.datalist=data1
            }    
        });
  },
  components: {
    'big-img':BigImg
  },
  methods: {
    onSwipeLeft() {
      //左滑上一页
      this.$router.go(-1);
    },
    listSet(data){   //将字符串数据转换成数组
        var arr=data.split(',')
        arr.pop()
        for(let i in arr){
          arr[i]='http://101.89.175.155/api/'+arr[i]
        }
        return arr
    },
    clickImg(e) {
        this.showImg = true;
        console.log(e.currentTarget.src)
        // 获取当前图片地址
        this.imgSrc = e.currentTarget.src;
    },
    viewImg(){
        this.showImg = false;
    },
  }
};
</script>
<style scoped>
.contain {
  overflow: hidden;
  background-color: #f5f5f5;
}
.box {
  width: 100%;
  box-sizing: border-box;
  margin-bottom: 0.15rem;
  overflow: hidden;
  background: #ffffff;
  text-align: left;
}
.box .title {
  width: 100%;
  color: #333;
  border-bottom: 0.01rem solid #e6e6e6;
  padding: 0.23rem 0 0.23rem 0.33rem;
}
.box .title p {
  border-left: 0.06rem solid #11b786;
  padding-left: 0.09rem;
  font-size: 0.28rem;
  text-align: left;
}
.box .text {
  line-height: 0.35rem;
  color: #666666;
  font-size: 0.26rem;
  padding: 0.1rem 0;
}
.box .text > span {
  margin-left: 0.45rem;
  padding: 0.1rem 0;
  display: block;
  width: 3.2rem;
  float: left;
}
.box .text div > span {
  display: block;
  float: left;
}
.box .text div {
  clear: both;
  margin-left: 0.45rem;
  padding: 0.1rem 0;
  overflow: hidden;
}
.box .text div p {
  width: 5.54rem;
  float: left;
}
.project {
  font-size: 0.26rem;
  padding: 0.2rem 0.45rem;
  box-sizing: border-box;
  line-height: 0.4rem;
  border-bottom: 0.01rem solid #e6e6e6;
  overflow: hidden;
}
.project p {
  margin-bottom: 0.26rem;
}
.project img {
  width: 46%;
  float: left;
  margin: 0.1rem 1%;
}
.nevbar{
    color: #11b786 !important;
    border-bottom: 0.02rem solid #eeeeee  !important;
}
.logo {
  width: 100%;
  height: 1rem;
  background-image: url("../../../../static/images/logoB.png");
  background-size: 2.97rem 0.48rem;
  background-position: center;
  background-repeat: no-repeat;
}
.isimg{
  font-size: 0.26rem !important;
}
.isimg img{
  width: 50%;
}
</style>


