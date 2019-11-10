---
description: 创建指定名字的 chart
---

# Helm Create

## 概要

此命令将创建一个 chart 目录以及在 chart 中使用的常见文件和目录。 它提供了一个基本示例，并不意味着涵盖所有 Kubernetes 资源。

例如，“ helm create foo” 将创建一个类似于以下内容的目录结构：

```text
foo/
  |
  |- .helmignore        # 打包 Helm charts 时将忽略匹配到的文件
  |
  |- Chart.yaml         # 你的 chart 信息
  |
  |- values.yaml        # 你的模版默认值
  |
  |- charts/            # chart 的依赖
  |
  |- templates/         # 模版文件
  |
  |- templates/tests/   # 测试文件
```

“ helm create” 把目录作为参数。 如果给定路径中的目录不存在，Helm 将尝试随其创建它们。 如果给定的目标存在并且该目录中有文件，则冲突的文件将被覆盖，而其他文件将被保留。

通过调用此命令创建的 chart 包含一个 Deployment，Ingress 和一个 Service。 要将其他 Kubernetes 资源与你的 chart 一起使用，请参阅[《chart 模板开发人员指南》](../developing-templates/)。

