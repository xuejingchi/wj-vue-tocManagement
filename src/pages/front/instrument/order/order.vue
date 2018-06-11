<template>
  <div class="order">
    <div class="top">
      <ul class="header">
        <li class="left">查询</li>
        <li class="right">
          <el-button class="btn" @click="search">查询</el-button>
        </li>
      </ul>
      <ul class="body">
        <li>
          <span class="label">SKU编号:</span>
          <el-input
            class="input"
            placeholder=""
            v-model="searchParams.skuCode"
            clearable>
          </el-input>
        </li>
        <li>
          <span class="label">生产线:</span>
          <el-select class="input" v-model="searchParams.productionLine" placeholder="请选择">
            <el-option
              v-for="item in lineType"
              :key="item.valueSetLineId"
              :label="item.description"
              :value="item.description">
            </el-option>
          </el-select>
        </li>
        <!--<li>-->
          <!--<span class="label">提交日期:</span>-->
          <!--<div class="start-time f-l">-->
            <!--<el-date-picker-->
              <!--v-model="searchParams.afterAdviseReleaseDate"-->
              <!--type="date"-->
              <!--placeholder="开始时间"-->
              <!--class="start-input"-->
            <!--&gt;-->
            <!--</el-date-picker>-->
          <!--</div>-->
          <!--<span class="f-l" style="margin-top: 10px;padding: 0 10px;">至</span>-->
          <!--<div class="f-l end-time">-->
            <!--<el-date-picker-->
              <!--v-model="searchParams.beforeAdviseReleaseDate"-->
              <!--type="date"-->
              <!--placeholder="结束时间"-->
              <!--class="end-input">-->
            <!--</el-date-picker>-->
          <!--</div>-->
        <!--</li>-->
      </ul>
    </div>
    <div id="myChart" :style="{width: '100%', height: '400px'}"></div>
  </div>
</template>

<script>
import { getMakeToOrderChart } from '@/api/front/instrument'
import { findLocationInfoList } from '@/api/front/setting/index'
import { getUserInfo } from '@/utils/auth'
export default {
  name: 'order',
  data () {
    return {
      msg: 'Welcome to Your Vue.js App',
      // 搜索参数
      searchParams: {
        supplierId: getUserInfo().supplierId,
        productionLine: '', // 生产线
        skuCode: '', // SKU编号
        afterAdviseReleaseDate: '',
        beforeAdviseReleaseDate: '',
        status: ''
      },
      drawList: [],
      lineType: [] // 生产线
    }
  },
  created () {
    this.getLocationList(1)
    this.getTableList()
  },
  mounted () {
    // this.drawLine()
  },
  methods: {
    // 获取位置
    getLocationList (num) {
      let params = {
        locationType: num,
        supplierId: getUserInfo().supplierId
      }
      findLocationInfoList(params).then(res => {
        this.lineType = res.data.data
      })
    },
    drawLine () {
      // 基于准备好的dom，初始化echarts实例
      let myChart = this.echarts.init(document.getElementById('myChart'))
      // 绘制图表
      myChart.setOption({
        title: {
          text: '工单交期表现',
          x: 'center',
          align: 'right'
        },
        tooltip: {
          trigger: 'axis'
        },
        toolbox: {
          show: true,
          y: 'bottom',
          feature: {
            mark: {show: true},
            dataView: {show: true, readOnly: false},
            magicType: {show: true, type: ['line', 'bar', 'stack', 'tiled']},
            restore: {show: true},
            saveAsImage: {show: true}
          }
        },
        calculable: true,
        legend: {
          data: ['black', 'red', 'yellow', 'green', 'cyan', 'DDP'],
          orient: 'vertical',
          right: 0,
          top: 'middle'
        },
        dataZoom: [{
          type: 'inside',
          startValue: this.drawList.xaxis[1][0],
          endValue: this.drawList.xaxis[1][59]
        }],
        xAxis: [
          {
            type: 'category',
            splitLine: {show: false},
            splitNumber: 1,
            data: this.drawList.xaxis[1]
          }
        ],
        yAxis: [
          {
            type: 'value',
            position: 'left'
          }
        ],
        // 全局调色盘。
        color: ['#1F1F1F', '#FF3939', '#FEFF3C', '#ACFF3C', 'cyan'],
        series: [
          {
            name: 'black',
            type: 'bar',
            tooltip: {trigger: 'item'},
            stack: '广告',
            data: this.drawList.series[5]
          },
          {
            name: 'red',
            type: 'bar',
            tooltip: {trigger: 'item'},
            stack: '广告',
            data: this.drawList.series[4]
          },
          {
            name: 'yellow',
            type: 'bar',
            tooltip: {trigger: 'item'},
            stack: '广告',
            data: this.drawList.series[3]
          },
          {
            name: 'green',
            type: 'bar',
            tooltip: {trigger: 'item'},
            stack: '广告',
            data: this.drawList.series[2]
          },
          {
            name: 'cyan',
            type: 'bar',
            tooltip: {trigger: 'item'},
            stack: '广告',
            data: this.drawList.series[1]
          },
          {
            name: 'DDP',
            type: 'line',
            data: this.drawList.series[11]
          }
        ]
      })
    },
    getTableList () {
      let params = {
        supplierId: 'ee69bc2059b911e8b271525400a13961',
        productionLine: this.searchParams.productionLine, // 生产线
        skuCode: this.searchParams.skuCode // SKU编号
        // afterAdviseReleaseDate: new Date(this.searchParams.afterAdviseReleaseDate).getTime(),
        // beforeAdviseReleaseDate: new Date(this.searchParams.beforeAdviseReleaseDate).getTime()
      }
      getMakeToOrderChart(params).then(res => {
        this.drawList = res.data.data
        this.drawLine()
      })
    },
    // 点击查询
    search () {
      this.getTableList()
    }
  }
}
</script>
