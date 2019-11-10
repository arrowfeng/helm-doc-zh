---
description: Kubernetes 的包管理器
---

# Helm

## 概述

Kubernetes 包管理器

要开始 Helm，首先需要执行命令 \`helm init\`：

```text
$ helm init
```

这将会安装 tiller 到你的 Kubernetes 集群，它也将自动安装任何所需要的本地配置。

常见的命令包括：

* helm search：搜寻 Charts
* helm fetch：下载 Charts 到你的本地目录
* helm install：上传 Charts 到 Kubernetes
* helm list：列出 Charts 的版本

环境变量：

* $HELM-HOME: 设置 Helm 的文件路径，默认为 ～/.helm
* $HELM-HOST:  设置 Tiller 的主机，格式为 host:port
* $HELM-NO-PLUGINS: 关闭插件，设置 HELM-NO-PLUGINS=1 关闭插件
* $TILLER-NAMESPACE:  设置 Tiller 的命名空间，默认为 kube-system
* $KUBECONFIG:  设置 Kubernetes 配置文件，默认为 ～/.kube/config
* $HELM-TLS-CA-CERT:  设置验证 Helm 客户端和 Tiller 服务端证书的 TLS CA 证书文件路径，默认为  $HELM-HOME/ca.pem
* $HELM-TLS-CERT: 设置 Helm 客户端向 Tiller 服务端验证证书的文件路径，默认为  $HELM-HOME/cert.pem
* $HELM-TLS-KEY: 设置 Helm 客户端向 Tiller 服务端验证密钥的文件路径，默认为  $HELM-HOME/key.pem
* $HELM-TLS-ENABLE: 在 Helm 和 Tiller 之间开启 TLS 连接，默认为关闭
* $HELM-TLS-VERIFY: 在 Helm 和 Tiller 之间开启 TLS 连接并验证 Tiller 服务端证书，默认为 false
* $HELM-TLS-HOSTNAME: hostname 或者 IP 地址被用来验证 Tiller 服务端证书，默认为127.0.0.1
* $HELM-KEY-PASSPHRASE: 设置你的 PGP 私钥的密码短语。如果设置了，在签名 helm charts 时你将不会被提示输入密码。

## 选项

```text
      --debug                           启用详细输出
  -h, --help                            helm 帮助
      --home string                     指定你的 Helm config，覆盖 $HELM-HOME（默认为～/.helm）
      --host string                     指定 Tiller 的地址，覆盖 $HELM-HOST
      --kube-context string             指定要操作的 Kubernetes context
      --kubeconfig string               指定要使用的 kubeconfig 文件的绝对路径
      --tiller-connection-timeout int   
      The duration (in seconds) Helm will wait to establish a connection to Tiller (default 300)
      --tiller-namespace string         Namespace of Tiller (default "kube-system")
```

## 其他命令



* [helm completion](helm-completion.md) - 对于指定的shell（bash 或 zsh）产生自动补全脚本
* [helm create](helm-create.md) - 使用给定的名字创建新的 chart
* [helm delete ](helm-delete.md)- 从 Kubernetes 中删除指定的 release
* [helm dependency](helm-dependency.md) - 管理 chart 的依赖
* [helm fetch ](helm-fetch.md)- 从仓库下载 chart 并解压它到本地目录
* [helm get](helm-get.md) - 下载指定名字的 release
* [helm history](helm-history.md) - 获取 release 历史版本
* [helm home](helm-home.md) - 显示 HELM—HOME 的目录
* [helm init ](helm-init.md)- 初始化 Helm 客户端和服务端
* [helm inspect ](helm-inspect.md)- 检出 chart
* [helm install ](helm-install.md)- 安装 chart
* [helm lint ](helm-lint.md)- 检查 chart 可能存在的问题 
* [helm list](helm-list.md) - 列出 releases
* [helm package](helm-package.md) - 归档 chart 目录
* [helm plugin](helm-plugin.md) - 增加，列出，移除 Helm 插件
* [helm repo ](helm-repo.md)- 增加，列出，移除，更新和索引 chart 仓库
* [helm reset ](helm-reset.md)- 从集群中卸载 Tiller
* [helm rollback](helm-rollback.md) - 回滚 release 到之前的版本
* [helm search](helm-search.md) - 根据关键字搜索 charts
* [helm serve](helm-serve.md) - 开启本地 http web server
* [helm status](helm-status.md) - 显示指定 release 的状态
* [helm template](helm-template.md) - 本地渲染模版
* [helm test ](helm-test.md)- 测试 release
* [helm upgrade](helm-upgrade.md) - 升级 release
* [helm verify](helm-verify.md) - 根据指定路径验证 chart 已被签名并且有效
* [helm version](helm-version.md) - 打印客户端/服务端版本信息







