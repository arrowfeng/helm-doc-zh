---
description: 本指南介绍了使用 Helm（和Tiller）管理 Kubernetes 集群上的软件包的基础知识。
---

# 使用 Helm

假设你已经安装了 Helm 客户端和 Tiller 服务端（通常由`helm init`安装）。

如果你只想运行一些快速命令，则可以从[“快速入门指南”](kuai-su-kai-shi.md)开始。本章介绍 Helm 命令的细节，并说明如何使用Helm。

## 三大概念

_**Chart**_ 是 Helm 的软件包。它包含一个应用、工具或者服务在 Kubernetes 急群中运行所需要的所有资源定义。可以把它想象成类似于 Kubernetes 的 Homebrew formula，Apt dpkg 或 Yum RPM 文件。

 _**Repository**_ 是存放 charts的仓库，它可以收集、共享charts。它就像 Perl 的 CPAN 存档或 Fedora 包数据库，但是这是 Kubernetes 软件包。

 _**Release**_ 是一个 chart 运行在 Kubernetes 集群中的实例。一个 chart 能被多次安装到同一个集群中。并且每次安装，一个新的 _**release**_ 被创建。例如一个 MySQL chart。如果你想让两个数据库运行在你的集群中，你能安装两次这个 chart。每个实例都拥有自己的 release，而后者又有自己的 _**release name**_。

考虑到这些概念，我们现在可以像这样解释Helm：

Helm 安装 _**charts**_ 到Kubernetes中，并为每一次安装创建一个新的_**release**_。并且要查找这些新 _**charts**_，你可以搜寻 Helm 的 chart _**repository**_。  


## ‘HELM SEARCH’: 查找 CHARTS

当你第一次安装 Helm 时，它被预配置去访问官方的 Kubernetes charts 仓库。该仓库包含许多精心策划和维护的 charts。默认情况下这个仓库被命名为`stable`。

通过运行`helm search`你可以看到哪一个 charts 可用：

```text
$ helm search
NAME                 	VERSION 	DESCRIPTION
stable/drupal   	0.3.2   	One of the most versatile open source content m...
stable/jenkins  	0.1.0   	A Jenkins Helm chart for Kubernetes.
stable/mariadb  	0.5.1   	Chart for MariaDB
stable/mysql    	0.1.0   	Chart for MySQL
...
```

没有过滤条件，helm search将向你展示所有可用的charts。你可以增加过滤条件来缩小你的结果：

```text
$ helm search mysql
NAME               	VERSION	DESCRIPTION
stable/mysql  	0.1.0  	Chart for MySQL
stable/mariadb	0.5.1  	Chart for MariaDB
```

现在你只能看到匹配你条件的结果。

为什么 `mariadb`在列表中呢？因为它的包描述与 MySQL 相关。我们使用`helm inspect chart`去看看：

```text
$ helm inspect stable/mariadb
Fetched stable/mariadb to mariadb-0.5.1.tgz
description: Chart for MariaDB
engine: gotpl
home: https://mariadb.org
keywords:
- mariadb
- mysql
- database
- sql
...
```

搜索是查找可用包的好方法。找到要安装的软件包后，可以使用 helm install 进行安装。

## ‘HELM INSTALL’: 安装 CHARTS

要安装新软件包，请使用`helm install`命令。最简单的是，它只需要一个参数：chart 的 name。

```text
$ helm install stable/mariadb
Fetched stable/mariadb-0.3.0 to /Users/mattbutcher/Code/Go/src/k8s.io/helm/mariadb-0.3.0.tgz
NAME: happy-panda
LAST DEPLOYED: Wed Sep 28 12:32:28 2016
NAMESPACE: default
STATUS: DEPLOYED

Resources:
==> extensions/Deployment
NAME                     DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
happy-panda-mariadb   1         0         0            0           1s

==> v1/Secret
NAME                     TYPE      DATA      AGE
happy-panda-mariadb   Opaque    2         1s

==> v1/Service
NAME                     CLUSTER-IP   EXTERNAL-IP   PORT(S)    AGE
happy-panda-mariadb   10.0.0.70    <none>        3306/TCP   1s


Notes:
MariaDB can be accessed via port 3306 on the following DNS name from within your cluster:
happy-panda-mariadb.default.svc.cluster.local

To connect to your database run the following command:

   kubectl run happy-panda-mariadb-client --rm --tty -i --image bitnami/mariadb --command -- mysql -h happy-panda-mariadb
```

现在安装了`mariadb`chart。请注意，安装一个 chart 就会创建新的 release 对象。上面的 release 被命名为`happy-panda`。（如果要使用自己的 release 名称，只需在`helm install`上使用`--name`标志。）

在安装过程中，`helm`客户端将打印有关创建的资源的有用信息，release 状态以及是否可以或应该采取其他配置步骤。

Helm不会等到所有资源都处于运行状态之前才会退出。许多 charts 需要大小超过 600M 的 Docker 镜像，并且可能需要很长时间才能安装到群集中。

要跟踪 release 的状态，或者反复读取配置信息，你可以使用`helm status`：

```text
$ helm status happy-panda
Last Deployed: Wed Sep 28 12:32:28 2016
Namespace: default
Status: DEPLOYED

Resources:
==> v1/Service
NAME                     CLUSTER-IP   EXTERNAL-IP   PORT(S)    AGE
happy-panda-mariadb   10.0.0.70    <none>        3306/TCP   4m

==> extensions/Deployment
NAME                     DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
happy-panda-mariadb   1         1         1            1           4m

==> v1/Secret
NAME                     TYPE      DATA      AGE
happy-panda-mariadb   Opaque    2         4m


Notes:
MariaDB can be accessed via port 3306 on the following DNS name from within your cluster:
happy-panda-mariadb.default.svc.cluster.local

To connect to your database run the following command:

   kubectl run happy-panda-mariadb-client --rm --tty -i --image bitnami/mariadb --command -- mysql -h happy-panda-mariadb
```

上述展示了当前你的 release 当前的状态。

## 安装之前自定义配置 Chart

上述安装方法我们只使用了此 Chart 的默认配置选项。

很多时候，你可能想使用你自己偏好的配置去自定义 chart。

要想了解一个 chart 中什么选项是可配置的，使用`helm inspect values`：

```text
helm inspect values stable/mariadb
Fetched stable/mariadb-0.3.0.tgz to /Users/mattbutcher/Code/Go/src/k8s.io/helm/mariadb-0.3.0.tgz
## Bitnami MariaDB image version
## ref: https://hub.docker.com/r/bitnami/mariadb/tags/
##
## Default: none
imageTag: 10.1.14-r3

## Specify a imagePullPolicy
## Default to 'Always' if imageTag is 'latest', else set to 'IfNotPresent'
## ref: https://kubernetes.io/docs/user-guide/images/#pre-pulling-images
##
# imagePullPolicy:

## Specify password for root user
## ref: https://github.com/bitnami/bitnami-docker-mariadb/blob/master/README.md#setting-the-root-password-on-first-run
##
# mariadbRootPassword:

## Create a database user
## ref: https://github.com/bitnami/bitnami-docker-mariadb/blob/master/README.md#creating-a-database-user-on-first-run
##
# mariadbUser:
# mariadbPassword:

## Create a database
## ref: https://github.com/bitnami/bitnami-docker-mariadb/blob/master/README.md#creating-a-database-on-first-run
##
# mariadbDatabase:
```

然后你可以以 YAML 格式的文件覆盖这些配置，通过在安装期间指定这个文件。

```text
$ cat << EOF > config.yaml
mariadbUser: user0
mariadbDatabase: user0db
EOF
$ helm install -f config.yaml stable/mariadb
```

上述配置将会创建一个 name 为`user0`的 默认 MariaDB 用户，并赋予这个用户访问`user0db`的权限。而余下的配置将使用 chart 中默认的配置。

在安装期间有两种方式可以设置配置：

* `--values`\(或 `-f`\)：指定一个 YAML 文件去覆盖，这可以多次指定，最右边的文件优先。
* `--set`\(`--set-string`或`--set-file`\)：指定一个命令去覆盖。

如果同时使用，`--set`的值将以更高的优先级合并到`--values`上。使用`--set`指定的覆盖值将持久化在 configmap 中。对于给定的 release，通过`--set`设置的值可以使用`helm get values <release-name>`查看。通过`--set`设置的值运行`helm upgrade --reset-values`清除。

### `--set`的格式和限制

`--set`选项需要零个或多个名称/值对。最简单的是，它使用如下： `--set name=value`。等价于 YAML  文件：

```text
name: value
```

多个值使用`,`分隔。如：`--set a=b, c=d`：

```text
a: b
c: d
```

同样也支持更负责的表达式。例如`--set outer.inner=value`可以被转换成这样：

```text
outer:
  inner: value
```

 列表可使用花括号来表示。例如`--set name={a,b,c}`：

```text
name:
- a
- b
- c
```

自 Helm 2.5.0 起，访问列表项可以使用数组索引的语法。例如`--set servers[0].port=80`:

```text
servers:
- port: 80
```

多个值可以以这样的方式设置`--set servers[0].port=80,servers[0].host=example`:

```text
servers:
- port: 80
  host: example
```

如果你需要在`--set`使用特别的字符。你可以使用反斜杠进行转义；`--set name = "value1\,value2"`:

```text
name: "value1,value2"
```

同样，你也可以转义点序列，当在 `charts` 中使用 `toYaml` 函数解析 `annotations`，`labels` 和 `node selectors` 时可能会派上用场。例如：--set nodeSelector."kubernetes\.io./role"=master：

```text
nodeSelector:
  kubernetes.io/role: master
```

使用`--set`很难去表达深层嵌套的数据结构。在进行`values.yaml`文件的格式设计时，开发者通常应该考虑`--set`的用法。

`Helm`将转换使用`--set`设置的某些为整数的值。例如，`--set foo=true`导致`Helm`转换`true`为`int64`类型的数值。万一你想要字符串类型，可以使用`--set`的一个变体`--set-string`。`--set-string foo=true`的结果为字符串`"true"`

`--set-file key=filepath`是`--set`的另一个变体。它读取文件并使用其内容作为值。它的一个示例用例是将多行文本插入值中，而无需处理`YAML`中的缩紧。假如你想使用 5 行 JS 代码创建一个`brigade`工程，你可能需要编写这样的`values.yaml`文件：

```text
defaultScript: |
  const {events, Job} = require("brigadier")
  function run(e, project) {
    console.log("hello default script")
  }
  events.on("run", run)
```

由于嵌入在`YAML`中，这使你更难使用IDE功能和测试框架编写代码等。相反，你能使用把-`-set-file defaultScript=brigade.js`和`brigade.js`文件结合使用：

```text
const {events, Job} = require("brigadier")
function run(e, project) {
  console.log("hello default script")
}
events.on("run", run)
```

### 安装方法

`helm install`命令能从下列源处安装：

* chart 仓库（上述提到的）
* 本地 chart 压缩包
* 解压的 chart 目录
* 完整饿 URL

##  ‘HELM UPGRADE AND ROLLBACK‘：升级一个 Release 或在失败时恢复

当一个新版的 chart 发布时，或者是你想去改变你现在版本的配置，你可以使用`helm upgrade`命令。

它将根据你所提供的信息对已存在的版本进行升级。因为 Kubernetes charts 可能很大和复杂，Helm 尽力进行最小范围的升级。它将只升级上次版本改变的信息。

```text
$ helm upgrade -f panda.yaml happy-panda stable/mariadb
Fetched stable/mariadb-0.3.0.tgz to /Users/mattbutcher/Code/Go/src/k8s.io/helm/mariadb-0.3.0.tgz
happy-panda has been upgraded.
Last Deployed: Wed Sep 28 12:47:54 2016
Namespace: default
Status: DEPLOYED
```

上面的例子，`happy-panda`版本使用同样的 `chart` 进行升级，不同的是多了一个 YAML 文件：

```text
mariadbUser: user1
```

我们能使用helm get values 查看这个新设置是否生效。

```text
$ helm get values happy-panda
mariadbUser: user1
```

`helm get`命令在集群中被用来查看版本信息。正如我们上述所看到的，它向我们展示了来自文件`panda.yaml`版本的新配置值。

如果现在运行的版本遇到什么问题，使用`helm rollback [RELEASE] [REVISION]`可以很容易的回退到之前的版本。

```text
$ helm rollback happy-panda 1
```

上面将会回滚到我们`happy-panda`应用的最开始的那个版本。新版本的版本号将会递增。每次安装，升级，回滚，版本号都会加1。最开始的版本总是1。我们能使用`helm history [RELEASE]`查看特定的版本号。

## 安装/升级/回滚有帮助的选项

你可以在安装/升级/回滚的时候指定一些选项设置`Helm`的行为。请注意这不是所有命令行选项。要查看所有选项的描述，只需运行`helm <command> --help`。

* `--timeout`：等待 Kubernetes 命令完成的秒数（默认）为300（5分钟）
* `--wait`：直到所有 Pod 处于 ready 状态，PVCs 被绑定，Deployments 达到最小 Pod 数（期望数减去`maxUnavailable`）处于 ready 状态和 Services 有 IP 地址（Ingress有负载均衡器），才把这个`release`标记为成功。它将等待`--timeout`设置的时长。如果到达超时时间，`release`将会被标记为`FAILED`。注意：在 Deployment 副本设置为 1 且`maxUnavailable`未设置为 0，在进行滚动更新时，只要最小 Pod 数量处于就绪状态 `--wait`将返回满足 ready 状态的标记。
* `--no-hooks`：跳过命令的运行时钩子
* `--recreate-pods`（只对`upgrade`和`rollback`有效）：这个选项将使所有的 pods 被重建（除了属于 deployments 的 Pod）

## ‘HELM DELETE’：删除 RELEASE

当你需要从集群中卸载或者删除一个`release`时，使用`helm delete`命令：

```text
$ helm delete happy-panda
```

将从集群中移除这个`release`。使用helm list命令可以查看当前你部署的所有 `release`。

```text
$ helm list
NAME           	VERSION	UPDATED                        	STATUS         	CHART
inky-cat       	1      	Wed Sep 28 12:59:46 2016       	DEPLOYED       	alpine-0.1.0
```

从上面的输出可以看出，`happy-panda` `release`已经被删除。

但是，`helm` 总是会保存着`releases`所发生的记录。`helm list --deleted`将会展示已经删除的`releases`。helm list --all 将展示所有releases（已经删除的、当前部署的和失败的）：

```text
⇒  helm list --all
NAME           	VERSION	UPDATED                        	STATUS         	CHART
happy-panda   	2      	Wed Sep 28 12:47:54 2016       	DELETED        	mariadb-0.3.0
inky-cat       	1      	Wed Sep 28 12:59:46 2016       	DEPLOYED       	alpine-0.1.0
kindred-angelf 	2      	Tue Sep 27 16:16:10 2016       	DELETED        	alpine-0.1.0
```

因为 Helm 保存了已删除的`releases`记录，所以`release`的 name 不能重复使用。（如果你需要重复使用`release`的名字，你能使用`--replace`选项，它只是重复的使用已存在的`release`并替换它的资源。）

注意，因为`releases`能以这种方式保留，所以你能回滚到已删除的资源并让它重新激活。

## ‘HELM REPO‘：操作仓库

到目前为止，我们已经从`stable`仓库安装了`charts`。但是你还能配置`helm`去使用其他仓库。`helm`在`helm repo`命令下提供了一系列仓库工具集。

使用`helm repo list`可以列出已配置的仓库：

```text
$ helm repo list
NAME           	URL
stable         	https://kubernetes-charts.storage.googleapis.com
local          	http://localhost:8879/charts
mumoshu        	https://mumoshu.github.io/charts
```

使用`helm repo add`可以添加一个新的仓库：

```text
$ helm repo add dev https://example.com/dev-charts
```

因为chart仓库变化频繁，通过使用`helm repo update`在任何时间点确保你的`helm client`是最新的。

## 创建你自己的 CHARTS

[Chart Development Guide ](../helm-commands/helm-list.md)展示了怎样开发你自己的`charts`。使用`helm create`命令可以快速创建它。

```text
$ helm create deis-workflow
Creating deis-workflow
```

现在有一个`chart`在`./deis-workflow`目录下。你能编辑它并创建自己的模版。

编辑好你的`chart`之后，你可以运行`helm lint`校验它的格式是否正确。

当是时候去打包`chart`进行发行，你可以运行`helm package`命令：

```text
$ helm package deis-workflow
deis-workflow-0.1.0.tgz
```

并且这个`chart`通过`helm install`能被很容易的安装：

```text
$ helm install ./deis-workflow-0.1.0.tgz
...
```

被归档的`charts`能被上传到`chart`仓库。请参阅`chart`存储服务器的文档以了解如何上传。

注意：`stable`仓库由 [Helm Charts GitHub](https://github.com/helm/charts) 仓库管理。该项目接受`chart`源代码，并（在审核之后）为你打包这些源代码。

