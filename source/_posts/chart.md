---
title: chart
date: 2023-12-05 09:31:08
tags: 库
description : 主流的图表库
---
# echarts

 **title**

> 图表的标题

配置项（部分）：

- text：标题文字
- textStyle：标题的文字样式（object）
- padding：标题的内边距，多个值放数组中
- top：标题的上边距
- zlevel：层级，数值大的在上面

**legend**

> 图表的图例

配置项（部分）：

- type：图例类型。scroll：可滚动
- orient：图例的方向
- itemGap：图例之间的间距
- itemWidth：图例图形的宽度
- itemStyle：图例图形样式（object）
- lineStyle：图例线条样式，线条折线图之类的才有（object）
- formatter：处理图例的文字，可直接写字符串，也可写函数，接收的参数就是系列的 name
- selectedMode：是否可以通过点击图例控制数据的显示
- textStyle：图例文字的公共样式（object）
- icon：图例的图形图标
- data：图例的数据数组。数组项通常为一个字符串，每一项代表一个系列的 name。如果 data 没有被指定，会自动从当前系列中获取。
  可以设置多个对象，单独为每一个图例配置名字，图标和样式等...

**xAxis 和 yAxis**

> 图标的坐标轴

配置项（部分）：

- type：坐标轴类型。'value'：数值轴，适用于连续数据。'category'：类目轴，适用于离散的类目数据。'time'：时间轴，适用于连续的时序数据。'log' 对数轴
- alignTicks：当坐标轴为数值轴的时候，开启这个配置可以自动对齐刻度，只对 type 为'value'和'log'类型的轴有效
- name：坐标轴名称
- nameLocation：名称位置
- nameTextStyle：名称文字样式（object）
- nameGap：名称和坐标轴的间距
- nameRotate：名称旋转角度
- boundaryGap：坐标轴两边是否留一部分空白，默认值为 true
- min：坐标轴最小刻度值，可设置为特殊值'dataMin'，自动获取数据的最小值。也可设置为函数，接收参数 value，value.min 表示数据中的最小值
- max：坐标轴最大刻度值，可设置为特殊值'dataMax
- scale：是否是脱离 0 值比例。设置成 true 后坐标刻度不会强制包含零刻度。在设置 min 和 max 之后该配置项无效。
