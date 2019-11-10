---
description: 根据requirements.lock 文件重新构建 charts/ 目录
---

# Helm Dependency Build

## 概要

从 requirements.lock 文件构建输出懂啊 charts/ 目录。

构建用于将 chart 的依赖关系重建为 requirements.lock 文件中指定的状态。

如果未找到 requirements.lock 文件，则 “helm dependency build” 的作用与 “helm dependency update“ 命令一样。 这意味着它将更新磁盘上的依赖关系以镜像requirements.yaml 文件并生成一个 requirements.lock 文件。

## 选项

```text
 -h,  --help             help for build
      --keyring string   包含公钥的密钥环（默认为“〜/.gnupg/pubring.gpg”）
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

