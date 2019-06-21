<template>
  <div class="mod-record">
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
            <el-select v-model="form.datatype" placeholder="请选择数据类型">
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
        <el-col :span="6">
          <el-form-item label="">
            <el-date-picker
              v-model="form.date"
              type="datetimerange"
              range-separator="至"
              start-placeholder="开始日期"
              end-placeholder="结束日期">
            </el-date-picker>
          </el-form-item>
        </el-col>
        <el-col :span="3">
          <el-button type="primary" @click="search">查询</el-button>
        </el-col>
      </el-form>
    </el-row>
    <el-row class="records" :gutter="20">
      <el-col :span="8" v-for="(item, index) in recordData" :key="index" class="recordItem">
        <div class="wrap">
          <p class="name"><i></i><span>{{item.recordPerson}}</span><span style="color: green"></span></p>
          <div class="content">{{item.content}}</div>
          <el-divider></el-divider>
          <p class="date">{{item.recordDate}}</p>
        </div>
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
          datatype: '',
          place: '',
          date: []
        },
        recordData: [
          {recordPerson: '超级管理员', recordDate: '2019-06-02 22:25:40', content: '完成施肥', status: '1'},
          {recordPerson: '超级管理员', recordDate: '2019-06-02 22:25:40', content: '完成施肥', status: '2'},
          {recordPerson: '超级管理员', recordDate: '2019-06-02 22:25:40', content: '完成施肥', status: '3'},
          {recordPerson: '超级管理员', recordDate: '2019-06-02 22:25:40', content: '完成施肥', status: '2'},
          {recordPerson: '超级管理员', recordDate: '2019-06-02 22:25:40', content: '完成施肥', status: '3'}
        ],
        statusData: {
          '1': 'red',
          '2': 'green',
          '3': 'yellow'
        }
      }
    },
    components: {
    },
    activated () {
    },
    methods: {
      search () {
        if (this.form.date.length) {
          this.form.srartDate = this.form.date[0]
          this.form.endDate = this.form.date[1]
        }
      }
    }
  }
</script>
<style lang="scss" scoped>
.records {
  .recordItem {
    height: 150px;
    .wrap {
      height: 100%;
      border: 1px solid #dfdfdf;
      border-radius: 4px;
    }
  }
}
</style>

