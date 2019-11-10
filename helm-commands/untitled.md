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









