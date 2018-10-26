<template>
    <div class='contain'>
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
                <div v-if="datalist.receptionTime!=null"><span>验收时间：</span><p>{{datalist.receptionTime}}</p></div>
            </div>
        </div>
        <div class="box" v-for="item in datalist.entryReportStandards">
            <div class="title">
                <p>{{"【"+item.items+"】"+item.point}}</p>
                <span v-if="item.selected==1" class="green">合格</span>
                <span v-if="item.selected==2" class="red">不合格</span>
            </div>
            <!-- <div class="title">
                <span v-if="item.selected==1">合格</span>
                <span v-if="item.selected==2">不合格</span>
            </div> -->
            <!-- <mt-navbar class="nevbar" v-model="item.selected">
                <mt-tab-item id="1" v-if="item.selected==1">合格</mt-tab-item>
                <mt-tab-item id="2" v-if="item.selected==2">不合格</mt-tab-item>
            </mt-navbar> -->
            <mt-tab-container v-model="item.selected" class="isimg">
                <mt-tab-container-item id="1">
                    <mt-cell v-if="item.isService==1" :title="'检测说明：'+item.instructions" />
                    <mt-cell v-if="item.isService==2" :title="'无需验收说明：'+item.instructions" />
                    <div class="imglist" v-if="item.isService==1" v-for="ite in item.imgs" @click="clickImg($event)" :style="'background-image:url('+ite+')'" :data-img='ite'></div>
                    <!-- <img v-if="selected==1" v-for="item in datalist.entryReportStandards[0].imgs" @click="clickImg($event)" :src="item"> -->
                </mt-tab-container-item>
                <mt-tab-container-item id="2">
                    <mt-cell :title="'检测说明：'+item.instructions" />
                    <mt-cell :title="'施工隐患：'+item.hdanger" />
                    <mt-cell :title="'解决方案：'+item.solution" />
                    <mt-cell :title="'解决方法：'+item.solutionf" />
                    <div class="imglist" v-for="ite in item.imgs" @click="clickImg($event)" :style="'background-image:url('+ite+')'" :data-img='ite'></div>
                    <!-- <img v-if="selected==2" v-for="item in datalist.entryReportStandards[0].imgs" @click="clickImg($event)" :src="item"> -->
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
    console.log(data)
    if(data.workList!=null){
      data.workList.createTime=data.workList.createTime.split('.')
      data.workList.createTime=data.workList.createTime[0]
    }
    this.dataInfo=data
    let id=this.$route.query.id
    console.log(that.$route.query.id)
    // console.log(data)
    this.$http({
            url: "/api/public/entryreport/queryByIds",
            method: "post",
            data:[id],
        }).then((res) => {
          console.log(res.data.info)
            var data1=res.data.info.list[0]
            if(res.data.status!=200){
              Toast(res.data.msg);
            }else{
              for(let j in data1.entryReportStandards){
                // console.log(data1.entryReportStandards[j].imgs)
                if(data1.entryReportStandards[j].imgs!=null){
                  data1.entryReportStandards[j].imgs=that.listSet(data1.entryReportStandards[j].imgs)
                }
                for(let i in data1.entryReportStandards[j]){
                  if(data1.entryReportStandards[j][i]==null){
                    data1.entryReportStandards[j][i]='无'
                  }
                }
                if(data1.entryReportStandards[j].isService==0){
                  data1.entryReportStandards[j].selected='2'
                }else if(data1.entryReportStandards[j].isService==1 || data1.entryReportStandards[j].isService==2){
                  data1.entryReportStandards[j].selected='1'
                }
              }
              console.log(data1)
              that.datalist=data1
            }    
        });
  },
  components: {
    'big-img':BigImg
  },
  methods: {
    // onSwipeRight() {
    //   //左滑上一页
    //   this.$router.go(-1);
    // },
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
        // console.log(e.target.dataset.img)  
        // 获取当前图片地址
        this.imgSrc = e.target.dataset.img;
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
.contain .box {
  width: 100%;
  box-sizing: border-box;
  margin-bottom: 0.15rem;
  overflow: hidden;
  background: #ffffff;
  text-align: left;
}
.contain .box .title {
  width: 100%;
  color: #333;
  border-bottom: 0.01rem solid #e6e6e6;
  padding: 0.23rem 0 0.23rem 0.33rem;
  position: relative;
}
.contain .box .title span{
  position: absolute;
  right: 0.7rem;
  font-size: 0.28rem;
  top: 0;
  padding: 0.23rem 0rem;
}
.contain .box .title .red{
  color: #d7434d
}
.contain .box .title .green{
  color: #11b786
}
.contain .box .title p {
  border-left: 0.06rem solid #11b786;
  padding-left: 0.09rem;
  font-size: 0.28rem;
  text-align: left;
  padding-right: 2rem;
  box-sizing: border-box;
  overflow: hidden;
  text-overflow:ellipsis;
  white-space: nowrap;
}
.contain .box .text {
  line-height: 0.35rem;
  color: #666666;
  font-size: 0.26rem;
  padding: 0.1rem 0;
}
.contain .box .text > span {
  margin-left: 0.45rem;
  padding: 0.1rem 0;
  display: block;
  width: 3.2rem;
  float: left;
}
.contain .box .text div > span {
  display: block;
  float: left;
}
.contain .box .text div {
  clear: both;
  margin-left: 0.45rem;
  padding: 0.1rem 0;
  overflow: hidden;
}
.contain .box .text div p {
  width: 5.54rem;
  float: left;
}
.contain .project {
  font-size: 0.26rem;
  padding: 0.2rem 0.45rem;
  box-sizing: border-box;
  line-height: 0.4rem;
  border-bottom: 0.01rem solid #e6e6e6;
  overflow: hidden;
}
.contain .project p {
  margin-bottom: 0.26rem;
}
.contain .project img {
  width: 46%;
  float: left;
  margin: 0.1rem 1%;
}
.contain .nevbar{
    color: #11b786 !important;
    text-align: left;
    border-bottom: 0.02rem solid #eeeeee  !important;
}
.contain .logo {
  width: 100%;
  height: 1rem;
  background-image: url("../../../../static/images/logoB.png");
  background-size: 2.97rem 0.48rem;
  background-position: center;
  background-repeat: no-repeat;
}
.contain .isimg{
  font-size: 0.26rem !important;
  width: 98% !important;
  margin: 0 auto;
}
.contain .isimg .imglist{
  width: 48%;
  height: 3.5rem;
  margin: 0.1rem 1%;
  float: left;
  background-position:center;
  background-size:cover 
}
.contain .isimg img{
  width: 48%;
  margin: 0.1rem 1%;
}
</style>


