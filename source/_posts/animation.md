---
title: css动画
date: 2022-04-24
tags: 语法
categories: CSS
description: 一些好用，使用的css样式和动画
---

### 加载动画
一个简单的加载动画，带粘滞效果
```html
<style>
  body{
    height: 1000px;
    position: relative;
    background: linear-gradient(20deg,rgb(255, 186, 186),rgb(187, 255, 187),rgb(172, 172, 255));
  }
  .bigb{
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    width: 200px;
    height: 200px;
    border-radius: 50%;
    margin: auto;
    background-color: #fff;
    /* 设置对比度，配合blur可设置粘滞效果 */
    filter: contrast(10);
  }
  .smallb{
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    width: 70px;
    height: 70px;
    border-radius: 50%;
    margin: auto;
    background-color: #000;
    /* 设置高斯模糊，配合contrast可设置粘滞效果 */
    filter: blur(5px);
  }
  .ball{
    position: absolute;
    top: 0;
    bottom: 0;
    margin: auto;
    width: 50px;
    height: 50px;
    border-radius: 50%;
    margin: auto;
    background-color: red;
    filter: blur(5px);
    /* 动画 */
    animation: move 2s linear infinite;
  }
  /* 帧动画 */
  @keyframes move {
    0% {
      left: 5px;
      width: 50px;
      height: 35px;
    }
    25%{
      left: 80px;
      width: 40px;
      height: 45px;
    }
    50%{
      left: 145px;
      width: 50px;
      height: 30px;
    }
    75%{
      left: 70px;
      width: 45px;
      height: 40px;
    }
    100%{
      left: 5px;
      width: 50px;
      height: 35px;
    }
  }
</style>

<body>
  <div class="bigb">
    <div class="smallb"></div>
    <div class="ball"></div>
  </div>
</body>
```
效果如下图：
{% gallery %}
  ![animation](./animation/ani1.png)
  ![animation](./animation/ani2.png)
  ![animation](./animation/ani3.png)
{% endgallery %}
**设置粘滞效果时，背景一定要用纯色，不要带透明度那些，否则会有模糊效果**
