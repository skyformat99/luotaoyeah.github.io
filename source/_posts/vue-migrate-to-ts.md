---
title: Vue.js 逐步迁移到 TypeScript
date: 2017-11-12 21:08:02
categories:
  - VUE.JS
tags:
  - vue
  - typescript
---

## 0. WHY ?

JavaScript 自有其作为动态语言的优势，但在面对增长越来越快，代码量越来越大的复杂应用时，其缺陷与不足也越来越明显，所谓"动态类型一时爽，代码重构火葬场"；而 TypeScript 作为 JavaScript 的超集，其引入的诸多特性，使得构建大型复杂应用成为可能。

从一开始就使用 TypeScript 来构建应用自然是最好的选择，如果应用一开始是使用 JavaScript 来构建，当增长到一定规模时，想要迁移到 TypeScript 应该如何实现呢 ?

## 1. 前置条件

本文假设原有项目是基于 vue-cli 的 webpack 模板进行搭建的。

## 2. 原理

在原有的基于 webpack 打包构建的流程之上，引入单独的处理器对 TypeScript 文件进行处理，实现 js 文件与 ts 文件共存且分别进行处理，从而实现 JavaScript 到 TypeScript 的逐步迁移。经过改造之后的项目将具备以下特性：
* js 文件和 ts 文件可以共存，互不影响；
* js 模块和 ts 模块可以相互引用；
* 支持单文件组件（SFC）迁移到 ts；

## 3. 配置

#### 3.1. 安装相关依赖

<script src="https://gist.github.com/luotaoyeah/45c663f135b757c18a2beeb4430a0669.js"></script>

#### 3.2. 修改 webpack 配置

* `resolve.extensions` 添加 `.ts`；
* 针对 ts 文件添加一个 `rule`；

<script src="https://gist.github.com/luotaoyeah/655ce5ad5ad942b14eec106e7259f74c.js"></script>

#### 3.3. 添加 tsconfig.json 配置文件

在根目录添加配置文件 `tsconfig.json`：

<script src="https://gist.github.com/luotaoyeah/1cf4b72c4da9cd950ef999c21d6c3ef9.js"></script>

其中配置项 `include` 使用了通配符：`["./src/**/*"]`，这样就实现了只有 ts 相关的文件才会被 TypeScript 处理；

> If a segment of a glob pattern includes only `*` or `.*`, then only files with supported extensions are included (e.g. .ts, .tsx, and .d.ts by default with .js and .jsx if allowJs is set to true).

> [https://www.typescriptlang.org/docs/handbook/tsconfig-json.html](https://www.typescriptlang.org/docs/handbook/tsconfig-json.html)

#### 3.4. 添加类型定义文件

* 在根目录新建文件夹 `types`，用来存放类型定义文件；
* 添加类型定义文件 `shim-vue.d.ts`：
<script src="https://gist.github.com/luotaoyeah/c38cc6de04d5b0392828ad28bb7c6269.js"></script>

## 4. 迁移

#### 4.1. js 文件

对于 js 文件，
* 将文件后缀修改为 `.ts`；
* 将 js 代码重构为 ts 代码；

#### 4.2. vue 文件

对于 vue 文件，
* 将 script 标签中的 js 代码分离到单独的 `.ts` 文件；
* 将 js 代码重构为 ts 代码；
* 在 vue 文件引入该 `.ts` 文件；

![](/images/vue-migrate-to-ts/vue-migrate-to-ts-01.png)
![](/images/vue-migrate-to-ts/vue-migrate-to-ts-02.png)
