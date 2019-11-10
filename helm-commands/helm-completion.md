---
description: 针对 shell（bash 或 zsh）的自动补全脚本
---

# Helm Completion

## 概要

此命令可以自动生成 shell 补全

```text
$ helm completion bash
```

能被 source 使用

```text
$ source < (helm completion bash)
```

```text
$ helm completion SHELL [flags]
```

## 选项

```text
  -h, --help   help for completion
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



