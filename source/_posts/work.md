---
title: work
date: 2025-02-13 11:35:33
tags: 实操
categories: 日常
description : 实际遇到的问题和解决方法
---

### 疑难杂症

- 在 vite2 的项目中，使用 postcss-pxtorem6.0.0 版本的插件将 px 单位转换为 rem，会出现 bug。
  在开发阶段没什么问题，但打包后的样式会出现某些元素的单位转换不成功，每次打包到的结果都不一样
  解决：降低插件版本为 5.1.1 即可

**在 vue2 中使用防抖和节流，需要将方法从外部导入，然后在组件中声明方法时，使用`:`的方式，而不是直接写一个函数**

```javascript
methods: {
  myFun: debounce(function() {
    // 真正的函数代码
  }, 2000);
}
```

- tailwindcss配合element-plus使用时，tailwind的默认样式会覆盖element的按钮默认样式，需要在tailwind的配置文件中手动重写按钮样式
```javascript
module.exports = {
  plugins: [
    // 解决覆盖了 element-plus 按钮样式的问题
    function ({ addBase }) {
      addBase({
        ".el-button": {
          "background-color": "var(--el-button-bg-color,val(--el-color-white))"
        }
      });
    }
  ]
};
```

- tailwind中有一些类名比较特殊，比如`container`，所以在使用时，要多加小心，以免造成样式冲突，而且还不好排查。只能将可疑的类名去tailwind官网查一下。
