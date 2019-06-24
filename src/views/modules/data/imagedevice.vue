<template>
  <div class="mod-survey">
        <div class="search">
          <el-row :gutter="10">
            <el-form ref="form" :model="form">
              <el-col :span="3">
                <el-form-item label="">
                  <el-select v-model="form.company" placeholder="请选择企业" @change="companyChange">
                    <el-option :label="item.name" :value="item.id + ''" v-for="(item, index) in companyList" :key="index"></el-option>
                  </el-select>
                </el-form-item>
              </el-col>
              <el-col :span="3">
                <el-form-item label="">
                  <el-select v-model="form.plots" placeholder="请选择地块" @change="plotsChange">
                    <el-option :label="item.fieldName" :value="item.id + ''" v-for="(item, index) in plotsList" :key="index"></el-option>
                  </el-select>
                </el-form-item>
              </el-col>
              <el-col :span="3">
                <el-form-item label="">
                  <el-select v-model="form.area" placeholder="请选择分区" @change="areaChange">
                    <el-option :label="item.name" :value="item.id + ''" v-for="(item, index) in areaList" :key="index"></el-option>
                  </el-select>
                </el-form-item>
              </el-col>
              <el-col :span="3">
                <el-form-item label="">
                  <el-select v-model="form.place" placeholder="请选择地点">
                    <el-option :label="item.name" :value="item.id + ''" v-for="(item, index) in placeList" :key="index"></el-option>
                  </el-select>
                </el-form-item>
              </el-col>
            </el-form>
          </el-row>
        </div>
        <div>
          <div class="tlPick">
            <span @click="handleDateDialog" class="dateBtn">日期</span>
            <span class="date_left" @click="HandleLeft"></span>
            <VueSlideBar
              v-model="slider.value"
              :data="slider.data"
              :range="slider.range"
              :labelStyles="{ color: '#4a4a4a', backgroundColor: '#4a4a4a' }"
              :processStyle="{ backgroundColor: '#d8d8d8' }"
              @callbackRange="callbackRange()">
              <template slot="tooltip" slot-scope="tooltip">
                <span class="quanquan"></span>
              </template>
            </VueSlideBar>
            <span class="date_right" @click="HandleRight"></span>
            <div style="margin-top:20px;margin-bottom:20px;">
              <div class="dateData">
                <span>{{startTime}}</span>
                <span>---</span>
                <span>{{endTime}}</span>
                <span>  数据</span>
              </div>
            </div>
            
            <h2>Value: {{slider.value}}</h2>
            <h2>Label: {{rangeValue.label}}</h2>
          </div>
        </div>        
        
        <div class="clearfix">
          <div class="img">
            <el-carousel height="540px" indicator-position="outside">
              <el-carousel-item v-for="(pic, pdex) in pictureData" :key="pdex">
                <div class="items">
                  <img :src="pic.img" alt="">
                  <div class="cover">
                    <p><i class="name"></i>地块名：{{pic.name}}</p>
                    <p><i class="plant"></i>植&nbsp;&nbsp;&nbsp;&nbsp;物：{{pic.plant}}</p>
                    <p><i class="address"></i>{{pic.address}}</p>
                  </div>
                </div>
              </el-carousel-item>
            </el-carousel>
          </div>

          <div class="charts">
            <el-tabs v-model="activeName" @tab-click="handleClick"  class="survey-tabs">
              <el-tab-pane label="图像数据" name="first">图像数据</el-tab-pane>
              <el-tab-pane label="土壤数据" name="second">
                <!-- 土壤数据 -->
                <!-- <div class="charts"> -->
                    <div id="J_chartBox" class="chart-box"></div>
                <!-- </div> -->
              </el-tab-pane>
            </el-tabs>
          </div>
        </div>
        

        <!-- 日期弹框 -->
        <el-dialog
          title="提示"
          :visible.sync="dateDialog"
          width="30%"
          center>
          <el-date-picker
            style="width:100%"
            v-model="datePick"
            type="daterange"
            align="right"
            format="yyyy 年 MM 月 dd 日"
            unlink-panels
            range-separator="至"
            start-placeholder="开始日期"
            end-placeholder="结束日期"
            :picker-options="pickerOptions">
          </el-date-picker>
          <span slot="footer" class="dialog-footer">
            <el-button @click="centerDialogVisible = false">取 消</el-button>
            <el-button type="primary" @click="search">确 定</el-button>
          </span>
        </el-dialog>
  </div>

</template>

<script>
  import echarts from 'echarts'
  import VueSlideBar from 'vue-slide-bar'
import { constants } from 'fs';
  export default {
    data () {
      return {
        activeName:'second',
        images:[
          {name:'@/assets/img/001.png'},
          {name:'@/assets/img/002.png'},
          {name:'@/assets/img/003.png'},
        ],
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
        startTime:'',
        endTime:'',
        datePick:'',
        pickerOptions: {
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
        dateDialog:false,
        rangeValue: {},
        slider: {
          value: '',
          data: [],
          range: [],
        },
        // activeName: '1',
        form: {
          company: '',
          plots: '',
          area: '',
          datatype: '',
          place: '',
          date: []
        },
        threshold: {
          min: '',
          max: ''
        },
        chartSet: null,
        companyList: [],
        plotsList: [],
        areaList: [],
        datatypeList: [],
        placeList: [],
        series:[],
        dateList: [
            {
              date: '2019-6-21',
              name: '生长情况',
              type: 'bar',
              data: [2.0, 4.9, 7.0, 23.2, 25.6, 76.7, 135.6, 162.2, 32.6, 20.0, 6.4, 3.3],
              markPoint: {
                data: [
                  {type: 'max', name: '最大值'},
                  {type: 'min', name: '最小值'}
                ]
              },
              markLine: {
                data: [
                  {type: 'average', name: '平均值'},
                  {type: 'max', name: '平均值'}
                ]
              }
            },{
              date: '2019-6-20',
              name: '生长情况',
              type: 'bar',
              data: [2.0, 4.9, 7.0, 23.2, 25.6, 76.7, 135.6, 130.2, 32.6, 20.0, 6.4, 3.3],
              markPoint: {
                data: [
                  {type: 'max', name: '最大值'},
                  {type: 'min', name: '最小值'}
                ]
              },
              markLine: {
                data: [
                  {type: 'average', name: '平均值'},
                  {type: 'max', name: '平均值'}
                ]
              }
            },{
              date: '2019-6-19',
              name: '生长情况',
              type: 'bar',
              data: [2.0, 4.9, 7.0, 23.2, 25.6, 76.7, 135.6, 100.2, 32.6, 20.0, 6.4, 3.3],
              markPoint: {
                data: [
                  {type: 'max', name: '最大值'},
                  {type: 'min', name: '最小值'}
                ]
              },
              markLine: {
                data: [
                  {type: 'average', name: '平均值'},
                  {type: 'max', name: '平均值'}
                ]
              }
            }
          ]
      }
    },
    components: {
      VueSlideBar
    },
    activited () {
      this.$nextTick(() => {
        this.initCharts()
      })
    },
    mounted () {
      this.getCompanyList()
      this.getDatatypeList()
      this.startTime = getSevenDay(new Date());
      this.endTime = getCurrenrtDay(new Date());
      console.log(getSevenDay(new Date()));
      this.slider.value = getCurrenrtDay(new Date());
      this.slider.data[6] = this.slider.value;
      var obj = {label: this.slider.value};
      this.slider.range.unshift(obj);
      for(var i = 0; i < 6; i++ ){
        var date = this.slider.data[6 - i];
        this.slider.data[6 - i - 1] = getPrevDay(date);
        var obj = {label: this.slider.data[6 - i - 1]};
        this.slider.range.unshift(obj);
      }
      this.dateList.forEach((val) => {
        if(val.date == this.slider.value){
          this.series=val;
          this.$nextTick(() => {
            this.initCharts(this.series)
          })
        }
      })
      
    },
    methods: {
      handleDateDialog(){
        this.dateDialog = true;
      },
      
      getCompanyList () {
        this.$http({
          url: this.$http.adornUrl('/sys/customer/selectAll'),
          method: 'get'
        }).then(({data}) => {
          if (data && data.code === 0) {
            this.companyList = data.list
          } else {
            this.companyList = []
          }
        })
      },
      companyChange (val) {
        this.getPlotsList(val)
      },
      getPlotsList (val) {
        this.$http({
          url: this.$http.adornUrl('/field/field/selectField/' + val),
          method: 'get'
        }).then(({data}) => {
          if (data && data.code === 0) {
            this.plotsList = data.fieldList
          } else {
            this.plotsList = []
          }
        })
      },
      plotsChange (val) {
        this.getAreaList(val)
      },
      getAreaList (val) {
        this.$http({
          url: this.$http.adornUrl('/field/area/selectArea/' + val),
          method: 'get'
        }).then(({data}) => {
          if (data && data.code === 0) {
            this.areaList = data.fieldList
          } else {
            this.areaList = []
          }
        })
      },
      areaChange (val) {

      },
      getDatatypeList () {
        this.$http({
          url: this.$http.adornUrl('/equipment/channeltype/dataTypes'),
          method: 'get'
        }).then(({data}) => {
          if (data && data.code === 0) {
            this.datatypeList = data.list
          } else {
            this.datatypeList = []
          }
        })
      },
      datatypeChange (val) {

      },
      getPlaceList () {
        this.$http({
          url: this.$http.adornUrl('/equipment/location/2/3'),
          method: 'get'
        }).then(({data}) => {
          if (data && data.code === 0) {
            this.placeList = data.list
          } else {
            this.placeList = []
          }
        })
      },
      handleClick(tab, event) {
        console.log(tab, event);
      },
      //日期查询事件
      search () {
        this.dateDialog = false;
        this.slider.range = [];
        this.slider.data = [];
        this.slider.value = getCurrenrtDay(this.datePick[0]);
        this.startTime = getYmd(getCurrenrtDay(this.datePick[0]));
        this.endTime = getYmd(getCurrenrtDay(this.datePick[1]));
        var num = DateMinus(this.datePick[0],this.datePick[1]);
        var d = new Date(this.datePick[0]);
        this.slider.data[0] = d.getFullYear() + '-' + (d.getMonth() + 1) + '-' + d.getDate();
        for(var i = 0; i <= num; i++ ){
          var date = this.slider.data[i];
          if(i != num){
            this.slider.data[i + 1] = getNextDay(date);
          }
          if(i % 10 == 0){
            var isHide = true;
          }else{
            var isHide = false;
          }
          var obj = {label: this.slider.data[i],isHide: isHide};
          this.slider.range.push(obj);
        }
      },
      //左侧箭头事件
      HandleLeft(){
        if(this.slider.value !== this.slider.range[0]){
          this.slider.value = getPrevDay(new Date(this.slider.value));
        }else{
          return false;
        }
      },
      //右侧箭头事件
      HandleRight(){
        if(this.slider.value !== this.slider.range[this.slider.range.length - 1]){
          this.slider.value = getNextDay(new Date(this.slider.value));
        }else{
          return false;
        }
      },
      download () {
        window.open('http://b.hiphotos.baidu.com/image/h%3D300/sign=92afee66fd36afc3110c39658318eb85/908fa0ec08fa513db777cf78376d55fbb3fbd9b3.jpg')
      },
      initCharts (series) {
        var option = {
          title: {
            text: '某地区农作物生长情况',
            subtext: ''
          },
          tooltip: {
            trigger: 'axis'
          },
          legend: {
            data: ['生长水平']
          },
          toolbox: {
            show: true,
            feature: {
              dataView: {show: false, readOnly: false},
              magicType: {show: true, type: ['line', 'bar']},
              restore: {show: false},
              saveAsImage: {show: false}
            }
          },
          calculable: true,
          xAxis: [
            {
              type: 'category',
              data: ['1月', '2月', '3月', '4月', '5月', '6月', '7月', '8月', '9月', '10月', '11月', '12月']
            }
          ],
          yAxis: [
            {
              type: 'value'
            }
          ],
          series: series
        }
        this.chartSet = echarts.init(document.getElementById('J_chartBox'))
        this.chartSet.setOption(option)
        window.addEventListener('resize', () => {
          this.chartSet.resize()
        })
      },
      callbackRange (val) {
        setTimeout(()=>{
          this.series = [];
          this.dateList.forEach((val) => {
            if(val.date == this.slider.value){
              this.series=val;
              this.$nextTick(() => {
                this.initCharts(this.series)
              })
            }
          })
        },200)
      },
    }
  }
  function DateMinus(date1,date2){
  　var sdate = new Date(date1); 
  　var now = new Date(date2); 
  　var days = now.getTime() - sdate.getTime(); 
  　var day = parseInt(days / (1000 * 60 * 60 * 24)); 
  　return day;
  }
  function getNextDay(d){
      d = new Date(d);
      d = +d + 1000*60*60*24;
      d = new Date(d);
      return d.getFullYear()+"-"+(d.getMonth()+1)+"-"+d.getDate();
  }
  function getPrevDay(d){
      d = new Date(d);
      d = d - 1000*60*60*24;
      d = new Date(d);
      return d.getFullYear()+"-"+(d.getMonth()+1)+"-"+d.getDate();
  }
  function getCurrenrtDay(d){
      d = new Date(d);
      return d.getFullYear()+"-"+(d.getMonth()+1)+"-"+d.getDate();
  }
  function getSevenDay(d){
      d = new Date(d);
      d = d - 1000*60*60*24*6;
      d = new Date(d);
      return d.getFullYear()+"-"+(d.getMonth()+1)+"-"+d.getDate();
  }
   function getYmd(d){
      var arr = d.split("-");
      return (arr[0]+"年"+arr[1]+"月"+arr[2]+"日")
  }
</script>
<style lang="scss" scoped>
.mod-survey {
  /deep/.survey-tabs{
    .el-tabs__header {
      margin-bottom: 10px;
      .el-tabs__active-bar {
        height: 2px;
      }
      .el-tabs__nav-wrap::after {
        height: 0;
      }
      .el-tabs__item {
        // font-size: 14px !important;
        border: none !important;
        // margin-right: 0 !important;
        // padding: 0 20px !important;
        // height: 30px;
        // line-height: 30px;
      }
      .el-tabs__nav{
        .is-active {
          color: #009C8E !important;
          background: #fff !important;
        }
      }
    }
  }
  .search{
    padding-bottom: 20px;
    border-bottom: 2px solid #C0C4CC;
  }
  .el-form {
      .el-form-item {
        margin-bottom: 0;
      }
      .el-date-editor .el-range__close-icon {
        display: none;
      }
      .el-range-editor--medium .el-range-separator {
        width: 22px;
      }
    }
    .setArea {
      margin: 10px 0;
    }
    .chart-box {
      width: 100%;
      height: 500px;
    }
    .img{
      width: 50%;
      height: 600px;
      float: left;
      border: 1px solid #E4E7ED;
      padding: 20px 20px 10px;
      .el-carousel__item{
        // text-align: center;
      }
      .items {
          border-radius: 2px;
          position: relative;
          text-align: center;
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
            padding-top: 20px;
            text-align: left;
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
    .charts{
      width: 50%;
      height: 600px;
      float: right;
      border: 1px solid #E4E7ED;
      border-left: 0px solid #ccc;
      padding: 20px 20px 10px;
    }
    .tlPick{
      width: 80%;
      margin: 0 auto;
      position: relative;

      .dateBtn{
        position: absolute;
        left: -80px;
        top:16px;
        cursor: pointer;
      }
      .date_left{
        position: absolute;
        left: -32px;
        top:22px;
        width: 20px;
        height: 20px;
        background: url('~@/assets/img/date_left.png') no-repeat;
        background-size: 100% 100%;
        cursor: pointer;
      }
      .date_right{
        top: 22px;
        width: 20px;
        height: 20px;
        position: absolute;
        right: -32px;
        background: url('~@/assets/img/date_right.png') no-repeat;
        background-size: 100% 100%;
        cursor: pointer;
      }
    }
}
.quanquan{
  position: absolute;
  top:22px;
  display: block;
  width: 16px;
  height: 16px;
  border: 2px solid #39BEA1;
  border-radius: 8px;
  background-color: #fff;
}
.tlPick .vue-slide-bar-component{
    padding-top: 30px!important;
    min-height: auto!important;
}
.dateData{
  width: 280px;
  height: 26px;
  margin: 0 auto;
  line-height: 26px;
  background: url('~@/assets/img/rili.png') no-repeat left center;
  padding-left: 34px;
  background-size: 26px 26px;

  // .dateDatabgc{
  //   display: inline-block;
  //   height: 26px;
  //   width: 26px;
  //   background-size: 100% 100%;
  // }
}
.clearfix:after{    /*在.clearfix后边追加一个隐藏的block,带一个clear:both属性*/
    content: "";
    display: block;  /*block宽度会横向填充满屏幕，在父元素的最后追加一个height:0，占满屏幕的看不见的细长条*/
    line-height: 0;
    clear: both;  /*这个最下边细长条左右两边都清除float*/
}
</style>

