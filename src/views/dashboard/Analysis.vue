<template>
  <div>
    <a-row :gutter="24">
      <a-col :sm="24" :md="12" :xl="6" :style="{ marginBottom: '24px' }">
        <chart-card :loading="loading" title="当前在线" :total="0 | NumberFormat">
          <a-tooltip title="刷新" slot="action">
            <a-icon type="sync" />
          </a-tooltip>
          <template slot="footer">
            <template style="margin-right:100px;">
              设备总数
              <span>0</span>
            </template>
            <template>
              未激活设备
              <span>0</span>
            </template>
          </template>
        </chart-card>
      </a-col>
      <a-col :sm="24" :md="12" :xl="6" :style="{ marginBottom: '24px' }">
        <chart-card :loading="loading" title="今日设备消息量" :total="0 | NumberFormat">
          <a-tooltip title="刷新" slot="action">
            <a-icon type="sync" />
          </a-tooltip>
          <template slot="footer">
            当月设备消息量
            <span>{{ '0' | NumberFormat }}</span>
          </template>
        </chart-card>
      </a-col>
      <a-col :sm="24" :md="12" :xl="6" :style="{ marginBottom: '24px' }">
        <gauge-card :loading="loading" title="CPU 使用率">
          <a-tooltip title="服务器 CPU 使用情况" slot="action">
            <a-icon type="info-circle-o" />
          </a-tooltip>
          <div>
            <gauge-color
              :height="300"
              :color="['#0086FA', '#FFBF00', '#F5222D']"
              :scale="[{dataKey: 'value',min: 0,max: 10,tickInterval: 1,nice: false}]"
              :data="[{ value: cpuUsage }]"
            />
          </div>
        </gauge-card>
      </a-col>
      <a-col :sm="24" :md="12" :xl="6" :style="{ marginBottom: '24px' }">
        <gauge-card :loading="loading" title="JVM 内存">
          <a-tooltip title="Java 虚拟机内存占用情况" slot="action">
            <a-icon type="info-circle-o" />
          </a-tooltip>
          <div>
            <gauge
              :height="300"
              :scale="[{dataKey: 'value',min: 0,max: memeryUsage.max,tickInterval: 1,nice: false}]"
              :data="[{ value: memeryUsage.usage }]"
            />
          </div>
        </gauge-card>
      </a-col>
    </a-row>

    <a-card :loading="loading" :bordered="false" :body-style="{padding: '0'}">
      <div class="salesCard">
        <a-tabs
          default-active-key="1"
          size="large"
          :tab-bar-style="{marginBottom: '24px', paddingLeft: '16px'}">
          <div class="extra-wrapper" slot="tabBarExtraContent">
            <div class="extra-item">
              <a-radio-group defaultValue="a">
                  <a-radio-button value="a">1小时</a-radio-button>
                  <a-radio-button value="b">1天</a-radio-button>
                  <a-radio-button value="c">7天</a-radio-button>
                  <a-radio-button value="d">30天</a-radio-button>
                </a-radio-group>
            </div>
            <a-range-picker :style="{width: '256px'}" />
          </div>
          <a-tab-pane loading="true" tab="设备消息" key="1">
            <a-row>
              <a-col :xl="24" :lg="12" :md="12" :sm="24" :xs="24">
                <bar :data="barData" />
              </a-col>
            </a-row>
          </a-tab-pane>
        </a-tabs>
      </div>
    </a-card>

    <div class="antd-pro-pages-dashboard-analysis-twoColLayout" :class="!isMobile && 'desktop'">
      <a-row :gutter="24" type="flex" :style="{ marginTop: '24px' }">

        <a-col :xl="10" :lg="24" :md="24" :sm="24" :xs="24">
          <a-card
            class="antd-pro-pages-dashboard-analysis-salesCard"
            :loading="loading"
            :bordered="false"
            title="各型号设备占比"
            :style="{ height: '100%' }"
          >
            <div slot="extra" style="height: inherit;">
              <div class="analysis-salesTypeRadio">
                <a-radio-group defaultValue="a">
                  <a-radio-button value="a">全部设备</a-radio-button>
                  <a-radio-button value="b">在线</a-radio-button>
                  <a-radio-button value="c">离线</a-radio-button>
                </a-radio-group>
              </div>
            </div>
            <div>
              <span>数量统计</span>
              <a-select
                mode="multiple"
                :default-value="['a1', 'b2']"
                style="width: 60%;float:right;"
                placeholder="请选择设备型号"
                @change="handleChange"
              >
                <a-select-option v-for="i in 25" :key="(i + 9).toString(36) + i">
                  {{ (i + 9).toString(36) + i }}
                </a-select-option>
              </a-select>
            </div>
            <div>
              <div>
                <v-chart :force-fit="true" :height="405" :data="pieData" :scale="pieScale">
                  <v-tooltip :showTitle="false" data-key="item*percent" />
                  <v-axis />
                  <v-legend data-key="item" />
                  <v-pie position="percent" color="item" :vStyle="pieStyle" />
                  <v-coord type="theta" :radius="0.75" :innerRadius="0.6" />
                </v-chart>
              </div>
            </div>
          </a-card>
        </a-col>
        <a-col :xl="14" :lg="24" :md="24" :sm="24" :xs="24">
          <a-card :loading="loading" :bordered="false" :body-style="{padding: '0'}">
            <div class="salesCard">
              <a-tabs
                default-active-key="1"
                size="large"
                :tab-bar-style="{marginBottom: '24px', paddingLeft: '16px'}"
              >
                <div class="extra-wrapper" slot="tabBarExtraContent">
                  <div class="extra-item">
                    <a-radio-group defaultValue="a">
                        <a-radio-button value="a">1小时</a-radio-button>
                        <a-radio-button value="b">1天</a-radio-button>
                        <a-radio-button value="c">7天</a-radio-button>
                        <a-radio-button value="d">30天</a-radio-button>
                      </a-radio-group>
                  </div>
                  <a-range-picker :style="{width: '256px'}" />
                </div>
                <a-tab-pane loading="true" tab="设备消息量" key="1">
                  <a-row>
                    <a-col :xl="23" :lg="12" :md="12" :sm="24" :xs="24">
                      <div>
                        <a-select
                          mode="multiple"
                          :default-value="['a1', 'b2']"
                          style="width: 60%;float:right;"
                          placeholder="请选择设备型号"
                          @change="handleChange"
                        >
                          <a-select-option v-for="i in 25" :key="(i + 9).toString(36) + i">
                            {{ (i + 9).toString(36) + i }}
                          </a-select-option>
                        </a-select>
                      </div>
                    </a-col>
                  </a-row>
                  <a-row>
                    <a-col :xl="24" :lg="12" :md="12" :sm="24" :xs="24">
                      <bar :data="barData" />
                    </a-col>
                  </a-row>
                </a-tab-pane>
              </a-tabs>
            </div>
          </a-card>
        </a-col>
      </a-row>
    </div>
  </div>
</template>

<script>
import moment from 'moment'
import {
  ChartCard,
  MiniArea,
  MiniBar,
  MiniProgress,
  Bar,
  Trend,
  NumberInfo,
  MiniSmoothArea,
  Gauge,
  GaugeColor,
  GaugeCard
} from '@/components'
import { baseMixin } from '@/store/app-mixin'
import { wsUrl } from '@/api/analysis'
import Message from '@/utils/message'

const barData = []
const barData2 = []
for (let i = 0; i < 12; i += 1) {
  barData.push({
    x: `${i + 1}月`,
    y: Math.floor(Math.random() * 1000) + 200
  })
  barData2.push({
    x: `${i + 1}月`,
    y: Math.floor(Math.random() * 1000) + 200
  })
}

const searchUserData = []
for (let i = 0; i < 7; i++) {
  searchUserData.push({
    x: moment()
      .add(i, 'days')
      .format('YYYY-MM-DD'),
    y: Math.ceil(Math.random() * 10)
  })
}
const searchUserScale = [
  {
    dataKey: 'x',
    alias: '时间'
  },
  {
    dataKey: 'y',
    alias: '用户数',
    min: 0,
    max: 10
  }
]

const searchTableColumns = [
  {
    dataIndex: 'index',
    title: '排名',
    width: 90
  },
  {
    dataIndex: 'keyword',
    title: '搜索关键词'
  },
  {
    dataIndex: 'count',
    title: '用户数'
  },
  {
    dataIndex: 'range',
    title: '周涨幅',
    align: 'right',
    sorter: (a, b) => a.range - b.range,
    scopedSlots: { customRender: 'range' }
  }
]
const searchData = []
for (let i = 0; i < 50; i += 1) {
  searchData.push({
    index: i + 1,
    keyword: `搜索关键词-${i}`,
    count: Math.floor(Math.random() * 1000),
    range: Math.floor(Math.random() * 100),
    status: Math.floor((Math.random() * 10) % 2)
  })
}

const DataSet = require('@antv/data-set')

const sourceData = [
  { item: '家用电器', count: 32.2 },
  { item: '食用酒水', count: 21 },
  { item: '个护健康', count: 17 },
  { item: '服饰箱包', count: 13 },
  { item: '母婴产品', count: 9 },
  { item: '其他', count: 7.8 }
]

const pieScale = [
  {
    dataKey: 'percent',
    min: 0,
    formatter: '.0%'
  }
]

const dv = new DataSet.View().source(sourceData)
dv.transform({
  type: 'percent',
  field: 'count',
  dimension: 'item',
  as: 'percent'
})
const pieData = dv.rows

export default {
  name: 'Analysis',
  mixins: [baseMixin],
  components: {
    ChartCard,
    MiniArea,
    MiniBar,
    MiniProgress,
    Bar,
    Trend,
    NumberInfo,
    MiniSmoothArea,
    Gauge,
    GaugeColor,
    GaugeCard
  },
  data () {
    return {
      loading: true,
      cpuUsage: 0,
      memeryUsage: { usage: 0, max: 8 },
      // 搜索用户数
      searchUserData,
      searchUserScale,
      searchTableColumns,
      searchData,

      barData,
      barData2,

      //
      pieScale,
      pieData,
      sourceData,
      pieStyle: {
        stroke: '#fff',
        lineWidth: 1
      }
    }
  },
  computed: {
    message () {
      return this.$store.state.socket.message
    }
  },
  watch: {
    message () {
      switch (this.message.requestId) {
        case Message.ids.CPUSTATE:
          this.cpuUsage = this.message.payload.value / 10
          break
        case Message.ids.JVMSTATE:
          this.memeryUsage = { usage: this.message.payload.value.usage / 1024, max: Math.floor(this.message.payload.value.max / 1024) }
          break
        default:
          console.log('不支持的消息' + this.message)
      }
    }
  },
  methods: {
    watchServerInfo () {
      const messageCPU = Message.createMessage(Message.ids.CPUSTATE, Message.topics.CPUTOPIC, {
        params: {
          'history': 1
        }
      }, 'sub')
      this.sendMessage(messageCPU)
      const messageJVM = Message.createMessage(Message.ids.JVMSTATE, Message.topics.JVMTOPIC, {
        params: {
          'history': 1
        }
      }, 'sub')
      this.sendMessage(messageJVM)
    }
  },
  mounted () {
    setTimeout(() => {
      this.watchServerInfo()
    }, 1000)
  },
  created () {
    this.$connect(wsUrl)
    setTimeout(() => {
      this.loading = !this.loading
    }, 500)
  },
  beforeDestroy () {
    this.$disconnect()
  }
}
</script>

<style lang="less" scoped>
.extra-wrapper {
  line-height: 55px;
  padding-right: 24px;

  .extra-item {
    display: inline-block;
    margin-right: 24px;

    a {
      margin-left: 24px;
    }
  }
}

.antd-pro-pages-dashboard-analysis-twoColLayout {
  position: relative;
  display: flex;
  display: block;
  flex-flow: row wrap;
}

.antd-pro-pages-dashboard-analysis-salesCard {
  height: calc(100% - 24px);
  /deep/ .ant-card-head {
    position: relative;
  }
}

.dashboard-analysis-iconGroup {
  i {
    margin-left: 16px;
    color: rgba(0, 0, 0, 0.45);
    cursor: pointer;
    transition: color 0.32s;
    color: black;
  }
}
.analysis-salesTypeRadio {
  position: absolute;
  right: 24px;
  bottom: 12px;
}
</style>
