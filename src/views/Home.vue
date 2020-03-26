<template>
  <div class="home">
    <!-- <img alt="Vue logo" src="../assets/logo.png"> -->
    <el-container>
      <el-aside width="250px">
        <div>
          <h2><i class="el-icon-news"></i> 最新新闻</h2>
          <div>
            <ul>
              <li v-for="New in News" v-bind:key="New.index" class="New">
                <a :href="New.sourceUrl">
                  <h4>
                    {{ New["title"] }}
                  </h4>
                  <span class="dataTime">更新时间: {{ New.pubDate }}</span>
                  <p class="dataTime">
                    {{ New.pubDateStr }} | {{ New.provinceName }}
                  </p>

                  <p>
                    {{ New.summary }}
                  </p>
                </a>
              </li>
            </ul>
          </div>
        </div>
      </el-aside>
      <el-main>
        <div>
          <h2><i class="el-icon-view"></i> 全国数据</h2>
          <span class="dataTime">更新时间: {{ TotalData["date"] }}</span>
          <ul class="TotalData">
            <li>
              <i class="el-icon-s-custom"></i> 累计确诊:
              <span style="color:red">{{ TotalData["diagnosed"] }}</span>
            </li>
            <li>
              <i class="el-icon-user-solid"></i> 累计死亡:
              <span style="color:#4c4c4c">{{ TotalData["death"] }}</span>
            </li>
            <li>
              <i class="el-icon-user"></i> 累计治愈:
              <span style="color:green">{{ TotalData["cured"] }}</span>
            </li>
          </ul>
        </div>
        <div>
          <h2>
            <i class="el-icon-s-help"></i>
            省市数据查询
          </h2>
          <el-alert
            title="不要带‘省’或‘市’字，如：‘重庆市’直接填‘重庆’即可,如果不填写地区名，则查询所有省份数据"
            type="warning"
          >
          </el-alert>
          <div style="display:flex">
            <el-input
              placeholder="请输入地点"
              prefix-icon="el-icon-search"
              v-model="ProvinceQuery"
            >
            </el-input>
            <el-button plain @click="provinceQuery()">查询</el-button>
          </div>

          <div class="query" style="display:flex">
            <el-card class="box-card">
              <div slot="header" class="clearfix">
                <span>{{ this.ProvinceQuery }}</span>
                <!-- <el-button style="float: right; padding: 3px 0" type="text"
                >操作按钮</el-button> -->
              </div>
              <div
                v-for="Province in Provinces"
                :key="Province.index"
                class="text item"
              >
                <ul>
                  <li>
                    地区：{{ Province.provinceName }} | {{ Province.cityName }}
                  </li>
                  <li>累计确诊：{{ Province.confirmedCount }}</li>
                  <li>累计治愈：{{ Province.curedCount }}</li>
                  <li>累计死亡：{{ Province.deadCount }}</li>
                </ul>
              </div>
            </el-card>
            <CnEcharts />
          </div>
        </div>
        <History />
      </el-main>
    </el-container>
    <el-footer></el-footer>
  </div>
</template>

<script>
// @ is an alias to /src
import CnEcharts from "@/components/CnEcharts.vue";
import History from "@/components/History.vue";

export default {
  name: "Home",
  components: {
    CnEcharts,
    History
    // HelloWorld
  },
  data() {
    return {
      TotalData: "",
      News: [],
      NewsPageName: 1,
      sourceUrl: "",
      ProvinceQuery: "",
      Provinces: ""
    };
  },

  mounted() {
    this.getTotal();
    this.getNews();
    this.open();
  },
  methods: {
    open() {
      this.$confirm(
        "本页面的一部分数据来源为人工整理，仅供参考，若与官方信息有出入则以官方信息为准。如果关闭此弹窗则默认跳转至官方数据网页！",
        "注意",
        {
          distinguishCancelAndClose: true,
          confirmButtonText: "了解情况并进入页面",
          cancelButtonText: "跳转至丁香园疫情地图"
        }
      )
        .then(() => {
          this.$message({
            type: "info",
            message: "确认进入"
          });
        })
        .catch(() => {
          setTimeout(
            "javascript:location.href='https://ncov.dxy.cn/ncovh5/view/pneumonia?from=timeline&isappinstalled=0'",
            5000
          );
          this.$message({
            type: "info",
            message: "页面将在5秒后跳转至丁香园疫情地图，感谢您的访问"
          });
        });
    },
    provinceQuery() {
      if (this.ProvinceQuery == "所有省份数据") this.ProvinceQuery = "";
      this.$axios
        .get(
          "http://www.dzyong.top:3005/yiqing/area?area=" + this.ProvinceQuery
        )
        .then(res => {
          this.Provinces = res.data.data;
        });

      if (this.ProvinceQuery == "") this.ProvinceQuery = "所有省份数据";
    },
    load() {
      this.NewsPageName += 1;
      this.getNews();
    },
    getTotal: function() {
      this.$axios.get("http://www.dzyong.top:3005/yiqing/total").then(res => {
        this.TotalData = res.data.data[0];
      });
    },
    getNews: function() {
      this.$axios
        .get(
          "http://www.dzyong.top:3005/yiqing/news?pageNum=" +
            this.NewsPageName +
            "&pageSize=5"
        )
        .then(res => {
          if (this.News.length == 0) {
            this.News = res.data.data;
          } else {
            this.News.push(res.data.data);
          }
        });
    }
  }
};
</script>
<style lang="scss">
a {
  color: #000;
  text-decoration: none;
}
p {
  font-size: 12px;
}
ul {
  padding: 0;
}
li {
  list-style-type: none;
}
.TotalData {
  display: flex;
}
.TotalData li {
  padding-right: 10px;
}
.dataTime {
  color: #9f9f9f;
  font-size: 12px;
  margin: 3px 0;
}
.New {
  margin: 20px 0;
  padding: 10px;
  transition: ease 0.35s;
}
.New:hover {
  cursor: pointer;
  box-shadow: 0 0 2px 3px rgba(0, 0, 0, 0.1);
}
h4 {
  margin: 10px 0;
}

.text {
  font-size: 14px;
}

.item {
  margin-bottom: 18px;
}

.clearfix:before,
.clearfix:after {
  display: table;
  content: "";
}
.clearfix:after {
  clear: both;
}

.box-card {
  width: 480px;
}
.el-card__body {
  max-height: 700px;
  overflow: scroll;
}
.el-header{
  display: flex;
  align-items: center;
}
*{
  font-family: Arial, Helvetica, sans-serif;
}
@media screen and (max-width: 540px){
  .el-container{
    flex-direction: column-reverse !important;
  }
  .box-card{
    width: 100%;
  }
  .query{
    flex-direction:column;
  }
  .cnEcharts{
    width: 100% !important;
    height: 400px !important;
  }
  .historyMain{
    flex-direction: column !important;
    min-height: 220px;
    margin-bottom: 30px;
  }
  .el-aside{
    width: 100% !important;
  }
  .el-message-box{
    width: 100% !important;
  }
  
}
</style>
