---
description: Helm 有两部分：Helm 客户端（helm）和 Helm 服务端（Tiller）。这篇文章将指导你怎样安装客户端，然后继续展示两种安装服务端的方法。
---

# 安装 Helm

**IMPORTANT：**如果你负责确保尼的群集是受控环境，尤其是在共享资源时，强烈建议你使用安全配置安装 Tiller 。有关指导，请参阅 ~~安全的Helm安装~~。

## 安装 Helm 客户端

Helm 客户端既可以从源代码安装，也可以从预构建的二进制版本安装。

### 从二进制版本安装

Helm 的每个版本都为各种操作系统提供二进制版本。这些二进制版能被手动下载和安装。

1. 下载[预期的版本](https://github.com/helm/helm/releases)
2. 解压（`tar -zxvf helm-v2.0.0-linux-amd64.tgz`）
3. 在解压目录中找到`helm`二进制文件，把它移动到它预期的目录下（`mv linux-amd64/helm /usr/local/bin/helm`）

到了这里，你应该能够运行客户端：`helm help`。

### 从 Snap（Linux）安装

Helm 的 Snap 包由 [Snapcrafters ](https://github.com/snapcrafters/helm)维护。

```
sudo snap install helm --classic
```

### 从 Homebrew（macOS）安装

Kubernetes 社区的成员为 Homebrew 贡献了 Helm 构建方案。这个方案通常是最新的。

```text
brew install kubernetes-helm
```

{% hint style="info" %}
emacs-helm 还有一个方案，它是一个不同的项目
{% endhint %}

### 从 Chocolatey 或者 scoop（Windows）安装

Kubernetes 社区的成员为 [Chocolatey ](https://chocolatey.org/)贡献了 [Helm 包](https://chocolatey.org/packages/kubernetes-helm)。这个包通常是最新的。

```
choco install kubernetes-helm
```

二进制文件也可以通过 scoop 命令行安装程序安装。

```text
scoop install helm
```

### 从 Script 安装

现在 Helm 有一个安装脚本，它能自动的抓取最新的 Helm 客户端版本，然后在本地进行安装。

你可以获取该脚本，然后在~~本地执行它~~。它有详细的记录，在你运行它之前仔细阅读和理解它正在做什么。

```text
$ curl -LO https://git.io/get_helm.sh
$ chmod 700 get_helm.sh
$ ./get_helm.sh
```

是的，如果你想不一样，你可以这样安装`curl -L` [`https://git.io/get_helm.sh`](https://git.io/get_helm.sh) `| bash`。

### 从 Canary 构建版安装

“金丝雀” 构建版是 Helm 软件的版本，它通常是从最新的 master 分支上构建得来的。他们不是官方发行的版本，可能不稳定。但是，他们提供去测试新功能的机会。

Helm 的金丝雀二进制文件存储在 [get.helm.sh](https://get.helm.sh/)，以下是常见版本的链接：

* [Linux AMD64](https://get.helm.sh/helm-canary-linux-amd64.tar.gz)
* [macOS AMD64](https://get.helm.sh/helm-canary-darwin-amd64.tar.gz)
* [Experimental Windows AMD64](https://get.helm.sh/helm-canary-windows-amd64.zip)

### 从 Source（Linux，macOS）安装

从源码构建 Helm 需要多一些步骤，但是如果你想测试最新（预发布版本）的 Helm 版本，这是最好的方式。

你必须具有安装了 glide 的 Go 工作环境。

```text
$ cd $GOPATH
$ mkdir -p src/k8s.io
$ cd src/k8s.io
$ git clone https://github.com/helm/helm.git
$ cd helm
$ make bootstrap build
```

上述的 bootstrap 将尝试去安装依赖，重新构建`vendor/` 树，并校验配置。

而 build 将编译 helm 和把它放置到`bin/helm`下。Tiller 同样也被编译，它被放置到`bin/tiller`下。

## 安装 Tiller

Tiller 是 Helm 的服务部分，通常运行在你的 Kubernetes 集群内部。但是对于开发而言，它也可以在本地运行，并配置为远程 Kubernetes 通信。

### RBAC 用户特别注意事项

大多数云提供商开启了基于角色权限的访问控制的功能，简称 RBAC。如果你的云提供商开启了这个功能，你需要为 Tiller 创建一个拥有合适角色和权限去访问资源的 service account。

你可以查看 [Kubernetes 发行指南](kubernetes-fa-hang-zhi-nan.md)，了解是否有任何关于将 Helm 与云提供商一起使用的兴趣点。同时，你也可以查看 [Tiller and Role-Based Access Control](ji-yu-jiao-se-de-fang-wen-kong-zhi.md) 这篇引导来获得更多关于如何在一个打开了 RBAC 功能的 Kubernetes 集群上运行 Tiller。

### 快速安装

安装 tiller 到集群中最简单的方式是直接运行 helm init。这将验证是否正确设置了 helm 的本地环境（并在必要时进行设置）。然后它将连接到 kubectl 默认连接的任何集群（kubectl config view）。一旦连接成功，它将安装 tiller 到`kube-system`命名空间。

`helm init`后，你应该能运行`kubectl get pods --namespace kube-system`看到正在运行的 Tiller。

在`helm init`时，你可以指定一些参数：

* `--canary-image`：安装金丝雀（canary）构建版
* `--tiller-image`：自定义 Tiller 的镜像版本
* `--kube-context`：指定要安装的集群
* `--tiller-namespace`：指定要安装的命名空间
* `--service-account`：指定一个 Service Account
* `--automount-service-account false`：不需要 Service Account 绑定

一旦 Tiller 已安装，运行`helm version`就能向你展示 helm 和 tiller 的版本。（如果它仅展示客户端的版本，说明 helm 不能连接到服务端。使用`kubectl`检查 tiller 是否正在运行）。

除非设置了`--tiller-namespace`或 TILLER\_NAMESPACE，否则 Helm 将在`kube-system`命名空间中查找 Tiller。

### 安装金丝雀版 Tiller

金丝雀镜像是从主分支构建的。他们可能不稳定，但是他们向你提供了测试最新功能特性的机会。

安装金丝雀镜像的最简单方法是使用带有`--canary-image`标签的`helm init`：

```text
$ helm init --canary-image
```

这将使用最近构建的容器镜像。你始终可以通过使用`kubectl`从`kube-system`命名空间删除Tiller 部署来卸载 Tiller。

### 运行本地 Tiller

对于开发，有时让 Tiller 运行在本地更好，并将其配置为连接到远程 Kubernetes 集群。

上面已经解释了构建 Tiller 的过程。

只要`tiller`已构建好，那么就可以简单运行它：

```text
$ bin/tiller
Tiller running on :44134
```

当 Tiller 正在本地运行，它将尝试去连接由 `kubectl`配置的任何集群。（运行`kubectl config view`可了解当前所使用的集群）

你必须告诉`helm`连接到这个新的本地 Tiller 主机，而不是连接到一个集群内。有两种方法可以这样做。第一种是在命令行指定`--host`选项。第二种是去设置环境变量`$HELM_HOST`。

```text
$ export HELM_HOST=localhost:44134
$ helm version # Should connect to localhost.
Client: &version.Version{SemVer:"v2.0.0-alpha.4", GitCommit:"db...", GitTreeState:"dirty"}
Server: &version.Version{SemVer:"v2.0.0-alpha.4", GitCommit:"a5...", GitTreeState:"dirty"}
```

重要的是，即使在本地运行，Tiller 也会将发布配置存储在 Kubernetes 的 ConfigMaps 中。

## 升级 Tiller

从 Helm 2.2.0 开始，可以使用`helm init --upgrade`升级 Tiller。

对于老版本的 Helm 和需要手动升级而言，你可以使用`kubectl`去修改 Tiller 镜像：

```text
$ export TILLER_TAG=v2.0.0-beta.1        # Or whatever version you want
$ kubectl --namespace=kube-system set image deployments/tiller-deploy tiller=gcr.io/kubernetes-helm/tiller:$TILLER_TAG
deployment "tiller-deploy" image updated
```

设置 TILLER\_TAG = canary 将获得主分支上的最新版本。

## 删除或重新安装 Tiller

由于 Tiller 将其数据存储在 Kubernetes 的 ConfigMaps 中，因此尼可以安全地删除并重新安装 Tiller，而无需担心丢失任何数据。删除 Tiller 推荐的方式是使用`kubectl delete deployment tiller-deploy --namespace kube-system`，或者`helm reset`。

然后可以从客户端重新安装 Tiller：

```text
$ helm init
```

## 高级使用

`helm init`提供了额外的标签，用于在安装 Tiller 的部署清单之前对其进行修改。

### 使用`--node-selectors`

`--node-selectors`标志允许我们指定要安装 Tiller pod 所需节点的标签。

下面的示例将在nodeSelector属性下创建指定的标签。

```text
helm init --node-selectors "beta.kubernetes.io/os"="linux"
```

已安装的部署清单将包含我们的节点选择器标签。

```text
...
spec:
  template:
    spec:
      nodeSelector:
        beta.kubernetes.io/os: linux
...
```

### 使用`--override`

`--override` 允许你指定 Tiller 部署清单的属性。与 Helm 中其他地方使用的`--set`命令不同，`helm init --override`操纵最终清单的指定属性（没有“values”文件）。因此，你可以为部署清单中的任何有效属性指定任何有效值。

**Override annotation**

下面的示例我们使用`--override`来添加revision 属性并设置它的值为1。

```text
helm init --override metadata.annotations."deployment\.kubernetes\.io/revision"="1"
```

```text
apiVersion: apps/v1
kind: Deployment
metadata:
  annotations:
    deployment.kubernetes.io/revision: "1"
...
```

**Override affinity**

下面的示例我们设置 node 的 affinity 属性。多个`--override`命令可能会合并来修改同一列表项中的不同的属性。

```text
helm init --override "spec.template.spec.affinity.nodeAffinity.preferredDuringSchedulingIgnoredDuringExecution[0].weight"="1" --override "spec.template.spec.affinity.nodeAffinity.preferredDuringSchedulingIgnoredDuringExecution[0].preference.matchExpressions[0].key"="e2e-az-name"
```

指定的属性被合并到“preferredDuringSchedulingIgnoredDuringExecution”属性的第一个列表项。

```text
...
spec:
  strategy: {}
  template:
    ...
    spec:
      affinity:
        nodeAffinity:
          preferredDuringSchedulingIgnoredDuringExecution:
          - preference:
              matchExpressions:
              - key: e2e-az-name
                operator: ""
            weight: 1
...
```

### 使用`--output`

`--output`标志允许我们跳过 Tiller 部署清单的安装，只需将部署清单以 JSON 或 YAML 格式输出到 stdout。然后可以使用`jq`等工具修改输出，并使用`kubectl`手动安装。

下面的示例中，我们在执行`helm init`命令时添加`--output json`。

```text
helm init --output json
```

Tiller 的安装被跳过和清单以 JSON 的格式被输出到了stdout。

```text
"apiVersion": "apps/v1",
"kind": "Deployment",
"metadata": {
    "creationTimestamp": null,
    "labels": {
        "app": "helm",
        "name": "tiller"
    },
    "name": "tiller-deploy",
    "namespace": "kube-system"
},
...
```

### 存储后端

`tiller`通常存储版本信息在它运行的命名空间的`ConfigMaps`中。

#### Secret 存储后端

从 Helm 2.7.0 开始，有一个 beta 版的存储后端，它使用 Secrets 存储发布信息。在 Kubernetes 中与`Secret`加密的发布结合，在保护 charts 方面增加了额外的安全性。

要打开 secrets 后端，你需要使用下列选项初始化 Tiller：

```text
helm init --override 'spec.template.spec.containers[0].command'='{/tiller,--storage=secret}'
```

目前，如果要从默认后端切换到 secrets 后端，则必须自行进行迁移。当这个后端从测试版毕业时，将有更正式的迁移路径。

#### SQL 存储后端

从 Helm 2.14.0 开始，有一个 beta 版的 SQL 存储后端，它使用 SQL 数据库存储发布信息（目前只有 postgres 已经过测试）。

如果你的发布信息大小超过 1MB（在这种情况下，由于 Kubernetes 的底层 etcd 键值存储中的内部限制，它不能存储在 ConfigMaps/Secrets 中），使用这样的存储后端特别有用。

要打开 SQL 后端，你将需要部署一个 SQL 数据库和使用下列选项初始化 Tiller：

```text
helm init \
  --override \
    'spec.template.spec.containers[0].args'='{--storage=sql,--sql-dialect=postgres,--sql-connection-string=postgresql://tiller-postgres:5432/helm?user=helm&password=changeme}'
```

{% hint style="info" %}
生产环境注意：在生产环境上部署时，推荐更改 SQL 数据库的用户名和密码。打开 SSL 也是一个好主意。值得一提的是，执行 SQL 数据库的定期备份/快照。
{% endhint %}

目前，如果要从默认后端切换到 secrets 后端，则必须自行进行迁移。当这个后端从测试版毕业时，将有更正式的迁移路径。

## 总结

在大多数情况下，安装就像获取预先构建的 helm 二进制文件并运行 helm init 一样简单。本文档介绍了那些想要使用 Helm 做更复杂的事情的人的其他案例。

成功安装 Helm 客户端和 Tiller 后，你就可以继续使用 Helm 管理 charts。

