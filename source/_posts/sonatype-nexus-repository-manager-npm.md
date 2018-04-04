---
title: 使用 Nexus Repository Manager 搭建 NPM 私服
date: 2017-12-05 17:03:35
categories:
  - NODE.JS
tags:
  - nodejs
  - npm
  - nexus
---

## 0. WHY ?
网络环境太差的时候，使用 `npm install` 安装项目依赖是一件特别痛苦的事情。想到我以前使用 Nexus 搭建 Maven 私服的经历（[Maven入门指南⑤：使用Nexus搭建Maven私服](http://www.cnblogs.com/luotaoyeah/p/3791966.html)），我搜索了一下类似的搭建 NPM 私服的方法，发现 Nexus 竟然也同时支持创建 NPM 私服。经验证，效果还不错，强烈推荐大家试用。

### 0.1 参考文档

1. [Using Nexus 3 as Your Repository – Part 2: npm Packages](http://blog.sonatype.com/using-nexus-3-as-your-repository-part-2-npm-packages)
2. [Node Packaged Modules and npm Registries](https://help.sonatype.com/display/NXRM3/Node+Packaged+Modules+and+npm+Registries)

## 1. 创建私有仓库
私有仓库是用来发布公司内部或者个人私有项目的地方。项目发布到私有仓库之后，公司内部就可以像使用普通的 NPM 包一样安装 & 更新该项目，同时又不会将项目代码泄露到公共仓库。

1. 创建仓库：![](/images/sonatype-nexus-repository-manager-npm/create-repository.png)
2. 选择 `npm (hosted)`：![](/images/sonatype-nexus-repository-manager-npm/select-npm-hosted.png)
3. 填写名称，并保存：![](/images/sonatype-nexus-repository-manager-npm/config-npm-hosted.png)

## 2. 创建代理仓库
代理仓库是用来缓存远程仓库依赖的地方。通过代理仓库安装项目依赖时，Nexus 首先会查找代理仓库，如果找到该依赖，则直接从代理仓库下载该依赖到本地；如果没找到该依赖，则首先从远程仓库（[http://registry.npmjs.org/](http://registry.npmjs.org/)）下载该依赖到本地，并将该依赖缓存到代理仓库，以后再次安装依赖时，就直接从代理仓库下载。

1. 创建仓库：![](/images/sonatype-nexus-repository-manager-npm/create-repository.png)
2. 选择 `npm (proxy)`：![](/images/sonatype-nexus-repository-manager-npm/select-npm-proxy.png)
3. 填写名称及远程仓库地址 `http://registry.npmjs.org/`，并保存：![](/images/sonatype-nexus-repository-manager-npm/config-npm-proxy.png)

## 3. 创建仓库分组
仓库分组用来将多个仓库（上面创建的私有仓库和代理仓库）放到一个分组中，然后通过一个唯一的 URL 地址来同时访问这些仓库。

1. 创建仓库：![](/images/sonatype-nexus-repository-manager-npm/create-repository.png)
2. 选择 `npm (group)`：![](/images/sonatype-nexus-repository-manager-npm/select-npm-group.png)
3. 填写名称，勾选私有仓库和代理仓库到该分组，并保存：![](/images/sonatype-nexus-repository-manager-npm/config-npm-group.png)
4. 查看仓库分组信息如下，其中 `http://192.168.199.10:8081/repository/npm-group/` 就是我们要使用的唯一仓库 URL：![](/images/sonatype-nexus-repository-manager-npm/npm-group-info.png)

## 4. 使用代理仓库安装依赖
接下来我们使用刚刚创建的代理仓库来安装项目依赖。

### 4.1. 通过命令行参数，指定仓库地址

```bash
npm install --registry http://192.168.199.10:8081/repository/npm-group/ --verbose
```

可以看到已经开始从代理仓库下载依赖：
![](/images/sonatype-nexus-repository-manager-npm/npm-install-registry.png)

### 4.2. 修改 NPM 配置
通过修改 NPM 配置使用代理仓库，这样不用每次运行 `npm install` 时都需要通过参数指定仓库地址。可以使用命令或者直接修改配置文件。

#### 4.2.1. 使用命令修改 NPM 配置

```bash
npm config set registry http://192.168.199.10:8081/repository/npm-group/
```

#### 4.2.2. 直接修改 NPM 配置文件

 ```bash
# NPM 用户配置文件：C:/Users/[用户名]/.npmrc
registry=http://192.168.199.10:8081/repository/npm-group/
```

## 5. 发布项目到私有仓库
发布项目到私有仓库，需要首先配置权限认证。

### 5.1. 生成 `auth hash`
使用命令 `echo -n 'username:password' | openssl base64` 来生成 `auth hash`：
![](/images/sonatype-nexus-repository-manager-npm/anth-hash.png)

### 5.2. 修改 NPM 配置文件
```bash
registry=http://192.168.199.10:8081/repository/npm-group/
email=luotaoyeah@gmail.com
always-auth=true
_auth="YWRtaW46YWRtaW4="
```

### 5.3. 发布项目
两种方式指定发布到的仓库地址，命令参数或者配置 `package.json`：

#### 5.3.1. 通过命令行参数，指定发布仓库地址
```bash
npm publish --registry http://192.168.199.10:8081/repository/npm-group/
```
#### 5.3.2. 修改 `package.json`，指定发布仓库地址
修改 `package.json` 中 `publishConfig` 配置，指定发布地址：

```json
"publishConfig": {
    "registry": "http://192.168.199.10:8081/repository/npm-private/"
}
```

然后直接执行发布命令 `npm publish` 即可，项目成功发布到私有仓库：
![](/images/sonatype-nexus-repository-manager-npm/publish-success.png)
