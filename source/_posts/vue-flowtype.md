---
title: Vue.js 2 项目配置 Flowtype 静态类型检查
date: 2017-08-02 20:07:37
categories:
  - vue
tags:
  - vue
  - flowtype
---

## 1. Flowtype 是什么 ?

[Flowtype](https://flow.org/) 是 Facebook 开源的一款针对 Javascript 的静态类型检查工具。Flowtype 的出现弥补了 Javascript 作为一种动态类型语言先天性的类型系统缺陷，使用 Flowtype 对现有的代码进行类型标注之后，Flowtype 可以在编译时进行类型检查，避免很多运行时才能发现的错误。

Vue.js 从 2.0 开始采用了 Flowtype，作者尤雨溪在知乎上对为什么使用 Flowtype 有一篇回答：[Vue2.0 为什么选用 Flow 进行静态代码检查而不是直接使用 TypeScript ?](https://www.zhihu.com/question/46397274)

## 2. 如何在基于 Vue.js 的项目中使用 Flowtype ?

> 本文示例项目使用 [vue-cli](https://github.com/vuejs/vue-cli) 的 [webpack-simple](https://github.com/vuejs-templates/webpack-simple) 模板搭建。

### 2.1 安装依赖包

* 安装 Babel 相关依赖包：

```bash
npm install babel-plugin-syntax-flow babel-plugin-transform-class-properties babel-plugin-transform-flow-strip-types -D
```

* 安装 ESLint 相关依赖包：

```bash
npm install eslint-plugin-html eslint-plugin-flowtype-errors eslint-plugin-vue -D
```

* 安装 Flowtype 相关依赖包：

```bash
npm install flow-bin -D
```

### 2.2 配置插件

项目根目录下已经存在`.babelrc`和`.eslintrc.js`配置文件，更新配置如下：

```javascript .babelrc
{
  "plugins": [
    "syntax-flow",
    "transform-flow-strip-types"
  ]
}
```

```javascript .eslintrc.js
{
  "plugins": [
    "vue",
    "flowtype-errors"
  ],
  "rules": {
    "flowtype-errors/show-errors": 2
  }
}
```

`.flowconfig`需要手动添加，或者在项目根目录执行`flow init`来生成。

```config .flowconfig
[ignore]
.*/test/.*
.*/build/.*
.*/config/.*
[include]

[libs]

[options]
module.file_ext=.vue
module.file_ext=.js
module.file_ext=.scss
```

### 2.3 设置IDE

Intellij IDEA（或 WebStorm） 对 Flowtype 提供了很好的支持，结合官方的 Vue.js 插件，编写 Vue.js 单文件组件变得特别方便。

#### 2.3.1 启用 Node.js and NPM

![](/images/vue-flowtype/vue-flowtype-01.png)

#### 2.3.2 设置 Javascript 语言版本为 Flow

[WebStorm 官方帮助文档](https://www.jetbrains.com/help/webstorm/2017.1/flow-type-checker.html)

![](/images/vue-flowtype/vue-flowtype-02.png)

#### 2.3.3 设置 Flowtype 相关 Inspection

![](/images/vue-flowtype/vue-flowtype-03.png)

### 2.4 使用 Flowtype

在 .vue 文件 script 块中，添加注释`/* @flow */`或者`// @flow`来启用 Flowtype：

```html index.vue
<template></template>

<script type="text/babel">
  /* @flow */
  const str: string = 1;
</script>

<style lang="scss"></style>

```


此时 IDE 检测到文件变化时，会自动运行 ESLint；由于我们配置了 ESLint 和 Flowtype 集成的相关插件，Flowtype errors 会以 ESLint errors 的形式实时地显示到控制台，而不用手动运行`flow check`命令：

![](/images/vue-flowtype/vue-flowtype-04.png)
