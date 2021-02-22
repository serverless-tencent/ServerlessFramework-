---
title: 'Laravel 框架迁移'
menuText: 'Laravel 框架迁移'
layout: Doc
---

通过 [Serverless Framework Laravel 组件](https://github.com/serverless-components/tencent-laravel)，可以快速实现 Laravel 应用从本地到 Serverless 函数平台的迁移。


## 迁移前提

- 已经 [安装 Serverless Framework 1.67.2](../quickstart/installation) 以上版本。
- 已经[注册腾讯云账号](https://cloud.tencent.com/document/product/378/17985)并完成[实名认证](https://cloud.tencent.com/document/product/378/10495)。

  > 如果您的账户为**腾讯云子账号**，请首先联系主账号，参考 [账号和权限配置](../quickstart/credential) 进行授权。

 ## 架构说明

Laravel 组件将在腾讯云账号中使用到如下 Serverless 服务：

- **API 网关**：API 网关将会接收外部请求并且转发到 SCF 云函数中。
- **SCF 云函数**：云函数将承载 Laravel 应用。
- **CAM 访问控制**：该组件会创建默认 CAM 角色用于授权访问关联资源。
- **COS 对象存储**：为确保上传速度和质量，云函数压缩并上传代码时，会默认将代码包存储在特定命名的 COS 桶中

## 操作步骤

> 以下步骤主要针对命令行部署操作，控制台部署请参考[控制台部署指南](./console)。

### 1. （可选）初始化 Laravel 模版项目
如果您本地并没有 Laravel 项目，可通过以下指令完成 Laravel 项目初始化（本地已有项目可跳过该步骤）
```
serverless init laravel-starter --name example
cd example
```

### 2. 配置 yml 文件
在项目根目录下，新建 `serverless.yml` 文件，并将下列配置模版粘贴到文件中，实现基本的项目配置。
>基于您实际部署需要，您可以在 `serverless.yml` 中完成更多配置，yml 文件的配置信息请参考[ Laravel 组件全量配置](https://github.com/serverless-components/tencent-laravel/blob/master/docs/configure.md)

```sh
touch serverless.yml
```

```yml
# serverless.yml

component: laravel
name: laravelDemo

app: appDemo
stage: dev

inputs:
  src: ./
  region: ap-guangzhou
  runtime: Php7
  apigatewayConf:
    protocols:
      - http
      - https
    environment: release
```

### 3. 应用部署
通过 `sls deploy` 命令进行部署，并可以添加 --debug 参数查看部署过程中的信息。

```
sls deploy --debug
```
部署完成后，通过访问输出的 API 网关链接，完成对应用的访问。

### 4. 监控运维
部署完成后，您可以通过访问 [Serverless 应用控制台](https://console.cloud.tencent.com/ssr)，查看应用的基本信息，监控日志。
