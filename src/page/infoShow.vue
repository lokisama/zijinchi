<template>
    <div class="info_container">
       
        <!--资金信息栏-->
        <el-row class="data_row row" :gutter="10">
                <el-col :span="6">
                    <div class="area">
                    <p class="title"><i class="fa fa-cog"></i>W总资产</p>
                    <p class="num"><span class="symbol">￥</span>180,000.00</p>
                    <a class="detail dbgcolor">查看详情&nbsp;&nbsp;<i class="el-icon-d-arrow-right"></i></a>
                </div>
                </el-col>
                <el-col :span="6">
                <div class="area">
                    <p class="title"><i class="fa fa-cog"></i>待收资金</p>
                    <p class="num"><span class="symbol">￥</span>5000.00</p>
                    <a class="detail zbgcolor">查看详情&nbsp;&nbsp;<i class="el-icon-d-arrow-right"></i></a>
                </div>
                </el-col>
                <el-col :span="6">
                    <div class="area">
                    <p class="title"><i class="fa fa-cog"></i>待付资金</p>
                    <p class="num"><span class="symbol">￥</span>3050.00</p>
                    <a class="detail dbgcolor">查看详情&nbsp;&nbsp;<i class="el-icon-d-arrow-right"></i></a>
                </div>
                </el-col>
                <el-col :span="6">
                <div class="area">
                    <p class="title"><i class="fa fa-cog"></i>共赠送股份</p>
                    <p class="num">1032 <span class="symbol">股</span></p>
                    <a class="detail zbgcolor">查看详情&nbsp;&nbsp;<i class="el-icon-d-arrow-right"></i></a>
                </div>
                </el-col>
        </el-row>
        <!--广告信息栏-->
        <!-- <el-row class="ban_row row" :gutter="10">
            <el-col :span="24">
                <div class="area">
                    <ul class="banarea">
                        <li v-for="(item,index) in bandata" :key="index">
                            <a :href="item.url" target="_blank" :style="{backgroundPosition:0 +' -'+ item.data+'px'}"></a>
                        </li>
                   </ul>
                </div>
            </el-col>
        </el-row> -->

    </div>
</template>

<script>

    export default {
        data(){
            return {
               bandata:[
                   {url:'http://www.baidu.com',data:300},
                   {url:'http://www.sina.com',data:200},
                   {url:'http://www.360.com',data:100},
                   {url:'http://www.weibo.com',data:0},
                   {url:'http://www.hao123.com',data:100},
               ],
               uploadaction:'/api/userInfo/uploadimg',
               fileList:[], // name,url
            }
        },
      	mounted() {
         
	    },
        methods: {
            // 超过文件上传个数提示
            handleExceed(files, fileList) {
               this.$message.warning(`当前限制选择 1 个文件，本次选择了 ${files.length} 个文件，共选择了 ${files.length + fileList.length} 个文件`);
            },
            beforeAvatarUpload(file) {
                const isJPG = file.type === 'image/jpeg';
                const isLt2M = file.size / 1024 / 1024 < 2;

                if (!isJPG) {
                     this.$message.error('上传头像图片只能是 JPG 格式!');
                }
                if (!isLt2M) {
                     this.$message.error('上传头像图片大小不能超过 2MB!');
                }
                return isJPG && isLt2M;
           },
           handSuccess(file){

           },
           handError(file){

           },

        },
    }
</script>

<style lang="less" scoped>
    .info_container{
        padding: 10px;
        margin: 0 10px;
        overflow: auto;
    }
    .row{
        margin:20px;
    }
   .info_row{
        .area{
           border:1px solid #dfdfdf;
           height:178px;
           overflow:hidden;
            .imgarea{
                text-align:center;
                padding:10px;
                img {
                    width:120px;
                    height:120px;
                    border-radius: 50%;
                }
            }
            .namearea{
                padding:10px;
                font-size:14px;
                p{
                    line-height:24px;
                }
                .awards{
                    text-align:center;
                    width:100%;
                    height:30px;
                    line-height:30px;
                    cursor: pointer;
                    background-color: #3bc5ff;
                    border:1px solid #3bc5ff;
                    color: white;
                    display: block;
                }
                .awards:hover{
                    background-color: #f9c855;
                    border:1px solid #f9c855;
                }
            }
            .moneyarea{
                padding:10px;
                float:left;
                .title{
                    font-size:15px;
                    font-weight:bolder;
                    line-height:50px;
                    .money{
                        color:#c10000;
                        margin-right:5px;
                    }
                    .yuan{
                            font-weight:100;
                            font-size:13px;
                    }
                }
                
            }
            .chongzhiarea{
                    padding:10px;
                    text-align:center;
                    img{
                        width: 80%;
                        height: 40px;
                    }
                    .chongzhi{
                        margin-top:10px;
                    }
            }
            .tipsarea{
                clear:both;
                font-size:14px;
                padding:10px;
                padding-top:15px;
                a{
                    color:#3bc5ff;
                }
            }
            .dataarea{
                 padding:10px;
                 text-align:center;
                 font-size:14px;
                .gtitle{
                    width:100%;
                    height:30px;
                    line-height:30px;
                    cursor: pointer;
                    background-color: #3bc5ff;
                    color: white;
                    display: block;
                }
                .gdataarea{
                    padding-left: 25px;
                    p{
                        line-height:38px;
                    }
                    .num{
                        font-weight:bolder;
                        color:#c10000;
                    }
                    .title{
                        color:#3bc5ff;
                    }
                    .gdata{
                        margin:10px;
                    }
                }
                .morearea{
                    a{
                        color:#3bc5ff;
                    }
                }
            }
        }
   } 
   .data_row{
       font-size:18px;
       .area{
           border:1px solid #dfdfdf;
           height:200px;
           overflow:hidden;
           padding:10px;
           text-align:center;
           font-weight:bolder;
            .fa{
                margin-right:10px;
            }
            .symbol{
                font-size: 16px;
                font-style: italic;
                font-family: "microsoft yahei";
                margin-right: 10px;
                color:#232323;
                font-weight:100;
            }
           p{
               line-height:70px;
           }
           .num{
               color:#c10000;
           }
           .detail{
               margin-top:5px;
               font-size:14px;
               text-align: center;
               width: 100%;
                height: 30px;
                line-height: 30px;
                cursor: pointer;
                color: white;
                display: block;
                border:1px solid #3bc5ff;
           }
           .dbgcolor{
              background-color: #3bc5ff;
           }
           .zbgcolor{
               color:#3bc5ff;
           }
           .dbgcolor:hover{
                background-color: #fff;
                color:#3bc5ff
           }
           .zbgcolor:hover{
                background-color: #3bc5ff;
                color:#fff;
           }
       }
   }
   .ban_row{
       .area{
           padding-left:4%;
          .banarea{
            li{
                float: left;
                width: 200px;
                margin-right: 10px;
                a{
                    background: url(../assets/img/pro_map.gif?v=1.0) no-repeat;
                    width: 200px;
                    height: 50px;
                    display: block;
                }
            }
          }
       }
      
   }
</style>


