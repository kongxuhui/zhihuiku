<template>
  <div class="mod-farm">
    <div class="farm_header">
      <span class="icon_name"></span>
      <el-dropdown @command="handleCommand" trigger="click">
        <span class="el-dropdown-link">
          {{farmName || '请选择地块'}}<i class="el-icon-caret-bottom" style="margin:0 5px;"></i>
        </span>
        <el-dropdown-menu slot="dropdown">
          <el-dropdown-item :command="item.id" v-for="(item, index) in farmOptions" :key="index">{{item.name}}</el-dropdown-item>
        </el-dropdown-menu>
      </el-dropdown>
      
      <div class="curFigure">
        <span class="figures"><i class="icon_temp"></i>空气温度：32℃</span>
        <span class="figures"><i class="icon_hum"></i>空气湿度：32%</span>
        <span class="figures"><i class="icon_beam"></i>光照：1.7umol/m2 s</span>
      </div>
    </div>
    <div class="farm_body">
      <!-- <p class="carets"><i class="el-icon-caret-left" @click="getLeft"></i><i class="el-icon-caret-right" @click="getRight"></i></p> -->
      <div class="pictures">
       <swiper :options="swiperOption">
        <swiper-slide v-for="(pic, pdex) in pictureData" :key="pdex">
          <div class="items">
            <img :src="pic.img" alt="">
            <div class="cover">
              <p><i class="name"></i>地块名：{{pic.name}}</p>
              <p><i class="plant"></i>植&nbsp;&nbsp;&nbsp;&nbsp;物：{{pic.plant}}</p>
              <p><i class="address"></i>{{pic.address}}</p>
            </div>
          </div>
        </swiper-slide>
        <div class="swiper-button-prev" slot="button-prev"></div>
        <div class="swiper-button-next" slot="button-next"></div>
      </swiper>
      </div>
    </div>
    <div class="farm_tabs">
      <el-tabs v-model="activeName" @tab-click="handleClick">
        <el-tab-pane label="实时数据监测" name="first">
          <div v-for="(item, index) in curData" :key="index" class="firstWrap">
            <p class="firstTitle">{{item.title}}</p>
            <el-table border :data="item.data" style="width: 100%">
              <el-table-column prop="depth" label="深度" align="center"></el-table-column>
              <el-table-column prop="temperature" label="土壤温度" align="center"></el-table-column>
              <el-table-column prop="humidity" label="土壤湿度" align="center"></el-table-column>
              <el-table-column prop="beam" label="土壤EC" align="center"></el-table-column>
            </el-table>
          </div>
        </el-tab-pane>
        <el-tab-pane label="图像数据" name="second">
          <el-row :gutter="10">
            <el-col :span="6" v-for="(item, index) in pictureData" :key="index" class="secondWrap">
              <p class="secondTitle">时间：{{item.date}}</p>
              <img :src="item.img" alt="">
            </el-col>
          </el-row>
        </el-tab-pane>
        <el-tab-pane label="设备数据" name="third">
          <div class="thirdWrap" v-for="(item, index) in  equipmentData" :key="index">
            <p class="thirdTitle">设备名：{{item.name}}</p>
            <p class="desc" v-for="(content, k) in item.desc" :key="k">
              <span>指标{{k + 1}}：{{content}}</span>
            </p>
          </div>
        </el-tab-pane>
      </el-tabs>
    </div>
    
  </div>
  
</template>

<script>
import '../../../../node_modules/swiper/dist/css/swiper.css'
import { swiper, swiperSlide } from 'vue-awesome-swiper'

  export default {
    data () {
      return {
        farmName: '',
        farmOptions: [{id: '1', name: '北京市朝阳区慈云寺远洋国际中心e座603室的分区'}],
        activeName: 'first',
        pictureData: [
          {img: 'http://b.hiphotos.baidu.com/image/h%3D300/sign=92afee66fd36afc3110c39658318eb85/908fa0ec08fa513db777cf78376d55fbb3fbd9b3.jpg', name: '武汉华中农大狮子山标本园', plant: '阳光玫瑰-葡萄', address: '武汉华中农大狮子山标本园', date: '2019年5月30日 23:21:15'},
          {img: 'https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1559063887429&di=beb4cd45b75ce9cdf3a93cc84edc0d10&imgtype=0&src=http%3A%2F%2Fhbimg.b0.upaiyun.com%2F0f8fc717586cd30c8bf27e72a1659d6c5ab91ab548ac3-JO1ia5_fw658', name: '武汉华中农大狮子山标本园', plant: '阳光玫瑰-葡萄', address: '武汉华中农大狮子山标本园', date: '2019年5月30日 23:21:15'},
          {img: 'https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1559658815&di=5f87b5af9891684db4893bdb47128643&imgtype=jpg&er=1&src=http%3A%2F%2Fpic46.nipic.com%2F20140819%2F165250_170537812000_2.jpg', name: '武汉华中农大狮子山标本园', plant: '阳光玫瑰-葡萄', address: '武汉华中农大狮子山标本园', date: '2019年5月30日 23:21:15'},
          {img: 'http://b.hiphotos.baidu.com/image/h%3D300/sign=92afee66fd36afc3110c39658318eb85/908fa0ec08fa513db777cf78376d55fbb3fbd9b3.jpg', name: '武汉华中农大狮子山标本园', plant: '阳光玫瑰-葡萄', address: '武汉华中农大狮子山标本园', date: '2019年5月30日 23:21:15'},
          {img: 'http://b.hiphotos.baidu.com/image/h%3D300/sign=92afee66fd36afc3110c39658318eb85/908fa0ec08fa513db777cf78376d55fbb3fbd9b3.jpg', name: '武汉华中农大狮子山标本园', plant: '阳光玫瑰-葡萄', address: '武汉华中农大狮子山标本园', date: '2019年5月30日 23:21:15'},
          {img: 'http://b.hiphotos.baidu.com/image/h%3D300/sign=92afee66fd36afc3110c39658318eb85/908fa0ec08fa513db777cf78376d55fbb3fbd9b3.jpg', name: '武汉华中农大狮子山标本园', plant: '阳光玫瑰-葡萄', address: '武汉华中农大狮子山标本园', date: '2019年5月30日 23:21:15'},
          {img: 'http://b.hiphotos.baidu.com/image/h%3D300/sign=92afee66fd36afc3110c39658318eb85/908fa0ec08fa513db777cf78376d55fbb3fbd9b3.jpg', name: '武汉华中农大狮子山标本园', plant: '阳光玫瑰-葡萄', address: '武汉华中农大狮子山标本园', date: '2019年5月30日 23:21:15'},
          {img: 'https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1559658815&di=5f87b5af9891684db4893bdb47128643&imgtype=jpg&er=1&src=http%3A%2F%2Fpic46.nipic.com%2F20140819%2F165250_170537812000_2.jpg', name: '武汉华中农大狮子山标本园', plant: '阳光玫瑰-葡萄', address: '武汉华中农大狮子山标本园', date: '2019年5月30日 23:21:15'},
        ],
        curData: [
          {title: '分区一', data: [{depth: '深度一', temperature: '32℃', humidity: '32%', beam: '1.7umol/m2 s'}, {depth: '深度二', temperature: '32℃', humidity: '32%', beam: '1.7umol/m2 s'}]},
          {title: '分区二', data: [{depth: '深度一', temperature: '32℃', humidity: '32%', beam: '1.7umol/m2 s'}, {depth: '深度二', temperature: '32℃', humidity: '32%', beam: '1.7umol/m2 s'}]}
        ],
        equipmentData: [
          {name: '设备一', desc: ['设备指标一设备指标一设备指标一设备指标一设备指标一设备指标一设备指标一', '设备指标二设备指标二设备指标二设备指标二设备指标二设备指标二']},
          {name: '设备二', desc: ['设备指标一设备指标一设备指标一设备指标一设备指标一设备指标一设备指标一', '设备指标二设备指标二设备指标二设备指标二设备指标二设备指标二']}
        ],
        swiperOption: {
          slidesPerView: 4,
          spaceBetween: 30,
          navigation: {
            nextEl: '.swiper-button-next',
            prevEl: '.swiper-button-prev'
          },
        }
      }
    },
    computed: {
      setWrapWidth () {
        return {
          width: this.pictureData.length * 30 + (this.pictureData.length - 1) * 10 + 'px'
        }
      }
    },
    components: {
      swiper,
      swiperSlide
    },
    activated () {
    },
    created () {
      this.handleCommand(this.farmOptions[0].id)
    },
    methods: {
      handleCommand (command) {
        this.farmOptions.forEach(item => {
          if (command === item.id) {
            this.farmName = item.name
          }
        })
      },
      getLeft(){
        console.log()
      },
      getRight(){

      },
      handleClick (tab, event) {
        console.log(tab, event)
      }
    }
  }
</script>
<style lang="scss" scoped>
  .mod-farm {
    display: flex;
    flex-direction: column;
    .farm_header {
      overflow: hidden;
      .icon_name {
        background-size: cover;
        background-image: url(~@/assets/img/地块.png);
        width: 15px;
        height: 15px;
        float: left;
        margin-right: 5px;
      }
      .curFigure {
        border-bottom: 1px solid #eee;
        overflow: hidden;
        padding: 12px 0;
        .figures {
          float: left;
          margin-right: 30px;
          overflow: hidden;
          .icon_temp {
            background-size: auto 18px;
            background-repeat: no-repeat;
            background-position: center center;
            background-image: url(~@/assets/img/空气温度.png);
            width: 18px;
            height: 18px;
            float: left;
            margin-right: 5px;
          }
          .icon_hum {
            background-size: auto 18px;
            background-repeat: no-repeat;
            background-position: center center;
            background-image: url(~@/assets/img/空气湿度.png);
            width: 18px;
            height: 18px;
            float: left;
            margin-right: 5px;
          }
          .icon_beam {
            background-size: auto 18px;
            background-repeat: no-repeat;
            background-position: center center;
            background-image: url(~@/assets/img/光照.png);
            width: 18px;
            height: 18px;
            float: left;
            margin-right: 5px;
          }
        }
      }
    }
    .farm_body {
      padding-bottom: 20px;
      border-bottom: 1px solid #ddd;
      .carets {
        text-align: right;
        margin: 5px 0;
        i {
          margin: 0 5px;
          font-size: 16px;
          cursor: pointer;
        }
      }
      .pictures {
        overflow-y: hidden;
        overflow-x: auto;
        padding-top: 30px;
        &::-webkit-scrollbar {
          display: none;
        }
        .swiper-container{
          position: static!important;
        }
        .swiper-button-prev{
            background-image: url('~@/assets/img/date_left.png')!important;
            background-size: 100% 100%;
            left: auto!important;
            right: 70px!important;
            top: 88px;
            margin-top: 0;

        }
        .swiper-button-next{
            background-image: url('~@/assets/img/date_right.png')!important;
            background-size: 100% 100%;
            left: auto!important;
            right: 40px!important;
            top: 88px;
            margin-top: 0;

        }
        .swiper-button-prev, .swiper-button-next{
          width: 12px;
          height: 12px;
        }
        .wrap {
          height: 200px;
        }
        .items {
          float: left;
          width: 300px;
          height: 200px;
          border: 1px solid #ddd;
          border-radius: 2px;
          margin-right: 10px;
          position: relative;
          &:last-child {
            margin-right: 0;
          }
          img {
            width: 100%;
            height: 100%;
            border-radius: 2px;
          }
          .cover {
            position: absolute;
            left: 0;
            bottom: 0;
            right: 0;
            background-size: cover;
            background-image: url(~@/assets/img/jianbian.png);
            color: #fff;
            font-size: 12px;
            height: 100px;
            padding-top: 35px;
            p {
              margin: 0;
              line-height: 20px;
              overflow: hidden;
              padding-left: 4px;
              i {
                float: left;
                width: 12px;
                height: 12px;
                margin: 4px 5px;
              }
              .name {
                background-size: auto 10px;
                background-repeat: no-repeat;
                background-position: center center;
                background-image: url(~@/assets/img/地块名.png);
              }
              .plant {
                background-size: auto 10px;
                background-repeat: no-repeat;
                background-position: center center;
                background-image: url(~@/assets/img/植物.png);
              }
              .address {
                background-size: auto 10px;
                background-repeat: no-repeat;
                background-position: center center;
                background-image: url(~@/assets/img/地标.png);
              }
            }
          }
        }
      }
    }
    .farm_tabs {
      flex: 1;
      padding-top: 10px;
      /deep/ .el-tabs {
        .el-tabs__header {
          margin-bottom: 5px;
          .el-tabs__nav-wrap::after {
            height: 0;
          }
          .el-tabs__item {
            font-size: 14px !important;
            border: none !important;
            margin-right: 0 !important;
            padding: 0 15px 0 0 !important;
            height: 30px;
            line-height: 30px;
          }
          .is-active {
            color: #009C8E !important;
            background: #fff !important;
          }
        }
        .el-tabs__content {
          padding: 0 15px;
          .firstWrap {
            margin-bottom: 10px;
            &:last-child {
              margin-bottom: 0;
            }
            .firstTitle {
              margin: 0;
              line-height: 35px;
            }
            .el-table {
              th, td {
                padding: 4px 0 !important;
              }
            }
          }
          .secondWrap {
            overflow: hidden;
            height: 200px;
            .secondTitle {
              margin: 0;
              line-height: 30px;
              font-size: 12px;
            }
            img {
              width: 100%;
              height: 160px
            }
          }
          .thirdWrap {
            margin-bottom: 10px;
            p {
              margin: 0;
              color: #4D5C5C;
            }
            .thirdTitle {
              line-height: 35px;
              font-weight: bold;
            }
            .desc {
              line-height: 25px;
              font-size: 13px;
            }
          }
        }
      }
    }
  }
</style>

