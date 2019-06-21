<template>
  <div class="mod-survey">
    <el-tabs v-model="activeName" @tab-click="handleClick" class="survey-tabs">
      <el-tab-pane :label="item.label" :name="item.id" v-for="item in tabData" :key="item">
        <div class="search">
          <el-row :gutter="10">
            <el-form ref="form" :model="form">
              <el-col :span="3">
                <el-form-item label="">
                  <el-select v-model="form.company" placeholder="企业名">
                    <el-option label="区域一" value="shanghai"></el-option>
                  </el-select>
                </el-form-item>
              </el-col>
              <el-col :span="3">
                <el-form-item label="">
                  <el-select v-model="form.plots" placeholder="地块名">
                    <el-option label="区域一" value="shanghai"></el-option>
                  </el-select>
                </el-form-item>
              </el-col>
              <el-col :span="3">
                <el-form-item label="">
                  <el-select v-model="form.area" placeholder="分区名">
                    <el-option label="区域一" value="shanghai"></el-option>
                  </el-select>
                </el-form-item>
              </el-col>
              <el-col :span="3">
                <el-form-item label="">
                  <el-select v-model="form.datatype" placeholder="数据类型">
                    <el-option label="区域一" value="shanghai"></el-option>
                  </el-select>
                </el-form-item>
              </el-col>
              <el-col :span="3">
                <el-form-item label="">
                  <el-select v-model="form.place" placeholder="地点">
                    <el-option label="区域一" value="shanghai"></el-option>
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
        <div class="charts">
          <p class="setArea">
            <el-popover
              placement="right"
              width="160"
              v-model="visiblePopover">
              <p>
                <el-input v-model="min"></el-input>
                <span>-</span>
                <el-input v-model="max"></el-input>
                <el-button slot="reference">保存</el-button>
              </p>
              <el-button slot="reference" type="text">阈值设置</el-button>
            </el-popover>
          </p>
          <div id="J_chartBox" class="chart-box"></div>
        </div>
      </el-tab-pane>
    </el-tabs>
  </div>
</template>

<script>
  import echarts from 'echarts'
  export default {
    data () {
      return {
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
        chartSet: null
      }
    },
    components: {
    },
    activated () {
    },
    methods: {
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
        console.log(tab)
      },
      search () {
        if (this.form.date.length) {
          this.form.srartDate = this.form.date[0]
          this.form.endDate = this.form.date[1]
        }
      },
      download () {
        window.open('http://b.hiphotos.baidu.com/image/h%3D300/sign=92afee66fd36afc3110c39658318eb85/908fa0ec08fa513db777cf78376d55fbb3fbd9b3.jpg')
      },
      initCharts () {
        var option = {
          title: {
            text: '某地区蒸发量和降水量',
            subtext: ''
          },
          tooltip: {
            trigger: 'axis'
          },
          legend: {
            data: ['蒸发量']
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
              name: '蒸发量',
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
      }
      .setArea {
        margin: 0;
      }
    }
  }
}
</style>

