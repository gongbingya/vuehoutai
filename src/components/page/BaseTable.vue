<template>
    <div class="table">
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item><i class="el-icon-lx-cascades"></i> 基础表格</el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        <div class="container">
            <div class="handle-box">
                <el-select v-model="game" placeholder="dks" class="handle-select mr10" @change="changeGame" >
                    <el-option v-for="(item,index) in allGame" :key="index" :label="item.gameName" :value="item.gameName"></el-option> 
                </el-select>
                <el-select v-model="version" placeholder="版本" class="handle-select mr10" @change="changeVersion" v-for="(item,index) in allGame" :key="index">
                     <el-option v-for="(val,num) in item.version" :key="num" :label="val" :value="val"></el-option> 
                     <el-option key="99" label="版本" value=""></el-option>
                </el-select>
                <el-select v-model="channel" placeholder="渠道" class="handle-select mr10" @change="changeChannel" v-for="(item,index) in allGame" :key="index">
                     <el-option v-for="(val,num) in item.channel" :key="num" :label="val" :value="val"></el-option> 
                    <el-option key="99" label="渠道" value=""></el-option>
                </el-select>
                 <el-select  v-model="gender " placeholder="性别" class="handle-select mr10" @change="changeGender">
                    <el-option key="1" label="男" value="1"></el-option>
                    <el-option key="2" label="女" value="2"></el-option>
                    <el-option key="3" label="未知" value="0"></el-option> 
                    <el-option key="99" label="性别" value=""></el-option> 
                </el-select>
                   
                <el-date-picker
                    v-model="date"
                    type="daterange"
                    start-placeholder="开始日期"
                    end-placeholder="结束日期" 
                    @change = "changeDate">
                </el-date-picker>

                <el-select  v-for="(item,index) in allGame" v-model="shareImg" placeholder="分享图" class="handle-select mr10" @change="changeSharImg" :key="index">
                    <el-option v-for="(val,num) in item.allShareImg" :key="num" :label="val" :value="val"></el-option> 
                    <el-option key="3" label="分享" value=""></el-option> 
                </el-select>
              
                              
                <div style="margin: 15px 0;"></div> 
            </div>
            <el-table :data="results" border class="table" ref="multipleTable" @selection-change="handleSelectionChange">
                <el-table-column prop="date" label="日期" >
                </el-table-column>
                <el-table-column prop="version" label="版本" width="120" v-if="!version">
                </el-table-column>
                <el-table-column prop="channel" label="渠道"  v-if="!channel">
                </el-table-column>
                <el-table-column prop="sex" label="性别"  v-if="!sex">
                </el-table-column>
                <el-table-column prop="shareImg" label="分享图"  v-if="!shareImg">
                </el-table-column>
                <el-table-column prop="newUser" label="新注册用户" >
                </el-table-column>
                <el-table-column prop="newLogin" label="新登录用户" >
                </el-table-column>
                <el-table-column prop="aveOnlineTime" label="首日平均在线时长">
                </el-table-column>
                <el-table-column prop="aveEnterCount" label="平均访问次数">
                </el-table-column>
                <el-table-column prop="aveGameTime" label="平均有效时长" >
                </el-table-column>
                <el-table-column prop="shareNum" label="分享人数">
                </el-table-column>
                <el-table-column prop="aveShareCount" label="平均分享次数">
                </el-table-column>
                <el-table-column prop="shareUserAveShareCount" label="分享用户的平均分享次数">
                </el-table-column>
                <el-table-column prop="kkk" label="K因子">
                </el-table-column>
            </el-table>
            <div class="pagination">
                <el-pagination background @current-change="handleCurrentChange" layout="prev, pager,next,sizes" :page-size="1" :total="1000">
                </el-pagination>
            </div>
        </div>

    </div>
</template>

<script>
    export default {
        name: 'basetable',
        data() {
            return {
               allGame : [],
                results : [],
                data : [],
                cur_page: 1,
                multipleSelection: [],
                game: '',
                version:"",
                channel: "",
                gender : "",
                sex : "",
                date : "",
                shareImg : "",
                startTime :"",
                endTime : "",
                isShow : true,
                cur_page: 1,
                img : "",
                pickerOptions2: {
                      shortcuts: [{
                        text: '最近一周',
                        onClick(picker) {
                          const end = new Date();
                          const start = new Date();
                          start.setTime(start.getTime() - 3600 * 1000 * 24 * 7);
                          picker.$emit('pick', [start, end]);
                        }
                      }, {
                        text: '最近一个月',
                        onClick(picker) {
                          const end = new Date();
                          const start = new Date();
                          start.setTime(start.getTime() - 3600 * 1000 * 24 * 30);
                          picker.$emit('pick', [start, end]);
                        }
                      }, {
                        text: '最近三个月',
                        onClick(picker) {
                          const end = new Date();
                          const start = new Date();
                          start.setTime(start.getTime() - 3600 * 1000 * 24 * 90);
                          picker.$emit('pick', [start, end]);
                        }
                      }]
                    },
                   
            }
        },
        created() {
            this.getData();
            this.getAllGame();
        },
        computed: {
         
        },
        methods: {
            setTable(){

                let gameName = this.gameName ?  this.gameName : "dks";
                let version1 = this.version ?  "&version="+this.version : "";
                let channel = this.channel ?  "&channel="+this.channel : "";
                let gender = this.gender ? "&gender=" + this.gender : "";
                let startTime= this.startTime ? "&startTime=" + this.startTime : "";
                let endTime = this.endTime ? "&endTime=" + this.endTime : "";
                let shareImg = this.shareImg ? "&shareImg" + this.shareTime : "";
                console.log(channel);
                this.$axios.get("http://warp-tech.cn:82/back-collector/firstDayData?gameName="+gameName+version1+channel+gender+startTime+endTime+shareImg, {
                    page: this.cur_page
                }).then((res) => {
                    this.results = res.data;
                     console.log(res.data);
                })
            },
            // 分页导航
            handleCurrentChange(val) {
                this.cur_page = val;
                console.log(val);
                // this.getData();
            }, 
            getData() {
                this.$axios.get("http://warp-tech.cn:82/back-collector/firstDayData?gameName=dks", {
                    page: this.cur_page
                }).then((res) => {
                    this.results = res.data;
                     // console.log(res.data);
                })
            },
            getAllGame(){
                this.$axios.get("http://warp-tech.cn:82/back-collector/allGameInfo").then((res) => {
                    this.allGame = res.data;
                     console.log(res.data);
                })
            },

            handleSelectionChange(val) {
                this.multipleSelection = val;
            },
            changeGame(val){
                 this.gameName = val;

                this.setTable();
            },
            changeVersion(val){
                this.version = val;
                 this.isShow = false;
                 if(!val){
                   return this.getData();
                 };
                this.setTable();

            },
            changeChannel(val){
                this.channel = val;
                if(!val){
                  return this.getData();
                 };
                this.setTable();

            },
            changeGender(val){
               this.gender = val;
                if(!val){
                  return this.getData();
                 };
                this.setTable();
            },
            changeSharImg(val){
               this.sharImg = val;
            },
            changeDate(val){
                if(!val){
                  return this.getData();
                 };
                this.startTime = this.setDate(val[0]);
                this.endTime = this.setDate(val[1]);
                this.setTable();
                console.log(this.startTime,this.endTime);
            },

            setDate(a){
                var now = new Date(a);
                var yy = now.getFullYear();      //年
                var mm = now.getMonth() + 1;     //月
                var dd = now.getDate();          //日
                var hh = now.getHours();         //时
                var ii = now.getMinutes();       //分
                var ss = now.getSeconds();       //秒
                var clock = yy + "-";
                if(mm < 10) clock += "0";
                clock += mm + "-";
                if(dd < 10) clock += "0";
                clock += dd + "T";
                if(hh < 10) clock += "0";
                clock += hh + ":";
                if (ii < 10) clock += '0'; 
                clock += ii + ":";
                if (ss < 10) clock += '0'; 
                clock += ss;
                return clock;
            },
            setImg(){
                this.img = "<<img src='https://petgame.warp-tech.cn/shushu_v110/shareImg/xinwen.png' alt='分享图'' />"
            }
        }
    }

</script>

<style scoped>
    .handle-box {
        margin-bottom: 20px;
    }

    .handle-select {
        width: 120px;
    }

    .handle-input {
        width: 300px;
        display: inline-block;
    }
    .del-dialog-cnt{
        font-size: 16px;
        text-align: center
    }
    .table{
        width: 100%;
        font-size: 14px;
    }
    .red{
        color: #ff0000;
    }
</style>
