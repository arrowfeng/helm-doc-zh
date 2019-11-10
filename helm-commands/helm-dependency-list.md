---
description: 列出指定 chart 的依赖
---

# Helm Dependency List

## 概要

列出 chart 中声明的所有依赖项。

这可以将 chart 归档和 chart 目录作为输入。 它不会改变 chart 的内容。

如果无法加载 chart，将产生错误。 如果找不到 requirements.yaml，它将发出警告。

```text
$ helm dependency list [flags] CHART
```

## 选项

```text
  -h, --help   help for list
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

