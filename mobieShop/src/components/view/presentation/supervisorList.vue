<template>
    <div class='contain' v-touch:right="onSwipeRight">
        <div class="header"></div>
        <div class="content">
            <p @click="toSupInfo(item.id,item.reportname,index)" v-for="(item,index) in datalist"><span>{{item.reportname}}  ({{item.okCount==null? '0':item.okCount}}/{{item.reportCount}})</span><i class="icon iconfont icon-youjiantou1"></i></p>
        </div>
    </div>
</template>

<script>
import { Toast } from 'mint-ui'
export default {
    data(){
        return {
           datalist:'',
           dataInfo:''
        }
    },
    created(){
        //设置头部文字
        var that=this
        this.$root.$emit('header', '监理报告列表');
        var data=sessionStorage.getItem("presentationInfo")
        data=JSON.parse(data)
        this.dataInfo=data
        let id=this.$route.query.id
        this.$http({
            url: "/api/public/entryreport/queryMapByProjectIds",
            method: "post",
            data:[id],
        }).then((res) => {
            var data=res.data.info
            console.log(res)
            if(res.data.status==200){
                that.datalist=data
            }else{
                Toast(res.data.msg);
            }    
        });
    },
    methods:{
        onSwipeRight(){ //左滑上一页
            // this.$router.go(-1)
            this.$router.back(-1)
        },
        toSupInfo(id,name,index){
            console.log(id)
            var that=this
            if(that.datalist[index].okCount==that.datalist[index].reportCount){
                that.$router.push('/supervisorInfo?company=92&id='+id+'&name='+name);
            }else if(that.datalist[index].reportCount==1){
                this.$http({
                url: "/api/public/entryreport/queryByIds",
                method: "post",
                data:[id],
                }).then((res) => {
                    var data1=res.data.info.list[0]
                    if(data1.entryReportStandards[0].isService==3){
                        Toast('此节点还未有报告上传');
                    }else if(data1.entryReportStandards[0].isService==2){
                        Toast('此节点是无需验收节点');
                    } else{
                        that.$router.push('/supervisorInfo?company=92&id='+id+'&name='+name);
                    }    
                });
            }else{
                Toast('此节点还未有完整报告上传');
            }
        }
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
    background-image: url('../../../../static/images/presentation3.png');
    background-size: cover;
    background-position: center;
    overflow: hidden;
    box-shadow: 0 0 0.1rem 0.01rem #cccccc;
}
.contain .content{
    width: 100%;
    padding-top: 0.05rem;
    text-align: left;
}
.contain .content p{
    width: 100%;
    height: 0.8rem;
    line-height: 0.8rem;
    border-bottom: 0.01rem solid #eaeaea;
    font-size: 0.28rem;
    color: #333333;
    text-align: left;
    text-indent: 0.52rem;
    position: relative;
}
.contain .content p i{
    position: absolute;
    top: 50%;
    transform: translateY(-50%);
    right: 0.3rem;
}
</style>


