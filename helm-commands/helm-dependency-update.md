---
description: 根据 requirements.yaml 的内容更新 charts/
---

# Helm Dependency Update

## 概述

使用  requirements.yaml 文件，更新磁盘上的依赖。

此命令验证所需的charts（如“ requirements.yaml”中表示的）是否存在于“charts/”中，并且是否处于可接受的版本。 它将拉取满足依赖的最新 chart，并清理旧的依赖。

成功更新后，这将生成一个 lock 文件，该文件可用于将需求重新构建为确切的版本。

不需要在“ requirements.yaml”中表示依赖。因此，除非\(a\) requirements.yaml 文件中存在 charts，且\(b\)版本错误，否则更新命令不会删除这些 charts。

```text
helm dependency update [flags] CHART
```

## 选项

```text
 -h, --help              help for update
      --keyring string   包含公钥的密钥环（默认为“〜/.gnupg/pubring.gpg”）
      --skip-refresh     不刷新本地仓库缓存
      --verify           根据签名验证软件包
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

