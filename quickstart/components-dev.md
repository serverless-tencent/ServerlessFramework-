---
title: "组件应用开发"
menuText: "组件应用开发"
layout: Doc
---

Serverless Components 是 Serverless Framework 的一部分，通过定制的运行环境带来了更多的对传统框架应用的支持，让传统框架应用也可以获得 serverless 的优势，同时不需要花费时间来配置传统框架应用的运行时。

> 本教程以开发一个 Express API 的案例说明如何通过 Serverless Components 来使用腾讯 SCF 和网关构建一个后端 API 应用，并获得 serverless 的自动扩容并按量付费能力。

## 初始化组件应用

在**空目录**下，执行初始化命令：

```sh
# 交互式 serverless 初始化命令
$ serverless
```

接下来按照交互提示，完成项目初始化，选择 `express-starter` 组件模版，并等待依赖安装结束：

```sh
Serverless: 当前未检测到 Serverless 项目，是否希望新建一个项目？ Yes
Serverless: 请选择您希望创建的 Serverless 应用 express-starter

  eggjs-starter - 快速部署一个Egg.js 基础应用
❯ express-starter - 快速部署一个 Express.js 基础应用
  flask-starter - 快速部署一个 Flask 基础应用
  fullstack - 快速部署一个 Full Stack 应用, vuejs + express + postgres
  koa-starter - 快速部署一个 Koa.js 基础应用
  laravel-starter - 快速部署一个 Laravel 基础应用
  nextjs-starter - 快速部署一个 nextjs 应用

Serverless: 请输入项目名称 my-sls-express
Serverless: 正在安装 express-starter 应用...

- 项目 "my-sls-express" 已在当前目录成功创建
- 执行 "cd my-sls-express && serverless deploy" 部署应用

express-starter › 创建成功
```

应用创建完成之后，如果想要部署，可以选择【立即部署】并将已经初始化好的项目快速部署腾讯云平台：

> 如果不想部署可以不立即部署到云端，并在稍后通过`serverless deploy`进行手动部署。  
> 如果想要修改应用名称，或者进行其他调整请参考[YAML 配置](../basic/yaml)中的说明进行调整。

```sh
Serverless: 是否希望立即将该项目部署到云端？ Yes

xxxxxxxx
x  QR  x
x CODE x
xxxxxxxx

请使用微信扫描上方二维码或者点击下方链接登录
https://slslogin.qcloud.com/3N2f3dTv
登录成功！

serverless ⚡components
Action: "deploy" - Stage: "dev" - App: "my-sls-express-18525a69" - Instance: "express-starter"

region: ap-guangzhou
scf:
  functionName: express_component_bv2n5cc
  runtime:      Nodejs10.15
  namespace:    default
  lastVersion:  $LATEST
  traffic:      1
apigw:
  serviceId:   service-mt1d84ea
  subDomain:   service-mt1d84ea-xxxxxxxxxx.gz.apigw.tencentcs.com
  environment: release
  url:         https://service-mt1d84ea-xxxxxxxxxx.gz.apigw.tencentcs.com/release/

应用控制台: https://console.cloud.tencent.com/ssr/detail?stageName=dev&appName=my-sls-express-bv2n5cc&instanceName=express-starter&stageList=dev

102s › express-starter › 部署成功
```

> 如果有使用全局密钥有可能会与上面流程不同，关于登陆的更多方式和说明请查看[腾讯云账号控制](../basic/tencent-account.md)相关内容获得更详细帮助。

部署成功后会显示

- 当前部署组件实例的环境(stage)，应用，地区，信息。
- 已部署的云端组件(SCF 和 API 网关)的信息信息（运行时，命名空间，访问地址，环境信息）等。
- (腾讯)应用控制台信息，在这里可以查看管理该组件应用。

> 所有基础设施信息都默认自动生成，如果需要修改请到项目目录 serverless.yml 文件中进行修改 。

部署成功后访问 API 网关地址就可以访问部署应用。

## 修改配置

Serverless Framework 项目创建成功后会在目录生成 `serverless.yml`，这是 serverless 唯一的配置文件，所有配置都可以在这里进行快速修改。以下是自动生成的 serverless 配置文件

```yml
# ##应用信息##
app: my-sls-express-18525a69 # app名称(app唯一识别标识)。同账号下需唯一，留空则继承组件实例名称
component: express # [必选]要使用组件，更多组件请查看 https://github.com/serverless-components
name: express-starter # [必选]组件实例名称

# ##express 组件配置##
# 更多配置请查看：https://github.com/serverless-components/tencent-express/blob/master/docs/configure.md
inputs:
  src: # 执行目录
    src: ./
    exclude:
      - .env
  region: ap-guangzhou # 部署目标地区。 更多参考 https://cloud.tencent.com/document/api/583/17238#.E5.9C.B0.E5.9F.9F.E5.88.97.E8.A1.A8
  runtime: Nodejs10.15 # 运行环境。[Nodejs10.15, Nodejs12.16]
  apigatewayConf: # API 网关
    protocols:
      - http
      - https
    environment: release # 发布环境。[test, prepub，release]
```

这里需要注意：

> `app` 是应用名称，同时也作为云端应用的唯一标识。应用名称如果修改，会在云端重新部署为新的应用。  
> `name` 是 component 组件实例的名称，一个应用可以包含多个组件。  
> 每个账号下同一名称的应用只能存在一个，部署会按照应用名称创建/覆盖应用。同一应用下的组件在也会按照组件名创建/覆盖组件实例。  
> `inputs` 是组件配置的参数，所有的组件配置都需要将配置信息写在 inputs 中。

## 调试开发

Serverless Framework 提供了快速灵活的调试模式来替代云平台的复杂费时的调试方式。使用 `serverless dev` 就可以快速开启调试模式。

```sh
# 进入 serverless 调试模式
$ serverless dev

serverless ⚡components
Dev Mode - 项目监控中，任何变更都会通过日志输出

远程调试链接：ws://127.0.0.1:9222
更多信息请参考：https://nodejs.org/en/docs/inspector
请打开 Chrome 浏览器，地址栏访问 chrome://inspect, 点击 [Open dedicated DevTools for Node] 开始调试代码
--------------------- 实时日志 ---------------------
6:35:52 PM - express-starter - deployment
region: ap-guangzhou
scf:
  functionName: express_component_bv2n5cc
  runtime:      Nodejs10.15
  namespace:    default
  lastVersion:  $LATEST
  traffic:      1
apigw:
  serviceId:   service-mt1d84ea
  subDomain:   service-mt1d84ea-xxxxxxxxxx.gz.apigw.tencentcs.com
  environment: release
  url:         https://service-mt1d84ea-xxxxxxxxxx.gz.apigw.tencentcs.com/release/

express-starter › 监听中 ...
```

加下来修改代码保存后都会立即部署到腾讯云上，并可以立即开始测试。更多关于调试模式的使用方法请参考[调试模式](../basic/dev-mode.md)

> 调试模式不建议频繁保存代码，每次代码保存都会触发部署，过于频繁保存可能导致调试模式出现异常。

## 部署更新代码

在代码调试满意之后，通过一下命令部署代码到腾讯云。

```sh
# 部署项目代码到云服务器
$ serverless deploy
```

## 查看部署信息

如果希望再次查看应用的部署状态和资源，可以进入到部署成功的文件夹，运行如下命令，查看对应信息：

```sh
# 查看已部署应用信息
$ serverless info

serverless ⚡components


最后操作:  deploy (a few seconds ago)
部署次数:  6
应用状态:  active

region: ap-guangzhou
scf:
  functionName: express_component_bv2n5cc
  runtime:      Nodejs10.15
  namespace:    default
  lastVersion:  $LATEST
  traffic:      1
apigw:
  serviceId:   service-mt1d84ea
  subDomain:   service-mt1d84ea-xxxxxxxxxx.gz.apigw.tencentcs.com
  environment: release
  url:         https://service-mt1d84ea-xxxxxxxxxx.gz.apigw.tencentcs.com/release/

应用控制台: https://console.cloud.tencent.com/ssr/detail?stageName=dev&appName=my-sls-express-bv2n5cc&instanceName=express-starter&stageList=dev

express-starter › 信息成功加载
```

**下一步：[了解组件开发(component)指南](../components/README)**

