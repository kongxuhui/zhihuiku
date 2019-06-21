<template>
  <div class="mod-survey">
    <el-tabs v-model="activeName" @tab-click="handleClick" class="survey-tabs">
      <el-tab-pane :label="item.label" :name="item.id" v-for="item in tabData" :key="item.id">
        <div class="search">
          <el-row :gutter="10">
            <el-form ref="form" :model="form">
              <el-col :span="3">
                <el-form-item label="">
                  <el-select v-model="form.company" placeholder="企业名" @change="companyChange">
                    <el-option :label="item.name" :value="item.id + ''" v-for="(item, index) in companyList" :key="index"></el-option>
                  </el-select>
                </el-form-item>
              </el-col>
              <el-col :span="3">
                <el-form-item label="">
                  <el-select v-model="form.plots" placeholder="地块名" @change="plotsChange">
                    <el-option :label="item.fieldName" :value="item.id + ''" v-for="(item, index) in plotsList" :key="index"></el-option>
                  </el-select>
                </el-form-item>
              </el-col>
              <el-col :span="3">
                <el-form-item label="">
                  <el-select v-model="form.area" placeholder="分区名" @change="areaChange">
                    <el-option :label="item.name" :value="item.id + ''" v-for="(item, index) in areaList" :key="index"></el-option>
                  </el-select>
                </el-form-item>
              </el-col>
              <el-col :span="3">
                <el-form-item label="">
                  <el-select v-model="form.datatype" placeholder="数据类型" @change="datatypeChange">
                    <el-option :label="item.name" :value="item.id + ''" v-for="(item, index) in datatypeList" :key="index"></el-option>
                  </el-select>
                </el-form-item>
              </el-col>
              <el-col :span="3">
                <el-form-item label="">
                  <el-select v-model="form.place" placeholder="地点">
                    <el-option :label="item.name" :value="item.id + ''" v-for="(item, index) in placeList" :key="index"></el-option>
                  </el-select>
                </el-form-item>
              </el-col>
              <el-col :span="6">
                <el-form-item label="">
                  <el-date-picker
                    style="width:100%"
                    v-model="form.date"
                    type="datetimerange"
                    range-separator="至"
                    start-placeholder="开始日期"
                    end-placeholder="结束日期">
                  </el-date-picker>
                </el-form-item>
              </el-col>
              <el-col :span="3">
                <el-button type="primary" size="small" @click="search">查询</el-button>
                <el-button type="primary" size="small" @click="download">下载</el-button>
              </el-col>
            </el-form>
          </el-row>
        </div>
        <div class="tlPick">
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
          <h2>Value: {{slider.value}}</h2>
          <h2>Label: {{rangeValue.label}}</h2>
        </div>
        <div class="charts">
          <div class="setArea">
            <el-popover
              placement="right"
              width="160"
              trigger="click"
              v-model="visiblePopover">
              <p>
                <el-input v-model="threshold.min"></el-input>
                <span>-</span>
                <el-input v-model="threshold.max"></el-input>
                <el-button @click="visiblePopover = false">保存</el-button>
              </p>
              <el-button slot="reference" type="text">阈值设置</el-button>
            </el-popover>
          </div>
          <div>
            <div class="img">
              <el-carousel indicator-position="outside">
                <el-carousel-item v-for="item in 4" :key="item">
                  <h3>{{ item }}</h3>
                </el-carousel-item>
              </el-carousel>
            </div>
            <div id="J_chartBox" class="chart-box"></div>
          </div>
        </div>
      </el-tab-pane>
    </el-tabs>
  </div>
</template>

<script>
  import echarts from 'echarts'
  import VueSlideBar from 'vue-slide-bar'
  export default {
    data () {
      return {
        rangeValue: {},
        slider: {
          value: '2019-6-28',
          data: [],
          range: [{label:''}],
        },
        activeName: '1',
        visiblePopover: false,
        tabData: [
          {label: '监测数据一', id: '1'},
          {label: '监测数据二', id: '2'},
          {label: '监测数据三', id: '3'},
          {label: '监测数据四', id: '4'}
        ],
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
        placeList: []
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
      this.$nextTick(() => {
        this.initCharts()
      })
      this.getCompanyList()
      this.getDatatypeList()
      // this.getPlaceList()
    },
    methods: {
      callbackRange (val) {
        console.log(1);
        // this.rangeValue = val
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
      handleClick (tab) {
        this.form.company = ''
        this.form.plots = ''
        this.form.area = ''
        this.form.datatype = ''
        this.form.place = ''
        this.form.date = []
        this.form.srartDate = ''
        this.form.endDate = ''
        this.search()
        this.$nextTick(() => {
          this.initCharts()
        })
        // console.log(tab)
      },
      search () {
        this.slider.range = [];
        if (this.form.date.length) {
          this.form.srartDate = this.form.date[0]
          this.form.endDate = this.form.date[1]
        }
        var num = DateMinus(this.form.date[0],this.form.date[1]);
        var d = new Date(this.form.date[0]);
        this.slider.data[0] = d.getFullYear() + '-' + (d.getMonth() + 1) + '-' + d.getDate();
        for(var i = 0; i < num; i++ ){
          var date = this.slider.data[i];
          this.slider.data[i + 1] = getNextDay(date);
          if(i % 2 == 0){
            var isHide = true;
          }else{
            var isHide = false;
          }
          var obj = {label: this.slider.data[i],isHide: isHide};
          this.slider.range.push(obj);
        }
        var l = this.slider.range.shift();
        console.log(this.slider.range)
      },
      download () {
        window.open('http://b.hiphotos.baidu.com/image/h%3D300/sign=92afee66fd36afc3110c39658318eb85/908fa0ec08fa513db777cf78376d55fbb3fbd9b3.jpg')
      },
      initCharts () {
        var option = {
          title: {
            text: '某地区农作物生长情况',
            subtext: ''
          },
          tooltip: {
            trigger: 'axis'
          },
          legend: {
            data: ['生长情况']
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
          series: [
            {
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
            }
          ]
        }
        this.chartSet = echarts.init(document.getElementById('J_chartBox'))
        this.chartSet.setOption(option)
        window.addEventListener('resize', () => {
          this.chartSet.resize()
        })
      }
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
</script>
<style lang="scss" scoped>
.mod-survey {
  /deep/.survey-tabs {
    .el-tabs__header {
      margin-bottom: 10px;
      .el-tabs__active-bar {
        height: 2px;
      }
      .el-tabs__nav-wrap::after {
        height: 0;
      }
      .el-tabs__item {
        font-size: 14px !important;
        border: none !important;
        margin-right: 0 !important;
        padding: 0 8px !important;
        height: 30px;
        line-height: 30px;
      }
      .is-active {
        color: #009C8E !important;
        background: #fff !important;
      }
    }
    .el-tabs__content {
      /deep/ .el-form {
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
        float: right;
        width: 50%;
        height: 600px;
      }
      .img{
        width: 50%;
        height: 600px;
        float: left;
      }
      .tlPick{
        width: 80%;
        margin: 0 auto;
      }
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
</style>

