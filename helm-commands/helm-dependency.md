---
description: 管理 chart 的依赖
---

# Helm Dependency

## 概要

管理一个 chart 的依赖

Helm  chart 把它们的依赖存储在 charts/  目录下， 对于 chart 开发人员来说，管理一个声明了所有依赖的文件（“ requirements.yaml”）通常更容易。

依赖命令在该文件上运行，使所需的依赖项和存储在“ charts /”目录中的实际依赖项之间的同步变得容易。

“ requirements.yaml” 文件是一种 YAML 文件，开发人员可以在其中声明 chart 依赖关系以及 chart 的位置和所需的版本。 例如，此 requirements 文件声明两个依赖项：

```text
# requirements.yaml
dependencies:
- name: nginx
  version: "1.2.3"
  repository: "https://example.com/charts"
- name: memcached
  version: "3.2.1"
  repository: "https://another.example.com/charts"
```

“name” 应该是 chart 的名称，该名称必须与该 chart 的 “Chart.yaml” 文件中的名称匹配。

“version” 字段应包含语义版本或版本范围。

仓库 URL 应指向 chart 所在的仓库。 Helm 希望通过在网址后附加“ /index.yaml”能被访问，它应该能够检索 chart 仓库的索引。 注意：“repository” 可以是别名。 别名必须以 “ alias：” 或 “@” 开头。

从 2.2.0 开始，仓库可以定义为本地存储的依赖 chart 目录的路径。 该路径应以“ file：//” 前缀开头。 例如：

```text
# requirements.yaml
dependencies:
- name: nginx
  version: "1.2.3"
  repository: "file://../dependency-chart/nginx"
```

如果从本地检索依赖 chart，则不需要通过 “helm repo add” 将存储库添加到 helm。 在这种情况下也支持版本匹配。

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

## 其他

* [helm dependency build](helm-dependency-build.md) - 根据 requirements.lock 文件重新编译 charts/ 目录 
* [helm dependency list](helm-dependency-list.md) - 列出指定 chart 的依赖
* [helm dependency update ](helm-dependency-update.md)- 根据  requirements.yaml 的内容升级 charts/

