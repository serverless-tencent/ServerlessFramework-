---
title: "函数开发(function)指南"
menuText: "函数开发(function)指南"
layout: Doc
---

> 与国际版本不同，Serverless 中国通过 SCF 组件实现了国际版的函数开发功能。

Serverless Framework 通过 SCF Component 提供了基于腾讯 SCF 的函数开发能力，用户可以使用 SCF Component 快速进行函数开发。

在函数开发的过程中，需要关注每个函数的两个参数，这两个参数是

- event：传递触发事件数据内容
- context：传递运行时信息内容

## 使用步骤

### 创建项目

```sh
# 使用命令行创建指定运行时项目
# sls init scf-nodejs
```

> 更多使用介绍请查看[函数应用开发](../quickstart/function-dev).

Serverless Framework 支持创建以下语言的函数应用模板

- Node.js 函数应用([scf-nodejs](https://github.com/serverless-components/tencent-examples/tree/master/scf-nodejs)): Node.js 10.15 和 Node.js12.16
- Python 函数应用([scf-python](https://github.com/serverless-components/tencent-examples/tree/master/scf-python)): Python2.7 和 Python3.6
- Golang 函数应用([scf-golang](https://github.com/serverless-components/tencent-examples/tree/master/scf-golang)): Golang1.8 及以上版本
- PHP 函数应用([scf-php](https://github.com/serverless-components/tencent-examples/tree/master/scf-php)): PHP5 和 PHP7

**下一步：开始函数开发**

- [函数 CLI 命令](./function-commands)
- [Node.js 函数应用开发](./nodejs)
- [Python 函数应用开发](./python)
- [Golang 函数应用开发](./golang)
- [PHP 函数应用站点](./php)
