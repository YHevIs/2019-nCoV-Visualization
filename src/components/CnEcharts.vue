<template>
  <div
    class="cnEcharts"
    id="cnEcharts"
    :style="{ width: '500px', height: '800px' }"
  ></div>
</template>

<script>
export default {
  name: "HelloWorld",
  props: {
    msg: String
  },
  data() {
    return {
      ProvinceAll: "",
      ProvinceName:[],
      confirmedNum:[],
      curesNum:[],
      deathsNum:[]
    };
  },
  mounted() {
      this.getProvince();

  },
  methods: {
    getProvince() {
      this.$axios.get("http://www.dzyong.top:3005/yiqing/province").then(res => {
        this.ProvinceAll = res.data.data;
        for(let nameIndex = 0;nameIndex < this.ProvinceAll.length; nameIndex++){
            this.ProvinceAll[nameIndex].provinceName == "���西" ? this.ProvinceName.push("陕西") : this.ProvinceName.push(this.ProvinceAll[nameIndex].provinceName); 
            this.confirmedNum.push(this.ProvinceAll[nameIndex].confirmedNum)
            this.curesNum.push(this.ProvinceAll[nameIndex].curesNum)
            this.deathsNum.push(this.ProvinceAll[nameIndex].deathsNum)
        }
        this.drawLine();
        
      });
    },
    drawLine() {
      // 基于准备好的dom，初始化echarts实例
      let cnEcharts = this.$echarts.init(document.getElementById("cnEcharts"));
      // 绘制图表
      cnEcharts.setOption({
        title: {
        text: '省份累计数据',
        subtext: '数据来自网络'
    },
    tooltip: {
        trigger: 'axis',
        axisPointer: {
            type: 'shadow'
        }
    },
    legend: {
        data: ['2011年', '2012年']
    },
    grid: {
        left: '3%',
        right: '4%',
        bottom: '3%',
        containLabel: true
    },
    xAxis: {
        type: 'value',
        boundaryGap: [0, 0.01]
    },
    yAxis: {
        type: 'category',
        data: this.ProvinceName
    },
    series: [
        {
            name: '累计确诊',
            type: 'bar',
            data: this.confirmedNum,
        },
        {
            name: '累计死亡',
            type: 'bar',
            data: this.deathsNum
        },
        {
            name: '累计治愈',
            type: 'bar',
            data: this.curesNum
        }

        
    ]
      });
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss"></style>
