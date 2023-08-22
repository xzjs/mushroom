<template>
  <div class="home">
    <div class="top-bar">
      <div class="top-title">蘑 菇 智 能 一 体 化 采 摘 系 统</div>
      <div class="top-logo"></div>
      <div class="top-time">{{ FormatTime(data.nowTime) }}</div>
    </div>
    <div class="mid-content">
      <div class="content">
        <div class="content-top-item sp">
          <div class="item-title">实时行为监测</div>
          <div class="item">
            <div id="echarts1" style="width: 100%; height: 100%"></div>
          </div>
        </div>
        <div class="content-top-item" style="">
          <img :src="camera" style="width: 100%; height: 100%" alt="" />
        </div>
        <div
          class="content-top-item"
          style="display: flex; justify-content: center; align-items: center"
        >
          <div class="item-title">历史采摘数据</div>

          <div id="echarts3" style="float: left; width: 94%; height: 96%" />
        </div>
      </div>
      <div class="content">
        <div class="content-top-item">
          <img src="/left.jpeg" style="width: 100%; height: 100%" alt="" />
        </div>
        <div
          class="content-top-item jqr_content"
          style="justify-content: flex-start"
        >
          <div class="item-title">机器人信息总览</div>

          <div class="list-title list-item">
            <div>序号</div>
            <div>机器人编号</div>
            <div>厂区编号</div>
            <div>蘑菇编号</div>
            <div>菇床编号</div>
            <div>操作</div>
          </div>
          <div class="JTableBody noScrollBar" id="scrollContentTj">
            <div class="list-item" v-for="(item, index) in data.jqrArr" :key="index">
              <div>{{ index + 1 }}</div>
              <div>{{ item.jqrbh }}</div>
              <div>{{ item.cqbh }}</div>
              <div>{{ item.mgbh }}</div>
              <div>{{ item.gcbh }}</div>
              <div>{{}}</div>
            </div>
          </div>
        </div>
        <div class="content-top-item">
          <img src="/right.jpeg" style="width: 100%; height: 100%" alt="" />
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import axios from "axios";
import * as echarts from "echarts";
import $ from "jquery";
import { reactive, onMounted, computed } from "vue";

const data = reactive({
  statistics: { keys: [], values: [] },
  camera: "/api/camera",
  timer: undefined,
  nowTime: new Date(),
  latedata: "2020-1-9",
  tbTimerTj: null,
  jqrArr: [
    {
      jqrbh: "001",
      cqbh: "北3区",
      mgbh: "1001",
      gcbh: "02",
    },
    {
      jqrbh: "002",
      cqbh: "北3区",
      mgbh: "1001",
      gcbh: "02",
    },
    {
      jqrbh: "003",
      cqbh: "北3区",
      mgbh: "23423",
      gcbh: "02",
    },
    {
      jqrbh: "001",
      cqbh: "北3区",
      mgbh: "3355",
      gcbh: "02",
    },
    {
      jqrbh: "001",
      cqbh: "北3区",
      mgbh: "2243",
      gcbh: "02",
    },
    {
      jqrbh: "001",
      cqbh: "北3区",
      mgbh: "2221",
      gcbh: "02",
    },
    {
      jqrbh: "001",
      cqbh: "北3区",
      mgbh: "6453",
      gcbh: "02",
    },
    {
      jqrbh: "001",
      cqbh: "北3区",
      mgbh: "4657",
      gcbh: "02",
    },
    {
      jqrbh: "001",
      cqbh: "北3区",
      mgbh: "3452",
      gcbh: "02",
    },
    {
      jqrbh: "001",
      cqbh: "北3区",
      mgbh: "4453",
      gcbh: "02",
    },
    {
      jqrbh: "001",
      cqbh: "北3区",
      mgbh: "4657",
      gcbh: "02",
    },
    {
      jqrbh: "001",
      cqbh: "北3区",
      mgbh: "3452",
      gcbh: "02",
    },
    {
      jqrbh: "001",
      cqbh: "北3区",
      mgbh: "4453",
      gcbh: "02",
    },
  ],
});

onMounted(() => {
  data.timer = setInterval(() => {
    data.nowTime = new Date().toLocaleString();
    getStatistics();
  }, 1000);
  initDataEchartsMj();

  jsScrollTj();
  // setInterval(() => {
  //   this.getCamera();
  // }, 100);
  // this.getCamera();
});

function getStatistics() {
  axios.get("/api/statistics").then((res) => {
    data.statistics = res.data;
    initDataEcharts1();
  });
}
function getCamera() {
  data.camera = "/api/camera?" + Date.now();
}
function FormatTime() {
  //返回显示的日期时间格式
  var date = new Date();
  return date.toLocaleString("zh-CN", { timeZone: "Asia/Shanghai" });
}

//竖向柱状排序图
function initDataEcharts1() {
  // 基于准备好的dom，初始化echarts实例
  const myChart = echarts.init(document.getElementById("echarts1"));
  //加载动画，可不管
  // myChart.showLoading({
  // 	text: 'loading',
  // 	color: '#c23531',
  // 	textColor: '#000',
  // 	maskColor: 'rgba(255, 255, 255, 0.8)',
  // 	zlevel: 0,
  // 	// 字体大小。从 `v4.8.0` 开始支持。
  // 	fontSize: 12,
  // 	// 是否显示旋转动画(spinner)。从 `v4.8.0` 开始支持。
  // 	showSpinner: true,
  // 	// 旋转动画(spinner)的半径。从 `v4.8.0` 开始支持。
  // 	spinnerRadius: 10,
  // 	// 旋转动画(spinner)的线宽。从 `v4.8.0` 开始支持。
  // 	lineWidth: 5,
  // 	// 字体粗细。从 `v5.0.1` 开始支持。
  // 	fontWeight: 'normal',
  // 	// 字体风格。从 `v5.0.1` 开始支持。
  // 	fontStyle: 'normal',
  // 	// 字体系列。从 `v5.0.1` 开始支持。
  // 	fontFamily: 'sans-serif'

  // });
  // setTimeout(function() {

  //echarts统计表主配置
  myChart.setOption({
    xAxis: {
      max: "dataMax",
      splitLine: {
        show: false,
      },
    },
    title: {
      text: "采摘行为（次数）",
      textStyle: {
        align: "center",
        color: "#fff",
        fontSize: 12,
      },
      top: "5%",
      left: "2%",
    },
    yAxis: {
      type: "category",
      show: true,
      splitLine: {
        show: false,
      },
      axisTick: {
        show: false, //刻度线
      },
      axisLine: {
        show: false, //隐藏y轴
      },
      axisLabel: {
        show: true, //隐藏刻度值
      },

      data: data.statistics.keys,
      inverse: true,
      animationDuration: 300,
      animationDurationUpdate: 300,
      //设置坐标轴字体颜色和宽度
      axisLine: {
        lineStyle: {
          color: "#fff",
          width: 0,
        },
      },
      max: 6, // only the largest 3 bars will be displayed
    },
    grid: {
      //与绝对定位相似，top，left，right，bottom 设定是根据上级盒子宽高来计算

      top: "20%",

      left: "10%",

      right: "10%",

      bottom: "-5%",
    },
    series: [
      {
        realtimeSort: true,
        name: "",
        type: "bar",
        barWidth: 8,
        data: data.statistics.values,
        label: {
          show: true,
          position: "right",
          valueAnimation: true,
        },
        //显示数值
        itemStyle: {
          normal: {
            //这里设置柱形图圆角 [左上角，右上角，右下角，左下角]
            barBorderRadius: [20, 20, 20, 20],

            label: {
              show: true, //开启显示
              position: "right", //在上方显示
              textStyle: {
                //数值样式
                color: "#fff",
                fontSize: "12px",
              },
            },
            color: function (params) {
              let colors = [
                "rgba(1,216,252,0.9)",
                "rgba(5,119,211,0.9)",
                "transparent",
              ];
              return new echarts.graphic.LinearGradient(1, 0, 0, 0, [
                {
                  offset: 0,
                  color: colors[0],
                },
                {
                  offset: 1,
                  color: colors[1],
                },
                {
                  offset: 1,
                  color: colors[2],
                },
              ]);
            },
          },
        },
      },
    ],
    legend: {
      show: true,
    },
    animationDuration: 0,
    animationDurationUpdate: 3000,
    animationEasing: "linear",
    animationEasingUpdate: "linear",
  });
  //关闭加载动画
  // myChart.hideLoading();
  // }, 1000);

  window.onresize = myChart.resize; //自适应浏览器窗口的大小
}
//折现面积图
function initDataEchartsMj() {
  // 基于准备好的dom，初始化echarts实例
  const myChart = echarts.init(document.getElementById("echarts3"));
  //加载动画，可不管
  // myChart.showLoading({
  // 	text: 'loading',
  // 	color: '#c23531',
  // 	textColor: '#000',
  // 	maskColor: 'rgba(255, 255, 255, 0.8)',
  // 	zlevel: 0,
  // 	// 字体大小。从 `v4.8.0` 开始支持。
  // 	fontSize: 12,
  // 	// 是否显示旋转动画(spinner)。从 `v4.8.0` 开始支持。
  // 	showSpinner: true,
  // 	// 旋转动画(spinner)的半径。从 `v4.8.0` 开始支持。
  // 	spinnerRadius: 10,
  // 	// 旋转动画(spinner)的线宽。从 `v4.8.0` 开始支持。
  // 	lineWidth: 5,
  // 	// 字体粗细。从 `v5.0.1` 开始支持。
  // 	fontWeight: 'normal',
  // 	// 字体风格。从 `v5.0.1` 开始支持。
  // 	fontStyle: 'normal',
  // 	// 字体系列。从 `v5.0.1` 开始支持。
  // 	fontFamily: 'sans-serif'

  // });
  // setTimeout(function() {

  //echarts统计表主配置
  myChart.setOption({
    backgroundColor: "rgba(0,0,0,0)",
    title: {
      text: "",
      textStyle: {
        align: "center",
        color: "#fff",
        fontSize: 20,
      },
      top: "5%",
      left: "center",
    },
    legend: {
      show: true,
      orient: "horizontal", //布局方式：  horizontal/vertical
      x: "center", // 水平安放位置，默认为全图居中，可选： 'center' ¦ 'left' ¦ 'right'  或 {number}（x坐标，单位px）
      y: "bottom", //垂直安放位置，默认为全图顶端，可选： 'top' ¦ 'bottom' ¦ 'center' 或 {number}（y坐标，单位px）
      textStyle: {
        fontSize: 10,
        color: "#70C5FF",
      },
      backgroundColor: "rgba(0,0,0,0)",
      borderColor: "#ccc", // 图例边框颜色
      borderWidth: 0, // 图例边框线宽，单位px，默认为0（无边框）
      padding: 10, // 图例内边距，单位px，默认各方向内边距为5，或数组形式分别设定上右下左边距，同css
      itemGap: 40, // 各个item之间的间隔，单位px，默认为10，横向布局时为水平间隔，纵向布局时为纵向间隔
      itemWidth: 30, // 图例图形宽度
      itemHeight: 14, // 图例图形高度
    },

    tooltip: {
      trigger: "axis",
      axisPointer: {
        lineStyle: {
          color: {
            type: "linear",
            x: 0,
            y: 0,
            x2: 0,
            y2: 1,
            colorStops: [
              {
                offset: 0,
                color: "#70C5FF",
              },
              {
                offset: 0.5,
                color: "#70C5FF",
              },
              {
                offset: 1,
                color: "#70C5FF",
              },
            ],
            global: false,
          },
        },
      },
    },
    grid: {
      top: "15%",
      left: "9%",
      right: "0%",
      bottom: "25%",
      // containLabel: true
    },
    xAxis: [
      {
        type: "category",
        axisLine: {
          show: true,
        },
        splitArea: {
          // show: true,
          color: "#f00",
          lineStyle: {
            background: "#17283E",
          },
        },
        axisLabel: {
          color: "#70C5FF",
          interval: 0,
          rotate: 35,
          fontSize: 8,
        },
        splitLine: {
          show: true,
          lineStyle: {
            color: "#050001;",
          },
        },
        boundaryGap: false,
        data: [
          "2023-01-01",
          "2023-01-02",
          "2023-01-03",
          "2023-01-04",
          "2023-01-05",
          "2023-01-06",
          "2023-01-07",
          "2023-01-08",
          "2023-01-09",
          "2023-01-10",
        ],
      },
    ],

    yAxis: [
      {
        type: "value",
        axisLine: {
          show: false,
        },
        min: 0,
        // max: 140,
        splitNumber: 7,

        splitLine: {
          show: true,
          lineStyle: {
            color: "#141D3B",
          },
        },

        axisLabel: {
          show: true,
          margin: 5,
          textStyle: {
            color: "#70C5FF",
          },
          fontSize: 10,
        },
        axisTick: {
          show: false,
        },
        //设置坐标轴字体颜色和宽度
        axisLine: {
          show: true,
          lineStyle: {
            color: "#fff",
            width: 0,
          },
        },
      },
    ],
    series: [
      {
        name: "当天产量",
        type: "line",
        smooth: true, //是否平滑
        showAllSymbol: false,
        // symbol: 'image://./static/images/guang-circle.png',
        symbol: "circle",
        symbolSize: 6,
        lineStyle: {
          normal: {
            color: "#6c50f3",
            shadowColor: "rgba(0, 0, 0, .3)",
            // shadowBlur: 0,
            // shadowOffsetY: 5,
            // shadowOffsetX: 5,
          },
        },
        label: {
          show: false,
          position: "top",
          textStyle: {
            color: "#6c50f3",
          },
        },
        itemStyle: {
          color: "#6c50f3",
          borderColor: "#fff",
          borderWidth: 1,
          shadowColor: "rgba(0, 0, 0, .3)",
          shadowBlur: 0,
          shadowOffsetY: 2,
          shadowOffsetX: 2,
        },
        tooltip: {
          show: true,
          formatter: function (params) {
            // do some thing
            return "名称：" + params.name;
          },
        },
        areaStyle: {
          normal: {
            color: new echarts.graphic.LinearGradient(
              0,
              0,
              0,
              1,
              [
                {
                  offset: 0,
                  color: "rgba(108,80,243,0.3)",
                },
                {
                  offset: 1,
                  color: "rgba(108,80,243,0)",
                },
              ],
              false
            ),
            shadowColor: "rgba(108,80,243, 0.9)",
            shadowBlur: 20,
          },
        },
        data: [
          202.84, 205.97, 232.79, 281.55, 298.35, 214.02, 232.79, 281.55,
          298.35, 214.02,
        ],
      },
      {
        name: "上月产量（环比）",
        type: "line",
        smooth: true, //是否平滑
        showAllSymbol: true,
        // symbol: 'image://./static/images/guang-circle.png',
        symbol: "circle",
        symbolSize: 6,
        lineStyle: {
          normal: {
            color: "#E6A23C",
            // shadowColor: "rgba(0, 0, 0, .3)",
            // shadowBlur: 0,
            // shadowOffsetY: 5,
            // shadowOffsetX: 5,
          },
        },
        label: {
          show: false,
          position: "top",
          textStyle: {
            color: "#E6A23C",
          },
        },
        itemStyle: {
          color: "#E6A23C",
          borderColor: "#fff",
          borderWidth: 1,
          shadowColor: "rgba(0, 0, 0, .3)",
          shadowBlur: 0,
          shadowOffsetY: 2,
          shadowOffsetX: 2,
        },
        tooltip: {
          show: true,
          formatter: function (params) {
            // do some thing
            return "名称：" + params.name;
          },
        },
        areaStyle: {
          normal: {
            color: new echarts.graphic.LinearGradient(
              0,
              0,
              0,
              1,
              [
                {
                  offset: 0,
                  color: "rgba(230,162,60,0.3)",
                },
                {
                  offset: 1,
                  color: "rgba(230,162,60,0)",
                },
              ],
              false
            ),
            shadowColor: "rgba(230,162,60, 0.9)",
            shadowBlur: 20,
          },
        },
        data: [
          500.84, 505.97, 532.79, 521.55, 528.35, 514.02, 532.79, 521.55,
          528.35, 514.02,
        ],
      },
      {
        name: "去年产量(同比)",
        type: "line",
        smooth: true, //是否平滑
        showAllSymbol: true,
        // symbol: 'image://./static/images/guang-circle.png',
        symbol: "circle",
        symbolSize: 6,
        lineStyle: {
          normal: {
            color: "#00ca95",
            shadowColor: "rgba(0, 0, 0, .3)",
            // shadowBlur: 0,
            // shadowOffsetY: 5,
            // shadowOffsetX: 5,
          },
        },
        label: {
          show: false,
          position: "top",
          textStyle: {
            color: "#00ca95",
          },
        },

        itemStyle: {
          color: "#00ca95",
          borderColor: "#fff",
          borderWidth: 1,
          shadowColor: "rgba(0, 0, 0, .3)",
          shadowBlur: 0,
          shadowOffsetY: 2,
          shadowOffsetX: 2,
        },
        tooltip: {
          show: true,
        },
        areaStyle: {
          normal: {
            color: new echarts.graphic.LinearGradient(
              0,
              0,
              0,
              1,
              [
                {
                  offset: 0,
                  color: "rgba(0,202,149,0.3)",
                },
                {
                  offset: 1,
                  color: "rgba(0,202,149,0)",
                },
              ],
              false
            ),
            shadowColor: "rgba(0,202,149, 0.9)",
            shadowBlur: 20,
          },
        },
        data: [
          781.55, 798.35, 714.02, 779.55, 789.57, 756.1, 714.02, 779.55, 789.57,
          756.14,
        ],
      },
    ],
  });
  //关闭加载动画
  // myChart.hideLoading();
  // }, 1000);

  window.onresize = myChart.resize; //自适应浏览器窗口的大小
}
// 滚动配置
function jsScrollTj(rowHei = 36, speed = 2000, delay = 1000) {
  //直接利用js实现
  var isStopSw = false;
  var contSw = document.getElementById("scrollContentTj");
  contSw.scrollTop = 0;
  contSw.onmouseover = function () {
    isStopSw = true;
  };
  contSw.onmouseout = function () {
    isStopSw = false;
  };

  function startScrollSw() {
    if (isStopSw) {
      console.log("暂停中");
    } else {
      console.log(contSw.scrollTop, contSw.scrollHeight - contSw.offsetHeight);
      if (contSw.scrollTop + 10 >= contSw.scrollHeight - contSw.offsetHeight) {
        contSw.scrollTop = 0;
      } else {
        // cont.scrollTop = parseInt(cont.scrollTop) + rowHei;
        let buSw = parseInt(contSw.scrollTop) + rowHei + "px";
        console.log("buSw", buSw);
        // document.getElementById('scrollContentTj').animate({scrollTop: buSw}, 200);
        $("#scrollContentTj").animate({ scrollTop: buSw }, 200);
      }
    }
  }

  setTimeout((fn) => {
    clearInterval(data.tbTimerTj);
    data.tbTimerTj = setInterval(startScrollSw, speed);
  }, delay);
}
</script>

<style lang="scss" scoped>
.home {
  width: 100vw;
  height: 100vh;
  font-size: 20px;
  color: #fff;
  // display: flex;
  // justify-content: center;
  // align-items: center;
  background-image: url(@/assets/img/background.png);
  // background-color: red;
  box-sizing: border-box;
  padding: 0 1.5%;

  .top-bar {
    width: 100%;
    height: 82px;
    background-image: url("@/assets/img/top_bar.png");
    background-size: 100% 100%;
    display: flex;
    justify-content: center;
    align-items: center;
    position: relative;

    .top-title {
      line-height: 24px;
      font-size: 26px;
      font-family: Alibaba PuHuiTi 2;
      font-weight: normal;
      color: #ffffff;
    }
    .top-logo {
      width: 290px;
      height: 38px;
      background-image: url("@/assets/img/logo.png");
      background-size: 100% 100%;
      position: absolute;
      bottom: 0.2%;
      left: 70px;
    }
    .top-time {
      width: 283;
      height: 17px;
      background-size: 100% 100%;
      position: absolute;
      bottom: 10px;
      right: 140px;
      font-family: AlibabaPuHuiTi_2_65_Medium;
      font-size: 18px;
      display: flex;
      align-items: flex-end;
    }
  }

  .mid-content {
    width: 100%;
    height: calc(100% - 142px);
    // background-color: red;
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    align-items: center;
    margin-top: 30px;
    box-sizing: border-box;
    padding: 10px 0;

    .content {
      width: 100%;
      height: 48%;
      display: flex;
      justify-content: space-around;
      align-items: center;

      .content-top-item {
        width: 32%;
        height: 100%;
        // border: 5px solid rgb(24,57,123);
        position: relative;
        // box-shadow: inset 0 0 10px #1A3F81;
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: space-between;
        background-image: url("@/assets/img/bigk.png");
        background-size: 100% 100%;

        .item {
          width: 100%;
          height: 100%;
          position: relative;
          // box-shadow: inset 0 0 10px  #1A3F81 ;
        }
        .item-title {
          width: 191px;
          height: 33px;
          position: absolute;
          z-index: 9;
          left: 50%;
          top: 0%;
          transform: translate(-50%, -50%);
          font-family: Alibaba PuHuiTi 2;
          display: flex;
          justify-content: center;
          align-items: center;
          background-image: url("@/assets/img/title_bar.png");
          background-size: 100% 100%;
          font-size: 14px;
          font-weight: bold;
          color: #02d9fd;
        }
      }
      .sp {
        box-shadow: none;
      }
    }
  }
}
// .bian{
//   width: 12px;
//   height: 12px;
//   //background-color: red;
//   position: absolute;

// }
// .tl{
//   top: 0;
//   left: 0;
//   border-top: 2px solid #00FFFF;
//   border-left: 2px solid #00FFFF;
// }
// .tr{
//   top: 0;
//   right: 0;
//   border-top: 2px solid #00FFFF;
//   border-right: 2px solid #00FFFF;
// }
// .bl{
//   bottom: 0;
//   left: 0;
//   border-bottom: 2px solid #00FFFF;
//   border-left: 2px solid #00FFFF;
// }
// .br{
//   bottom: 0;
//   right: 0;
//   border-bottom: 2px solid #00FFFF;
//   border-right: 2px solid #00FFFF;
// }

.jqr_content {
  box-sizing: border-box;
  padding: 0 12px 20px 12px;
}
.list-item {
  width: 100%;
  display: flex;
  justify-content: space-between;
  align-items: center;
  font-size: 7px;
  height: 36px;
  color: #005ed2;
  & > div {
    width: 16.5%;
    display: flex;
    justify-content: center;
    align-items: center;
  }
  & > div:nth-child(1) {
    width: 10%;
  }
  & > div:nth-child(2) {
    width: 20%;
  }
  & > div:nth-child(3) {
    width: 20%;
  }
  & > div:nth-child(4) {
    width: 20%;
  }
  & > div:nth-child(5) {
    width: 20%;
  }
  & > div:nth-child(6) {
    width: 10%;
  }
}
.list-item:nth-child(odd) {
  // background-color: rgba(5,6,60,1);
  background: linear-gradient(
    257deg,
    rgba(0, 0, 0, 0) 0%,
    rgba(0, 0, 0, 0) 100%
  );
}
.list-item:nth-child(even) {
  background: linear-gradient(257deg, #022a68 0%, #03277f 100%);
  // opacity: 0.4;
}
.list-item:hover {
  background: linear-gradient(257deg, #0355d1 0%, #0540d8 100%);
  // opacity: 0.4;
  color: #fff;
}
.list-title {
  background: linear-gradient(257deg, #022a68 0%, #03277f 100%);
  color: #2fd5ff;
  margin-top: 30px;
  height: 36px !important;
}

/*滚动列表*/
.JTableBody {
  width: 100%;
  max-height: 1270px;
  overflow-y: scroll;
  position: relative;
  padding: 0;
  margin: 0;
  /*background-color: rgb(33,99,160);*/
  /* box-sizing: border-box; */
  /*border: 5px solid #18c86c;*/
}

.oneBodyRow {
  width: 100%;
  height: 36px !important;
  line-height: 36px;
  box-sizing: border-box;
  position: relative;
  z-index: 2;
  /*background-color: #fff0fe;*/
  text-align: center;
  display: flex;
  align-items: center;
  justify-content: space-between;

  border: 1px solid rgb(13, 88, 153);
  border-radius: 5px;
  background: linear-gradient(rgba(9, 34, 68, 0.8), rgba(10, 71, 129, 1));
  /*margin: 5px 0;*/
  margin-top: 10px;
}

.oneBodyRow > div {
  width: 25%;
  height: 100%;
  font-size: 20px;
  text-overflow: ellipsis;
  overflow: hidden;
  white-space: nowrap;
  text-align: center;
  /*background-color: rgb(60,60,59,.6);*/
  color: #ffffff;
}
.oneBodyRow:hover {
  background: linear-gradient(rgba(37, 128, 255, 1), rgb(45, 101, 152));
}
/*.oneBodyRow>div:nth-child(1) {*/
/*	width: 15%;*/
/*	text-align: center;*/
/*}*/
/*.oneBodyRow>div:last-child {*/
/*	width: 15%;*/
/*	text-align: center;*/
/*}*/
.oneBodyRow:nth-child(even) {
  /*background-color: #3c3c3b;*/
  background-color: rgb(60, 60, 59, 0.6);
  color: #ffffff;
}

.oneBodyRow .sp-title {
  width: 35%;
  display: flex;
  flex-direction: column;
  font-size: 18px;
}
.oneBodyRow .sp-title .sp-top {
  width: 100%;
  height: 50%;
  display: flex;
  justify-content: center;
  align-items: center;
}
.oneBodyRow .sp-title .sp-bottom {
  width: 100%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
}
.oneBodyRow .sp-title .sp-bottom > div {
  width: 33%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
}

.noScrollBar::-webkit-scrollbar {
  width: 0 !important;
  height: 0 !important;
}

.noScrollBar {
  -ms-overflow-style: none;
}

.noScrollBar {
  overflow: -moz-scrollbars-none;
}
</style>
