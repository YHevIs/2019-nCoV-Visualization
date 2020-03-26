<template>
  <div class="history">
    <h2><i class="el-icon-document-copy"></i> 历史数据 (从2020/1/13开始)</h2>

    <div class="historyMain" style="display:flex">
      <el-timeline style="max-h">
        <el-timeline-item
          v-for="history in historys"
          :key="history.index"
          :timestamp="history.date"
          placement="top"
        >
          <el-card>
            <p>
              累计确诊：<span style="color:red">{{
                history.confirmedNum
              }}</span>
              | 累计死亡：<span style="color:rgb(76, 76, 76)">{{
                history.deathsNum
              }}</span>
              | 疑似病例：<span style="color:rgb(241, 201, 0);">{{
                history.suspectedNum
              }}</span>
              | 累计治愈：<span style="color:green;">{{
                history.curesNum
              }}</span>
            </p>
            <!-- <p>{{ history.date }}</p> -->
          </el-card>
        </el-timeline-item>
      </el-timeline>
      <div
        class="historyEcharts"
        id="historyEcharts"
        :style="{ width: '500px', height: '800px' }"
      ></div>
    </div>
  </div>
</template>

<script>
export default {
  name: "history",
  props: {
    msg: String
  },
  data() {
    return {
      historys: [],
      date: [],
      confirmedNum: [],
      suspectedNum: [],
      deathsNum: [],
      curesNum: []
    };
  },
  mounted() {
    this.getHistory();
  },
  methods: {
    getHistory() {
      this.$axios.get("http://www.dzyong.top:3005/yiqing/history").then(res => {
        this.historys = [...res.data.data].reverse();
        for (let historyST = 0; historyST < this.historys.length; historyST++) {
          this.date.push(res.data.data[historyST].date);
          this.confirmedNum.push(res.data.data[historyST].confirmedNum);
          this.suspectedNum.push(res.data.data[historyST].suspectedNum);
          this.deathsNum.push(res.data.data[historyST].deathsNum);
          this.curesNum.push(res.data.data[historyST].curesNum);
        }
        this.drawLine();
      });
    },
    drawLine() {
      // 基于准备好的dom，初始化echarts实例
      let historyEcharts = this.$echarts.init(
        document.getElementById("historyEcharts")
      );
      // 绘制图表
      historyEcharts.setOption({
        title: {
          //   text: "堆叠区域图"
        },
        tooltip: {
          trigger: "axis",
          axisPointer: {
            type: "cross",
            label: {
              backgroundColor: "#6a7985"
            }
          }
        },
        legend: {
          data: ["累计确诊", "累计死亡", "累计治愈", "疑似病例"]
        },
        toolbox: {
          feature: {
            saveAsImage: {}
          }
        },
        grid: {
          left: "3%",
          right: "4%",
          bottom: "3%",
          containLabel: true
        },
        xAxis: [
          {
            type: "category",
            boundaryGap: false,
            data: this.date,
            axisLabel: {
                interval:0,//代表显示所有x轴标签显示
            },
            show:false
          }
        ],
        yAxis: [
          {
            type: "value"
          }
        ],
        series: [
          {
            name: "累计确诊",
            type: "line",
            // stack: "总量",
            areaStyle: {},
            data: this.confirmedNum
          },
          {
            name: "累计死亡",
            type: "line",
            // stack: "总量",
            areaStyle: {},
            data: this.deathsNum
          },
          {
            name: "累计治愈",
            type: "line",
            // stack: "总量",
            areaStyle: {},
            data: this.curesNum
          },
          {
            name: "疑似病例",
            type: "line",
            // stack: "总量",
            areaStyle: {},
            data: this.suspectedNum
          }
        ]
      });
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
.history > div > .el-timeline {
  max-height: 760px;
  overflow: scroll;
  padding: 10px 10px 10px 3px;
}
</style>
