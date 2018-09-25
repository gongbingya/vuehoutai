<template>
    <div>
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item><i class="el-icon-lx-favor"></i> schart图表</el-breadcrumb-item>
            </el-breadcrumb>
        </div>

        
        <div class="container1">
            <el-select v-model="game" placeholder="dks" class="handle-select mr10" @change="getGameName" >
                    <el-option v-for="(item,index) in allGame" :key="index" :label="item.gameName" :value="item.gameName"></el-option> 
                </el-select>
                <el-select v-model="version1" placeholder="版本" class="handle-select mr10" @change="changeBanBeng" v-for="(item,index) in allGame" :key="index">
                     <el-option v-for="(val,num) in item.version" :key="num" :label="val" :value="val"></el-option> 
                     <el-option key="99" label="版本" value=""></el-option>
             </el-select>
        </div>

        <div class="container"  id="main" style="width: 600px;height: 400px;"> 
        </div>


    </div>
</template>

<script>
    import echarts from 'echarts';
    export default {
        name: '',
        created(){
          this.getData();
          this.getAllGame();
        },
        mounted(){
            this.$nextTick(function() {
                this.drawPie('main')
            })
        },
        update(){
          this.handleChange();

        },
        components: {
            echarts
        },
         data() {
          return {
            suju  : "",
            allGame:"",
            game : '',
            gameName:"",
            version1 : "",
           charts: '',
                opinion:['直接访问','邮件营销','联盟广告','视频广告','搜索引擎'],
                opinionData:[
                  {value:335, name:'直接访问'},
                  {value:310, name:'邮件营销'},
                  {value:234, name:'联盟广告'},
                  {value:135, name:'视频广告'},
                  {value:1548, name:'搜索引擎'}
                ]
            }
        },
        methods: {
          // 发送请求获取数据
          getData() {
            //获取改变的游戏名或版本  
            // 如果没有 默认“dks”
            let gameName = this.gameName ?  this.gameName : "dks";
            let version1 = this.version1 ?  "&version="+this.version1 : "";
            console.log(version1)

            this.$axios.get("http://warp-tech.cn:82/back-collector/userPortrait?gameName="+gameName+version1, {
                  page: this.cur_page
                }).then((res) => {
                    this.suju = res.data;
                    this.setData();
                })
          },
           getAllGame(){
                this.$axios.get("http://warp-tech.cn:82/back-collector/allGameInfo").then((res) => {
                    this.allGame= res.data;
                     console.log(res.data);
                    
                })
            },
          setData(){
             
          },
          getGameName(val){
            this.gameName = val;
            this.getData();
          },
          changeBanBeng(val){
            this.version1 = val;
             this.getData();
          }, 
           drawPie(id){
               this.charts = echarts.init(document.getElementById(id))
               this.charts.setOption({
                 tooltip: {
                   trigger: 'item',
                   formatter: '{a}<br/>{b}:{c} ({d}%)'
                 },
                 legend: {
                   orient: 'vertical',
                   x: 'left',
                   data:this.opinion
                 },
                 series: [
                   {
                     name:'访问来源',
                     type:'pie',
                     radius:['50%','70%'],
                     avoidLabelOverlap: false,
                     label: {
                       normal: {
                         show: false,
                         position: 'center'
                       },
                       emphasis: {
                         show: true,
                         textStyle: {
                           fontSize: '30',
                           fontWeight: 'blod'
                         }
                       }
                     },
                     labelLine: {
                       normal: {
                         show: false
                       }
                     },
                     data:this.opinionData
                   }
                 ]
               })
            }
      },
      computed:{

      }
    }
</script>

<style scoped>
.schart-box{
    display: inline-block;
    margin: 20px;
}
h4{
  margin-top: 20px;
 
}
.container{
  float: left;

}
    .schart{
        width: 550px;
        height: 400px;
    }
    .content-title{
        clear: both;
        font-weight: 400;
        line-height: 50px;
        margin: 10px 0;
        font-size: 22px;
        color: #1f2f3d;
    }
    
</style>


