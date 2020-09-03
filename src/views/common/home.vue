<template>
  <div class="box">
    <div class="tilte">
      <span>技能培训数据报表</span>
    </div>
    <div class="btn">
      <div class="btn1">
        <div style="font-size:0.9vw;margin-right:0.4vw;font-weight: 600;">总报名人数</div>
        <div>{{trainerSum}}</div>
      </div>
      <div class="btn1">
        <div style="font-size:0.9vw;margin-right:0.4vw;font-weight: 600;">已毕业人数</div>
        <div>{{graduateSum}}</div>
      </div>
      <div class="btn1">
        <div style="font-size:0.9vw;margin-right:0.4vw;font-weight: 600;">在学人数</div>
        <div>{{studySum}}</div>
      </div>
      <div class="btn1">
        <div style="font-size:0.9vw;margin-right:0.4vw;font-weight: 600;">今日新增人数</div>
        <div>{{todayTrainerSum}}</div>
      </div>
      <div class="btn1">
        <div style="font-size:0.9vw;margin-right:0.4vw;font-weight: 600;">全部班级数量</div>
        <div>{{classSum}}</div>
      </div>
      <div class="btn1">
        <div style="font-size:0.9vw;margin-right:0.4vw;font-weight: 600;">已审核人数</div>
        <div>{{auditTrainerSum}}</div>
      </div>
      <div class="btn1">
        <div style="font-size:0.9vw;margin-right:0.4vw;font-weight: 600;">月报名人数</div>
        <div>{{trainerMonthSum}}</div>
      </div>
    </div>
    <div class="box-moddie">
      <div class="box-left">
        <el-row>
          <el-col :span="18">
            <div id="J_chartBarBox" class="chart-box"></div>
          </el-col>
          <el-col :span="6">
            <div id="J_chartPieBox" class="chart-box"></div>
          </el-col>
        </el-row>
      </div>
      <div class="box-right">
        <div id="J_chartPie2Box" class="chart-box"></div>
      </div>
    </div>
    <div class="box-bottom">
      <el-row>
        <el-col :span="9">
          <div id="J_chartPieDoughnutBox" class="chart-box"></div>
        </el-col>
        <el-col :span="15">
          <div class="bottom-right">
            <div id="J_chartLineBox" class="chart-box"></div>
          </div>
        </el-col>
      </el-row>
    </div>
  </div>
</template>

<script>
import echarts from "echarts";
export default {
  data() {
    return {
      chartLine: null,
      chartBar: null,
      chartPie: null,
      chartPieDoug: null,
      chartScatter: null,
      auditTrainerSum: "",
      trainerMonthSum: "",
      classSum: "",
      graduateSum: "",
      studySum: "",
      todayTrainerSum: "",
      trainerSum: "",
      trainerCategorySum: [],
      newTrainerCategorySum: [],
      newtrainerTypeMonthSum: [],
      trainerTypeMonthSum: [],
      MonthtitleList: [],
      titleList: [],
      MonthSum: [],
      MonthPuduct: [],
      LineType: [],
      Daysum: [],
      trainList: []
    };
  },
  mounted() {
    this.getTrainerCategorySum(); //饼状图
    this.initChartPieDoug();
    this.getTrainerMonthSum(); //柱状图
    this.trainerDaySum();
    this.getTrainerTypeMonthSum(); //饼状图2数据
  },
  activated() {
    // 由于给echart添加了resize事件, 在组件激活时需要重新resize绘画一次, 否则出现空白bug
    if (this.chartLine) {
      this.chartLine.resize();
    }
    if (this.chartBar) {
      this.chartBar.resize();
    }
    if (this.chartPie) {
      this.chartPie.resize();
    }
    if (this.chartPie2) {
      this.chartPie2.resize();
    }
    if (this.chartPieDoug) {
      this.chartPieDoug.resize();
    }
  },
  created() {
    this.getTrainerSatutionSum();
  },
  methods: {
    // 折线图数据统计
    trainerDaySum() {
      this.$http({
        url: this.$http.adornUrl("/data/express/trainerDaySum"),
        method: "get",
        params: this.$http.adornParams({})
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.Daysum = data.dateList;
          this.trainList = data.trainerDaySum;
          this.initChartLine();
        }
      });
    },
    // 折线图
    initChartLine() {
      var lineStyle = {
        width: 4 // 0.1的线条是非常细的了
      };
      var option = {
        color: [
          "#fb5a5f",
          "#39dd95",
          "#c459fe",
          "#3c94fe",
          "#87e642",
          "#f6a440",
          "#dbcc4e"
        ],

        title: {
          text: "折线图堆叠"
        },
        tooltip: {
          trigger: "axis"
        },
        legend: {
          data: ["学科1", "学科2", "学科3", "学科4", "学科5"],
          textStyle: {
            color: "#ffffff"
          }
        },
        grid: {
          left: "3%",
          right: "4%",
          bottom: "3%",
          containLabel: true
        },
        toolbox: {
          feature: {
            saveAsImage: {}
          }
        },

        xAxis: {
          type: "category",
          boundaryGap: false,
          axisLine: {
            lineStyle: {
              color: "#fff"
            }
          },
          data: this.Daysum
        },
        yAxis: {
          type: "value",
          axisLine: {
            lineStyle: {
              color: "#fff"
            }
          },
          axisTick: {
            //y轴刻度线
            show: false
          },
          splitLine: {
            //网格线
            show: false
          }
        },

        series: this.trainList
      };
      this.chartLine = echarts.init(document.getElementById("J_chartLineBox"));
      this.chartLine.setOption(option);
      window.addEventListener("resize", () => {
        this.chartLine.resize();
      });
    },
    // 柱状图数据
    getTrainerMonthSum() {
      this.$http({
        url: this.$http.adornUrl("/data/express/trainerMonthSum"),
        method: "get",
        params: this.$http.adornParams({})
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.MonthSum = data.sum;
          this.MonthPuduct = this.MonthSum[0];
          console.log(this.MonthPuduct, 11);
          var itemLin = { type: "bar" };
          for (let i = 0; i < this.MonthPuduct.length - 1; i++) {
            this.LineType.push(itemLin);
          }
          console.log(this.LineType);

          this.initChartBar();
        }
      });
    },
    // 柱状图
    initChartBar() {
      var option = {
        color: ["#4cd4a7", "#43b7c8", "#3d94ef", "#bf5ff9"],
        legend: {
          textStyle: {
            color: "#ffffff"
          }
        },
        tooltip: {},
        dataset: {
          source: this.MonthSum
        },
        xAxis: {
          type: "category",
          axisLine: {
            lineStyle: {
              color: "#fff"
            }
          }
        },
        yAxis: {
          axisLine: {
            lineStyle: {
              color: "#fff"
            }
          }
        },
        // Declare several bar series, each will be mapped
        // to a column of dataset.source by default.
        series: this.LineType
      };
      this.chartBar = echarts.init(document.getElementById("J_chartBarBox"));
      this.chartBar.setOption(option);
      window.addEventListener("resize", () => {
        this.chartBar.resize();
      });
    },
    //修改key
    convertKey(arr, key) {
      let newArr = [];
      arr.forEach((item, index) => {
        let newObj = {};
        for (var i = 0; i < key.length; i++) {
          newObj[key[i]] = item[Object.keys(item)[i]];
        }
        newArr.push(newObj);
      });

      return newArr;
    },

    //环状图数据
    getTrainerCategorySum() {
      this.$http({
        url: this.$http.adornUrl("/data/express/trainerCategorySum"),
        method: "get",
        params: this.$http.adornParams({})
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.trainerCategorySum = data.trainerCategorySum;

          this.newTrainerCategorySum = this.convertKey(
            this.trainerCategorySum,
            ["name", "value"]
          );
          this.titleList = [];
          for (const iterator of this.newTrainerCategorySum) {
            this.titleList.push(iterator.name);
          }
        }
        this.initChartPie();

        this.initChartPieDoug();
      });
    },
    // 饼状图
    initChartPie() {
      var option = {
        tooltip: {
          trigger: "item",
          formatter: "{a} <br/>{b} : {c} ({d}%)"
        },
        color: [
          "#42bcd4",
          "#4cd4a7",
          "#3d94ef",
          "#bf5ff9",
          "#fb5a5f",
          "#dbcc4e",
          "#f6a440"
        ],
        series: [
          {
            name: "培训种类",
            type: "pie",
            radius: "55%",
            center: ["50%", "50%"],
            data: this.newTrainerCategorySum,
            emphasis: {
              itemStyle: {
                shadowBlur: 10,
                shadowOffsetX: 0,
                shadowColor: "rgba(0, 0, 0, 0.5)"
              }
            }
          }
        ]
      };
      this.chartPie = echarts.init(document.getElementById("J_chartPieBox"));
      this.chartPie.setOption(option);
      window.addEventListener("resize", () => {
        this.chartPie.resize();
      });
    },
    // 饼状图2数据
    getTrainerTypeMonthSum() {
      this.$http({
        url: this.$http.adornUrl("/data/express/trainerTypeMonthSum"),
        method: "get",
        params: this.$http.adornParams({})
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.trainerTypeMonthSum = data.trainerTypeMonthSum;

          this.newtrainerTypeMonthSum = this.convertKey(
            this.trainerTypeMonthSum,
            ["name", "value"]
          );
          this.MonthtitleList = [];
          for (const iterator of this.newtrainerTypeMonthSum) {
            this.MonthtitleList.push(iterator.name);
          }
        }

        this.initChartPie2();
      });
    },
    //饼状图2
    initChartPie2() {
      var option = {
        title: {
          text: "月报名人数",
          textStyle: {
            color: "#0DB9F2", //颜色
            fontStyle: "normal", //风格
            fontWeight: "normal", //粗细
            fontFamily: "Microsoft yahei", //字体
            fontSize: 14, //大小
            align: "center" //水平对齐
          }
        },

        tooltip: {
          trigger: "item",
          formatter: "{a} <br/>{b} : {c} ({d}%)"
        },
        color: [
          "#42bcd4",
          "#4cd4a7",
          "#3d94ef",
          "#bf5ff9",
          "#fb5a5f",
          "#dbcc4e",
          "#f6a440"
        ],
        series: [
          {
            name: "月报名人数",
            type: "pie",
            radius: "55%",
            center: ["50%", "40%"],
            data: this.newtrainerTypeMonthSum,
            emphasis: {
              itemStyle: {
                shadowBlur: 10,
                shadowOffsetX: 0,
                shadowColor: "rgba(0, 0, 0, 0.5)"
              }
            }
          }
        ]
      };
      this.chartPie2 = echarts.init(document.getElementById("J_chartPie2Box"));
      this.chartPie2.setOption(option);
      window.addEventListener("resize", () => {
        this.chartPie2.resize();
      });
    },
    //环状图
    initChartPieDoug() {
      var option = {
        color: [
          "#fb5a5f",
          "#39dd95",
          "#c459fe",
          "#3c94fe",
          "#87e642",
          "#f6a440",
          "#dbcc4e"
        ],
        tooltip: {
          trigger: "item",
          formatter: "{a} <br/>{b}: {c} ({d}%)"
        },
        legend: {
          orient: "vertical",
          left: 10,
          data: this.titleList,
          textStyle: {
            color: "#ffffff"
          }
        },
        series: [
          {
            name: "类别数据",
            type: "pie",
            radius: ["30%", "70%"],
            avoidLabelOverlap: false,
            label: {
              show: true,
              position: "outside"
            },
            emphasis: {
              label: {
                show: true,
                fontSize: "30",
                fontWeight: "bold"
              }
            },
            labelLine: {
              //设置延长线的长度
              normal: {
                length: 5 //设置延长线的长度
                // length2: 10,//设置第二段延长线的长度
              }
            },
            data: this.newTrainerCategorySum
          }
        ]
      };

      this.chartPieDoug = echarts.init(
        document.getElementById("J_chartPieDoughnutBox")
      );
      this.chartPieDoug.setOption(option);
      window.addEventListener("resize", () => {
        this.chartPieDoug.resize();
      });
    },

    // 人员统计
    getTrainerSatutionSum() {
      this.$http({
        url: this.$http.adornUrl("/data/express/trainerSatutionSum"),
        method: "get",
        params: this.$http.adornParams({})
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.auditTrainerSum = data.auditTrainerSum;
          this.trainerMonthSum = data.trainerMonthSum;
          this.classSum = data.classSum;
          this.graduateSum = data.graduateSum;
          this.studySum = data.studySum;
          this.todayTrainerSum = data.todayTrainerSum;
          this.trainerSum = data.trainerSum;
        }
      });
    }
  }
};
</script>

<style lang="scss">
.box {
  background: #06273a;
}
.chart-box {
  height: 2.5rem;
}
.tilte {
  height: 9vh;
  width: 100%;
  background: #0d416e;
  font-size: 20px;
  font-weight: 800;
  color: #79a0b6;
  line-height: 10vh;
  text-align: center;
}
.btn {
  height: 16vh;
  display: flex;
  padding-top: 3vh;
  justify-content: space-around;

  > .btn1 {
    height: 10vh;
    width: 10vw;
    display: flex;
    color: #fff;
    font-weight: 300;
    font-size: 1.7vw;
    border-radius: 5px;
    align-items: center;
    padding-left: 1vw;
  }
  .btn1:nth-child(1) {
    background: #76d9aa;
  }
  .btn1:nth-child(2) {
    background: #6ccee7;
  }
  .btn1:nth-child(3) {
    background: #e2b64b;
  }
  .btn1:nth-child(4) {
    background: #e85954;
  }
  .btn1:nth-child(5) {
    background: #bf5ff9;
  }
  .btn1:nth-child(6) {
    background: #5a6ffb;
  }
  .btn1:nth-child(7) {
    background: rgba(128, 255, 255, 1);
  }
}
.box-moddie {
  height: 2.8rem;
  display: flex;
  justify-content: space-between;
  padding: 1%;
  .box-left {
    width: 69%;
    height: 100%;
    border: 1px solid #385261;
    border-radius: 8px;
  }
  .box-right {
    width: 30%;
    height: 100%;
    border: 1px solid #385261;
    border-radius: 8px;
    box-sizing: border-box;
    padding-top: 10px;
    padding-left: 10px;
  }
}
.box-bottom {
  height: 3.5rem;
  margin-top: 20px;
  width: 100%;
  padding-bottom: 30px;
}
.bottom-right {
  border: 1px solid #385261;
  height: 3.5rem;
  > .chart-box {
    height: 3.45rem;
  }
}
</style>
