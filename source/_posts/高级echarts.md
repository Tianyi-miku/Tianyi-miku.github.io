---
title: 做一个高级的echarts图表
date: 2023-11-15 9:39:10
tags:
    - echarts
categories:  [业务,插件]
   
---

做一个不同于官网基础的大屏可视化echarts,用到extendShape
![Alt text](/img/echarts1.png)
<!-- more -->
用到的api shape class。[zrender](https://ecomfe.github.io/zrender-doc/public/api.html#zrender-api)
```
(
    opts: Object
) => zrender.graphic.Path
```
页面
```
<div id={props.keys || "column"} style={{ height: "100%" }}></div>
```
创建Echarts
```
const chartDom = document.getElementById(props.keys || "column") as HTMLElement;
let myChart = echarts.getInstanceByDom(chartDom);
if (!myChart) // 如果不存在则创建
{
  myChart = echarts.init(chartDom);
}
```
自定义柱状图上下环
```
      let isAuto = false;
      if (lables.length > 10) {
        isAuto = true;
      }
      let width = (chartDom.clientWidth - 90) / values.length / 3;
      const CubeLeft = echarts.graphic.extendShape({
        shape: {
          x: 0,
          y: 0,
        },
        buildPath: function (ctx, shape) {
          const xAxisPoint = shape.xAxisPoint;
          const c0 = [shape.x, shape.y];
          const c1 = isAuto
            ? [shape.x - width, shape.y - 4]
            : [shape.x - 20, shape.y - 4];
          const c2 = isAuto
            ? [xAxisPoint[0] - width, xAxisPoint[1] - 4]
            : [xAxisPoint[0] - 20, xAxisPoint[1] - 4];
          const c3 = [xAxisPoint[0], xAxisPoint[1]];
          (ctx as CanvasRenderingContext2D | any)
            .moveTo(c0[0], c0[1])
            .lineTo(c1[0], c1[1])
            .lineTo(c2[0], c2[1])
            .lineTo(c3[0], c3[1])
            .closePath();
        },
      });
      const CubeRight = echarts.graphic.extendShape({
        shape: {
          x: 0,
          y: 0,
        },
        buildPath: function (ctx, shape) {
          const xAxisPoint = shape.xAxisPoint;
          const c1 = [shape.x, shape.y];
          const c2 = isAuto
            ? [shape.x + width, shape.y - 4]
            : [shape.x + 20, shape.y - 4];
          const c3 = isAuto
            ? [xAxisPoint[0] + width, xAxisPoint[1] - 4]
            : [xAxisPoint[0] + 20, xAxisPoint[1] - 4];
          const c4 = [xAxisPoint[0], xAxisPoint[1]];
          (ctx as CanvasRenderingContext2D | any)
            .moveTo(c1[0], c1[1])
            .lineTo(c2[0], c2[1])
            .lineTo(c3[0], c3[1])
            .lineTo(c4[0], c4[1])
            .closePath();
        },
      });
      const CubeTop = echarts.graphic.extendShape({
        shape: {
          x: 0,
          y: 0,
        },
        buildPath: function (ctx, shape) {
          const c1 = [shape.x, shape.y + 4];
          const c2 = isAuto
            ? [shape.x + width, shape.y - 4]
            : [shape.x + 20, shape.y - 4];
          const c3 = [shape.x, shape.y - 13];
          const c4 = isAuto
            ? [shape.x - width, shape.y - 4]
            : [shape.x - 20, shape.y - 4];
          (ctx as CanvasRenderingContext2D | any)
            .moveTo(c1[0], c1[1])
            .lineTo(c2[0], c2[1])
            .lineTo(c3[0], c3[1])
            .lineTo(c4[0], c4[1])
            .closePath();
        },
      });
      echarts.graphic.registerShape("CubeLeft", CubeLeft);
      echarts.graphic.registerShape("CubeRight", CubeRight);
      echarts.graphic.registerShape("CubeTop", CubeTop);
```
设置option
```
  const option = {
        backgroundColor: '#0C2849',
        grid: {
          top: '5%',
          left: '2%',
          bottom: '5%',
          right: '5%',
          containLabel: true,
        },
        tooltip: {
          trigger: 'item',
          show: true,
          padding: 1,
          formatter: function (param: any) {
            return "<div style='background:#222853;color:#fff;border:1px solid rgba(255,255,255,.2);padding:5px;border-radius:3px;'>" +
              "<div style='padding-top:5px;'>" +
              "<span style=''> " + param.name + ": </span>" +
              // "<span style=''>" + param.value + "</span><span>" + `${props.keys ===1?'分':'%'}`+"</span>" +
              "<span style=''>" + param.value + (props.keys === 1 ? '分' : '%') +
              "</div>" +
              "</div>"
          }
        },
        animation: true,
        xAxis: [
          {
            data: lables,
            axisLabel: {
              textStyle: {
                color: '#aaaaaa',
                fontSize: 14
              },
              margin: 30, //刻度标签与轴线之间的距离。
            },

            axisLine: {
              show: false //不显示x轴
            },
            axisTick: {
              alignWithLabel: true //不显示刻度
            },
            boundaryGap: true,
            splitLine: {
              show: false,
              width: 0.08,
              lineStyle: {
                type: "solid",
                color: "#03202E"
              }
            }
          }
        ],
        yAxis: [
          {
            splitLine: {
              show: true,
              lineStyle: {
                color: '#14375E',
                type: 'solid'
              }
            },
            axisTick: {
              show: false
            },
            axisLine: {
              show: false
            },
            axisLabel: {
              textStyle: {
                color: '#888'
              },
            },
            max: 100,
            min: 0,
            interval: 20,
          },
        ],
        series: [
          {
            name: '',
            type: 'pictorialBar',
            symbolSize: [40, 10],
            symbolOffset: [0, -6], // 上部椭圆
            symbolPosition: 'end',
            z: 12,
            // "barWidth": "0",
            label: {
              normal: {
                show: true,
                position: 'top',
                // "formatter": "{c}%"
                fontSize: 15,
                fontWeight: 'bold',
                color: '#34DCFF',
              },
            },
            color: '#2DB1EF',
            data: values,
          },
          {
            name: '',
            type: 'pictorialBar',
            symbolSize: [40, 10],
            symbolOffset: [0, 7], // 下部椭圆
            // "barWidth": "20",
            z: 12,
            color: '#2DB1EF',
            data: values,
          },
          {
            name: '',
            type: 'pictorialBar',
            symbolSize: function (d: any) {
              return d > 0 ? [50, 15] : [0, 0]
            },
            symbolOffset: [0, 12], // 下部内环
            z: 10,
            itemStyle: {
              "normal": {
                color: new echarts.graphic.LinearGradient(0, 0, 0, 1, [{
                  offset: 0,
                  color: "rgba(89,211,255,1)"
                },
                {
                  offset: 1,
                  color: "rgba(23,237,194,1)"
                }
                ])
              }
            },
            data: values,
          },
          {
            name: '',
            type: 'pictorialBar',
            symbolSize: [40, 10],
            symbolOffset: [0, 18], // 下部外环
            z: 10,
            itemStyle: {
              normal: {
                color: 'transparent',
                borderColor: '#19465D',
                borderType: 'solid',
                borderWidth: 2,
              },
            },
            data: values,
          },
          {
            type: 'bar',
            //silent: true,
            barWidth: '40',
            barGap: '10%',
            barCateGoryGap: '10%',
            itemStyle: {
              normal: {
                color: new echarts.graphic.LinearGradient(0, 0, 0, 0.7, [
                  {
                    offset: 0,
                    color: '#38B2E6',
                  },
                  {
                    offset: 1,
                    color: '#0B3147',
                  },
                ]),
                opacity: 0.8,
              },
            },
            data: values,
          }
        ],
      };
      myChart.setOption(option);
```

如果说这种觉得不好看，可以参考Echart[社区](https://www.isqqw.com/)