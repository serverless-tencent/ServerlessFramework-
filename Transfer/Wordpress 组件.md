
腾讯云 Serverless 提供了基于 Serverless 架构的 Wordpress 全新部署方式，通过[Serverless Framework Wordpress 组件](https://github.com/serverless-components/tencent-wordpress) ，仅需几步，就可以快速部署一个 Wordpress 项目。

### 架构简介
该组件主要为您创建以下资源：

| 模块 | 说明 | 
|---------|---------|
| SCF 云函数| 负责 Serverless Wordpress 的接入层实现，从而运行 WordPress |
| API 网关| WordPress 的对外入口，实现了 RESTful API |
| CFS  | WordPress 的 Serverless 存储仓库 |
| TDSQL-C Serverless | 通过创建 TDSQL-C Serverless (原 CynosDB) 的 MySQL 类型数据库，实现数据库按量计费，自动扩缩容|
| VPC | 内网打通SCF云函数、CFS、TDSQL-C Serverless之间的网络，保障网络隔离 |

### 功能优势
1. 支持 Wordpress 原生框架

   使用 Serverless Wordpress 组件，您不需要对原生 Wordpress 项目进行任何改造，即可直接完成部署，做到对框架无入侵，也支持后续的版本升级。

2. 降低使用成本
   
   从接入层到计算层到存储层，全部使用 Serverless 资源，真正做到按量计费，弹性伸缩，大大节省成本

   ```
   示例:以一个个人博客网站为例，设定日访问量 100，1 GB 文件存储，1 GB 数据库存储，每月费用如下：
   - API 网关
     调用次数：100/10000*0.06*30=0.018元/月
     出流量：100*30/1024/1024*0.8*30=0.068元/月

   - SCF 云函数
     SCF 调用次数：100*30=3000次/月 免费额度内，不产生费用
     SCF 资源使用费用：（30/1000*100*30=90 GBs/月 免费额度内，不产生费用

   - CFS 存储费用（月费用）：1*0.35=0.35元/月

   - Serverless MySQL 数据库
     存储费用：1*0.00485元/GB/小时*24*30=3.49 元/月
     计算费用：100*0.000095*30=0.285 元/月

     合计: 0.018+0.068+0.35+3.49+0.285=4.211元
   ```
   对比可以发现，与传统自建方案对比，Serverless Wordpress 使用成本大大降低

3. 部署步骤简单

   通过 Serverless Wordpress 组件，只需几行 yml 文件配置，即可快速完成 Wordpress 应用部署，极大降低部署门槛

新用户第一次部署 Wordpress 应用，即可获得 **30 元 TDSQL-C** ，**5 元 CFS 文件存储**代金券，欢迎免费体验！

### 部署步骤
您可以通过 **命令行** 或 **控制台** 完成 Serverless Wordpress 部署，步骤如下：

#### 部署前提
- 开通[ SCF 云函数服务](https://console.cloud.tencent.com/scf)
- 开通[ CFS 文件存储服务](https://console.cloud.tencent.com/cfs)
- (可选)准备好已备案的自定义域名，您也可以通过[Serverless 备案资源包](https://cloud.tencent.com/document/product/583/45477)完成备案

#### 控制台部署
- 步骤一：登陆[ Serverless 应用控制台](https://console.cloud.tencent.com/sls?from=wpdocs), 点击"新建应用"
![](https://main.qcloudimg.com/raw/5fd422a7022b8ed0c8f6960bb7c0bc4b.png)
- 步骤二：根据指引，填入应用名称，选择"应用模版"--"Wordpress 应用"，点击"创建"
![](https://main.qcloudimg.com/raw/f197ee115867600bca2f87dc4a64fc20.png)
> 注意：目前只支持北京、广州、南京、上海四个区域

- 步骤三：创建完成后，点击"访问应用"，即可访问您的 Wordpress 项目，您也可以在应用详情页完成自定义域名的配置
![](https://main.qcloudimg.com/raw/c7467118e469cd619482659bc70449bb.png)

#### 命令行部署
- **步骤一：** 本地初始化 Serverless Wordpress 配置文件
```
sls init wordpress --name example
```

- **步骤二：** 进入目录下，执行 `sls deploy`, 即可完成部署
```
$ sls deploy

serverless ⚡framework

Action: "deploy" - Stage: "dev" - App: "appDemo" - Instance: "wordpressDemo"

region:       ap-shanghai
zone:         ap-shanghai-2
vpc: 
  ...

cfs: 
  ...

db: 
  ...

apigw: 
  created:     true
  url:         https://service-xxxxx.sh.apigw.tencentcs.com/release/
  ...

layer: 
  ...

wpInitFaas: 
  ...

wpServerFaas: 
  ...

```
部署成功后，点击 `apigw` 部分输出 url，根据指引完成账号密码配置，即可开始使用您的 Wordpress 应用。


如果想要配置自定义域名，您也可以修改 `serverless.yml` 配置文件，完成应用信息配置，或部署完成后在控制台修改，详细配置内容，请参考[全量配置文档](https://github.com/serverless-components/tencent-wordpress/blob/master/docs/configure.md)

> 注意：目前只支持 `ap-guangzhou-4`, `ap-shanghai-2`, `ap-beijing-3`, `ap-nanjing-1` 四个可用区。

