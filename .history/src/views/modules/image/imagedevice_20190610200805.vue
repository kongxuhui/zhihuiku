<template>
  <div class="mod-image">
    <el-row class="search-area" :gutter="10">
      <el-form ref="form" :model="form">
        <el-col :span="3">
          <el-form-item label="">
            <el-select v-model="form.company" placeholder="请选择企业">
              <el-option label="区域一" value="shanghai"></el-option>
            </el-select>
          </el-form-item>
        </el-col>
        <el-col :span="3">
          <el-form-item label="">
            <el-select v-model="form.plots" placeholder="请选择地块">
              <el-option label="区域一" value="shanghai"></el-option>
            </el-select>
          </el-form-item>
        </el-col>
        <el-col :span="3">
          <el-form-item label="">
            <el-select v-model="form.area" placeholder="请选择分区">
              <el-option label="区域一" value="shanghai"></el-option>
            </el-select>
          </el-form-item>
        </el-col>
        <el-col :span="3">
          <el-form-item label="">
            <el-select v-model="form.place" placeholder="请选择地点">
              <el-option label="区域一" value="shanghai"></el-option>
            </el-select>
          </el-form-item>
        </el-col>
      </el-form>
      <el-col class="dateSelect">
        <span style="width:30px;">日期</span><i class="icons el-icon-caret-left"></i><el-slider :step="10" v-model="form.date"></el-slider><i class="icons el-icon-caret-right"></i>
      </el-col>
      <el-col class="dateShow">
        <span class="wrap"><i class="icon-rili"></i><span class="tips">2019年5月28日 --- 2019年6月6日 数据</span></span>
      </el-col>
    </el-row>
    <el-row class="content">
      <el-col :span="12" class="left"><img :src="imgUrl" alt=""></el-col>
      <el-col :span="12" class="right">
        <el-tabs v-model="activeName">
          <el-tab-pane label="图像数据" name="first">
            <p>时间：{{currentData.date}}</p>
            <p>地块：{{currentData.plots}}</p>
            <p>分区：{{currentData.area}}</p>
            <p>作物：{{currentData.plants}}</p>
            <p>生育期：{{currentData.birthDate}}</p>
            <p>备注： <el-input type="textarea" v-model="currentData.remarks"></el-input></p>
            <p><el-button>提交</el-button></p>
          </el-tab-pane>
          <el-tab-pane label="土壤数据" name="second">
            <div id="J_chartBox" class="chart-box"></div>
          </el-tab-pane>
        </el-tabs>
      </el-col>
    </el-row>
  </div>
</template>

<script>
  export default {
    data () {
      return {
        form: {
          company: '',
          plots: '',
          area: '',
          place: '',
          date: []
        },
        activeName: 'first',
        currentData: {
          date: '2019年6月3日',
          plots: '地块一',
          area: '分区一',
          plants: '西红柿',
          birthDate: '3个月',
          remarks: ''
        },
        imgUrl: 'https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1559063887429&di=beb4cd45b75ce9cdf3a93cc84edc0d10&imgtype=0&src=http%3A%2F%2Fhbimg.b0.upaiyun.com%2F0f8fc717586cd30c8bf27e72a1659d6c5ab91ab548ac3-JO1ia5_fw658',
        dateRanges: ['28日', '29日', '30日', '5月31日', '6月1日', '2日', '3日', '4日', '5日', '6日']
      }
    },
    components: {
    },
    activated () {
    },
    methods: {
      search () {
      }
    }
  }
</script>
<style lang="scss" scoped>
.dateSelect {
  border-top:1px solid #dfdfdf;
  display:flex;
  justify-content:space-around;
  height: 38px;
  line-height: 38px;
  .icons {
    width: 20px;
    margin: 0 10px;
    line-height: 38px;
    font-size: 16px;
  }
  .el-slider {
    flex: 1;
  }
}
.dateShow {
  display: flex;
  line-height:20px;
  text-align: center;
  .wrap {
    .icon-rili {
      width: 20px;
      height: 20px;
      float: left;
      background-image: url(~@/assets/img/rili.png);
      background-size: 100%;
      margin: 0 10px;
    }
    .tips {
      float: left;
      line-height: 20px;
    }
  }
}
.content {
  margin-top: 20px;
  .left, .right{
    border: 1px solid #dfdfdf;
    height: 600px;
    text-align: center;
    img {
      width: 94%;
      height: 94%;
      margin-top: 3%;
    }
  }
  .right {
    border-left: none;
    /deep/ .el-tabs {
      width: 94%;
      height: 94%;
      margin: 3% auto;
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
          padding: 0 0px !important;
          margin-right: 8px !important;
          height: 30px;
          line-height: 30px;
        }
        .is-active {
          color: #009C8E !important;
          background: #fff !important;
        }
      }
      .el-tabs__content {
      }
    }
  }
}
</style>

