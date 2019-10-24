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





















