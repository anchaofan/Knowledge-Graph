<template>
    <div class="detail-container">
        <!-- 两栏布局 -->
        <div class="painting-cnt">
            <div class="left-cnt">
                <img :src="imgsrc" class="pic" alt="pic1" />

                <el-card class="box-card">
                    <div class="title"><i class="el-icon-brush"></i> 基本信息</div>
                      <div class="author-picture-cnt link" @click="routeToPainter(painterDetail.id)">
                    </div>

                  <div class="item"> <el-tag type="success">名称</el-tag> <span class="font">{{ sname }}</span></div>
                  <div class="item"> <el-tag type="warning">地址</el-tag> <span class="font">{{ dizhi }}</span></div>
                  <div class="item"> <el-tag type="danger">电话</el-tag> <span class="font">{{ phone }}</span></div>


                    <el-divider content-position="center" class="divider">旅游小贴士</el-divider>

                    <div class="painter-description-cnt">
                        <div class="empty-cnt" v-if="false">
                            <img src="../../assets/empty.png" alt="empty" class="empty"/>
                            <p class="empty-text">作者描述为空，我们正在努力获取 :)</p>
                        </div>

                        <div class="desc">
                          了解当地的民俗民风和规矩，泰国小孩的头摸不得，印度的小孩您抱不得。像这些异国他乡的规矩，在到人家国家旅游的时候，您还真应当了解一些。否则，人家并不会因为你是外国人，就不再责怪你。比如到泰国，您去之前一定要弄清楚了这么一些习俗：进入寺庙要脱鞋，女士见到僧侣要避让，游览大皇宫女士不能穿短裙和凉鞋。有不良卫生习惯的人千万要当心，在新加坡街头随地吐痰、乱扔废物，高额的罚款会让你脑袋大一圈。
                        </div>
                    </div>
                </el-card>
            </div>
            <div class="right-cnt">
                <el-card class="box-card">
                    <div class="title"><i class="el-icon-reading"></i> 简介 </div>
                    <div class="item"> <el-tag>等级</el-tag> <span class="font">{{level}}</span></div>
                    <div class="item"> <el-tag type="success">票价</el-tag> <span class="font">{{money}}</span></div>
                    <div class="item link" @click="routeToPainter"> <el-tag type="warning">详情链接</el-tag>
                      <a>
                        <span class="font">{{urls}}</span>
                      </a>
                    </div>

                    <el-divider content-position="center" class="divider">景点介绍</el-divider>

                    <div class="painting-description-cnt">
                        <div class="desc">
                          {{description}}.....
                        </div>
                    </div>

                </el-card>

                <div id="test-canvas" style="width:100%; height: 500px;">

                </div>
            </div>
        </div>

    </div>
</template>

<script>
    import Chart from 'roc-charts';
    //加入配置文件

    export default {
        name: "index",

        data() {
            return {
                paintingId: 1,
                chart: null,
                paintingVisible: false,
                updatePaintingDescriptionForm: {
                    description: ''
                },
                sname:"",
                sdata:[],
                imgsrc: null,
                dizhi: null,
                phone: null,
                level: null,
                urls: null,
                money: null,
                description: null,
                province: null,
                paintDetail: {},
                painterDetail: {},
            }
        },

        mounted() {
          this.sname = this.$route.params.id;
          this.sdata = this.$route.params.data;
          this.imgsrc = this.sdata[0].image;
          this.dizhi = this.sdata[0].address;
          this.phone = this.sdata[0].phone;
          this.level = this.sdata[0].level;
          this.urls = this.sdata[0].urls;
          this.money = this.sdata[0].money;
          this.description = this.sdata[0].description;
          this.province = this.sdata[0].province;
          this.initGraph();
        },
        created() {

        },
        methods: {
          getParam(){
            this.sname = this.$route.params.id;
            this.sdata = this.$route.params.data;
          },

            /**
             * @description 初始化知识图谱
             * @returns {boolean}
             */
            initGraph() {
                const config = {
                    core: {
                        initPlugin: true
                    },
                };

                const chart = new Chart({
                    id: 'test-canvas',  // 绘制图谱 dom 的 id
                    type: 'force',  // 图谱类型
                    data: {
                      nodes: [
                        {
                          id:1,
                          name: this.sname
                        },
                        {
                          id:2,
                          name: this.province
                        },
                        {
                          id:3,
                          name: this.dizhi
                        },
                        {
                          id:4,
                          name: this.level
                        },
                        {
                          id:5,
                          name: this.phone
                        },
                        {
                          id:6,
                          name: this.money
                        }

                      ],
                      links:[
                        {
                          from: 1,
                          to:2,
                          type: "城市"
                        },
                        {
                          from:2,
                          to:3,
                          type: "位置"
                        },
                        {
                          from:1,
                          to:4,
                          type: "等级"
                        },
                        {
                          from:1,
                          to:5,
                          type: "电话"
                        },
                        {
                          from:1,
                          to:6,
                          type: "门票"
                        }
                      ]
                    },  // 图谱数据
                });

                chart.init(config);  // 调用 init 方法绘图，配置项可选

                document.getElementsByClassName('searchNodeContainer-roc')[0].remove();

            },

            /**
             * @description 打开画作编辑器
             */
            openPaintingDescriptionEditor() {
                this.paintingVisible = true;
            },
            /**
             * @description 跳转到作者详情页
             * @param painterId
             */
            routeToPainter(painterId) {
                // eslint-disable-next-line
                console.log(painterId);
                this.$router.push('/painterDetail/'+painterId);
            }
        }
    }
</script>

<style lang="scss" >
    .detail-container {
        margin-left: 150px;
        margin-right: 150px;
        margin-top: 30px;

        .painting-description-textarea {
            margin-top: 15px;
            textarea {
                background: rgba(0,0,0,0.1) !important;

            }
        }

        .painting-cnt {


            .left-cnt {
                width: 300px;
                float: left;

                .pic {
                    width: 100%;
                    border-radius: 4px;
                }

                .box-card {
                    margin-top: 20px;
                    .title {
                        font-size: 18px;
                        font-weight: bold;
                        margin-bottom: 20px;
                        text-align: center;
                    }

                    .item {
                        font-weight: bold;
                        margin-bottom: 20px;

                        .font {
                            margin-left: 20px;
                            color: #F2F8FE;
                        }
                    }

                    .link {
                        cursor: pointer;
                    }

                    .author-picture-cnt {
                        text-align: center;
                        margin-bottom: 20px;

                        .author-picture {
                            width: 100px;
                            border-radius: 6px;
                        }
                    }

                    .divider {
                        margin-top: 30px;
                        background: #606266;
                        .el-divider__text {
                            background: #606266;
                        }
                    }

                    .painter-description-cnt {

                        .empty-cnt {
                            text-align: center;
                            .empty {
                                width: 150px;
                                opacity: 0.5;
                            }
                            .empty-text {
                                color: #606266;
                                font-size: 14px;
                            }
                        }

                    }

                }
            }

            .right-cnt {
                margin-left: 300px;
                padding-left: 30px;
                padding-right: 30px;

                .box-card {

                    .link {
                        cursor: pointer;
                    }

                    .title {
                        font-size: 26px;
                        font-weight: bold;
                        margin-bottom: 20px;
                    }

                    .item {
                        font-weight: bold;
                        margin-bottom: 20px;

                        .font {
                            margin-left: 20px;
                            color: #F2F8FE;
                        }
                    }

                    .divider {
                        margin-top: 30px;
                        background: #606266;
                        .el-divider__text {
                            background: #606266;
                        }
                    }

                    .painting-description-cnt {

                        .empty-cnt {
                            text-align: center;
                            .empty {
                                width: 150px;
                                opacity: 0.5;
                            }
                            .empty-text {
                                color: #606266;
                                font-size: 14px;
                            }
                        }

                    }

                }

                #test-canvas {
                    margin-top: 15px;
                    margin-bottom: 15px;
                    border-radius: 6px;
                    background: #27262F;
                }

            }
        }
    }
</style>
