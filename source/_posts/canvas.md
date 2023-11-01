---
title: canvas基础
date: 2023-03-20 18:13:55
tags:
    - canvas
categories: [学习,canvas]
---

## 基本用法

2D图像
```
const canvas = document.getElementById("id");
const ctx = canvas.getContext("2d");
```
<!-- more -->
# 绘制

fillRect(x, y, width, height) 绘制填充矩形
strokeRect(x, y, width, height) 矩形边框
clearRect(x, y, width, height) 清除指定区域
```
  const canvas = document.getElementById("canvas");
  const ctx = canvas.getContext("2d");

  ctx.fillRect(25, 25, 100, 100);
  ctx.clearRect(45, 45, 60, 60);
  ctx.strokeRect(50, 50, 50, 50);
```
beginPath() 新建路径
closePath() 关闭路径
lineTo() 连接
moveTo() 移动
fill() 填充
arc(x, y, radius, startAngle, endAngle, anticlockwise) //圆弧
quadraticCurveTo(cp1x, cp1y, x, y) //贝塞尔弧线

```
  const canvas = document.getElementById("canvas");
  const ctx = canvas.getContext("2d");
  ctx.beginPath();
  ctx.arc(75, 75, 50, 0, Math.PI * 2, true); // 绘制圆 anticlockwise 顺时针或者逆时针
  ctx.stroke();
```

## color

填充颜色,项目中有时候会用到colormap 插件
```
ctx.fillStyle = "orange"; //具体值或者rgba
ctx.fillRect(0, 37.5, 150, 37.5);

 var ctx = document.getElementById("canvas").getContext("2d");
  for (var i = 0; i < 6; i++) {
    for (var j = 0; j < 6; j++) {
      ctx.fillStyle =
        "rgb(" +
        Math.floor(255 - 42.5 * i) +
        "," +
        Math.floor(255 - 42.5 * j) +
        ",0)";
      ctx.fillRect(j * 25, i * 25, 25, 25);
    }
  }
```
globalAlpha 设置透明度
```
  ctx.globalAlpha = 0.2;
```

# line

lineWidth //线宽度
lineCap //线头样式 butt，round，square
lineJoin //线链接样子 round,bevel,miter
miterLimit //线限制
setLineDash //虚线
Shadows //阴影
```
ctx.shadowOffsetX = 2;
ctx.shadowOffsetY = 2;
ctx.shadowBlur = 2;
ctx.shadowColor = "rgba(0, 0, 0, 0.5)";
```
渐变 Gradients createLinearGradient(x1, y1, x2, y2) createRadialGradient(x1, y1, r1, x2, y2, r2) ....
Patterns
```
var img = new Image();
img.src = "someimage.png";
var ptrn = ctx.createPattern(img, "repeat");
```

# Text
填充
```
var ctx = document.getElementById("canvas").getContext("2d");
ctx.font = "48px serif";
ctx.fillText("Hello world", 10, 50);
```
# img
```
function draw() {
  var ctx = document.getElementById("canvas").getContext("2d");
  var img = new Image();
  img.onload = function () {
    ctx.drawImage(img, 0, 0);
    ctx.beginPath();
    ctx.moveTo(30, 96);
    ctx.lineTo(70, 66);
    ctx.lineTo(103, 76);
    ctx.lineTo(170, 15);
    ctx.stroke();
  };
  img.src = "backdrop.png";
}
```
#  Transformations

save restore 
save 保存当前状态，restore返回上一步状态

translat 移动 translate(x, y) 
rotate 旋转 rotate(angle)
scale 缩放 scale(x, y)
矩阵变换transform(a, b, c, d, e, f)
clip 裁剪

## 动画

步骤 1.清空 canvas  2.保存 canvas 3.绘制动画 4.恢复 canvas 状态

定时执行setInterval(function, delay),requestAnimationFrame(callback)
