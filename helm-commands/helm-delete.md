---
description: 从 Kubernetes 中删除指定名字的 chart
---

# Helm Delete

## 概要

此命令将从 Kubernetes 中删除指定的 release 。它删除了与 chart 的最新版本相关联的所有资源。

使用 “ –dry-run” 标志可查看要删除的 release，而无需实际删除它们。

```text
$ helm delete [flags] RELEASE-NAME [...]
```

## 选项

```text
      --description string    指定一个 release 的描述
      --dry-run               模仿删除
  -h, --help                  delete 帮助
      --no-hooks              删除期间防止 hooks 运行
      --purge                 从存储中移除 release 以便后续可以继续使用它的名字创建 release
      --timeout int           等待任何单个 Kubernetes 操作（如 Jobs for hooks）的时间（以秒为单位）（默认为300）
      --tls                   启用 TLS
      --tls-ca-cert string    TLS CA 证书文件路径（默认 "$HELM-HOME/ca.pem"）
      --tls-cert string       TLS 证书文件路径（默认 "$HELM-HOME/cert.pem"）
      --tls-hostname string   用于验证从服务器返回的证书上的主机名的服务器名称
      --tls-key string        TLS 密钥文件路径 (默认 "$HELM-HOME/key.pem")
      --tls-verify            启用TLS进行请求并验证远程端
```

## 继承父命令的选项

```text
      --debug                           启用详细输出
      --home string                     指定你的 Helm config，覆盖 $HELM-HOME（默认为～/.helm）
      --host string                     指定 Tiller 的地址，覆盖 $HELM-HOST
      --kube-context string             指定要操作的 Kubernetes context
      --kubeconfig string               指定要使用的 kubeconfig 文件的绝对路径
      --tiller-connection-timeout int   等待与 Tiller 建立连接的超时时间（默认为300s）
      --tiller-namespace string         Tiller 的命名空间（默认为 kube-system）
```

