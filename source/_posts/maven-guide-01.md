---
title: Maven 入门指南 ① ：Maven 快速入门及简单使用
date: 2014-06-02 20:07:37
categories:
  - MAVEN
tags: maven
---

## 0. 开发环境

- MyEclipse 2014
- JDK 1.8
- Maven 3.2.1

## 1. 什么是 Maven ？

[Maven](https://maven.apache.org/) 是一个 Java 语言编写的开源项目管理工具，是 Apache 软件基金会的顶级项目。主要用于项目构建，依赖管理，项目信息管理。

## 2. 下载及安装

- 下载：[apache-maven-3.2.1-bin.zip](https://archive.apache.org/dist/maven/maven-3/3.2.1/binaries/apache-maven-3.2.1-bin.zip)；
- 解压：![](/images/maven-guide-01/maven-guide-01-001.png)
- 配置环境变量（需要先配置好 `%JAVA_HOME%` 环境变量）：![](/images/maven-guide-01/maven-guide-01-002.png) ![](/images/maven-guide-01/maven-guide-01-003.png)
- `mvn -v` 检查环境变量是否配置成功：![](/images/maven-guide-01/maven-guide-01-004.png)
- 配置文件 `settings.xml`：
  - `%MAVEN_HOME%/conf` 目录下的 `settings.xml`，该配置文件针对系统所有用户：![](/images/maven-guide-01/maven-guide-01-005.png)
  - 用户目录下 `.m2` 下的 `settings.xml`，针对当前用户；默认没有，可自行复制一份：![](/images/maven-guide-01/maven-guide-01-006.png)

## 3. Maven 项目的目录结构

![](/images/maven-guide-01/maven-guide-01-007.png)

- `src/main/java` ： 存放项目.java 文件；
- `src/main/resources` ： 存放项目资源文件；
- `src/test/java` ： 存放测试类.java 文件；
- `src/test/resources` ： 存放测试资源文件；
- `target` ： 项目输出目录；
- `pom.xml` ： Maven 核心文件（Project Object Model）；

## 4. 使用 MyEclipse 创建 Maven 项目

1. MyEclipse 提供了 Maven4MyEclipse 插件，已经内置安装了 Maven；但我们通常使用自己下载配置的 Maven，在 Installations 选项中添加我们刚才下载配置的 Maven。Eclipse 可自行安装 m2eclipse 插件。
   ![](/images/maven-guide-01/maven-guide-01-008.png)

2. 新建一个 `Maven Project`：![](/images/maven-guide-01/maven-guide-01-009.png)

3. 选择 `Workbench Location`：![](/images/maven-guide-01/maven-guide-01-010.png)

4. 选择 `Maven Archetype`，这里我们选择 `maven-archetype-webapp`：![](/images/maven-guide-01/maven-guide-01-011.png)

5. 设置 Maven 相关参数，`Group Id`，`Artifact Id`，`Version`，`Package`，它们共同组成 Maven 的坐标：![](/images/maven-guide-01/maven-guide-01-012.png)

## 5. 后续配置

项目创建成功之后，我们看到 MyEclipse 默认只为我们创建了 `src/main/resources`，打开项目属性 - Java Build Path - Source 一栏，可看到 `src/main/java` 和 `src/test/java` 显示 missing：
![](/images/maven-guide-01/maven-guide-01-013.png)

打开 Libraries，编辑 JRE 环境，选择自己安装的 JDK：
![](/images/maven-guide-01/maven-guide-01-014.png) ![](/images/maven-guide-01/maven-guide-01-015.png)

这时，`src/main/java` 和 `src/test/java` 已经出来了，再自行添加 `src/test/resources` 目录：
![](/images/maven-guide-01/maven-guide-01-016.png)

接下来，设置 Project Facets。选择 Java 1.7 版本和 Dynamic Web Module 3.1 版本：
![](/images/maven-guide-01/maven-guide-01-017.png)

设置项目输出目录如下：
![](/images/maven-guide-01/maven-guide-01-018.png)

设置部署打包结构如下，删除测试相关目录：
![](/images/maven-guide-01/maven-guide-01-019.png)

## 6. 在 pom.xml 文件中添加相关 jar 包依赖

打开 `pom.xml` 文件，我们看到 Maven 已经为我们添加了 JUnit3.8.1 的依赖，这里我们改成 JUnit 4.11，同时添加 servlet-api 的依赖包：
![](/images/maven-guide-01/maven-guide-01-020.png)

保存之后，自动为我们添加了相关 jar 包。这时最开始缺少 servlet-api 相关类的错误提示已经没有了：
![](/images/maven-guide-01/maven-guide-01-021.png)

## 7. 构建及部署

右键 pom.xml - Run As - Maven -install：
![](/images/maven-guide-01/maven-guide-01-022.png)

打开 target 文件夹，我们看到生成了项目的部署文件：
![](/images/maven-guide-01/maven-guide-01-023.png)

部署项目到 Tomcat 应用服务器，访问项目：
![](/images/maven-guide-01/maven-guide-01-024.png)
