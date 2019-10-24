---
description: 这篇文章将指导你怎样快速开始使用 Helm
---

# 快速开始

## 必要前提

成功且合适安全的使用 Helm 必须要先满足以下条件：

1. 一个 Kubernetes 集群
2. 如果需要，决定使用什么样的安全配置应用到你的 Helm 安装中
3. 安装和配置好集群端服务的 Helm 和 Tiller

### 安装 Kubernetes 或者 访问一个已存在的集群

* 你必须拥有一个已安装好的 Kubernetes。对于最新的 Helm 版本，我们推荐使用最新稳定的 Kubernetes 版本，它通常是大版本的第二个小版本。
* 你应该有一份本地配置的 kubectl 副本。

{% hint style="info" %}
1.6之前的Kubernetes版本可能对基于角色权限的访问控制（RBAC）有限制或者是不支持。
{% endhint %}

为了解决将 Tiller 安装到哪一个集群，你可以运行命令 `kubectl config current-context` 或者 `kubectl cluster-info`

```
$ kubectl config current-context
my-cluster
```

### 理解你的安全上下文

与所有强大的工具一样，请确保为你的方案正确安装它。

如果在你完全掌控的集群上使用 Helm，像 minikube 或者 专用网络上的集群，其中共享不是问题，默认安装 — 不需要安全配置 — 就很好，这绝对是最简单的。要在没有其他安全步骤的情况下安装 Helm，请~~安装 Helm~~ 然后 ~~初始化Helm~~。

但是，如果你的集群被暴露在一个很大的网络或者如果你与其他人共享你的集群 — 生产集群属于这一类 — 你必须采取额外的步骤来保护你的安装，以防粗心或恶意的人员损坏集群和数据。 要在生产环境和其他多租户方案中应用 Helm 的安全配置，请参阅 ~~安全的 Helm 安装~~。

如果你集群的基于角色权限的访问控制已经打开，在开始安装之前，你可能需要去配置 ~~service account 和 rules~~ 。

## 安装 Helm

下载 Helm  客户端的二进制版本。你可以使用类似 `homebrew` 这样的工具，或者参阅[官方版本页面](https://github.com/helm/helm/releases)。

对于更多细节，或者其他操作，请参阅~~安装引导~~。

## 初始化 Helm 和 安装 Tiller

一旦你已经安装好 Helm，你就能初始化本地 CLI，同时也能一步安装 Tiller 到你的 Kubernetes 集群中：

`$ helm init --history-max 200`

**TIP：**建议在初始化 Helm 时设置 `--history-max` 作为 configmaps 和 如果没有达到最大限制，其他在 Helm 历史记录中的对象将不会被清除。如果没有设置最大历史记录，则无限期地保留历史记录， 留下大量记录以供 Helm 和 Tiller 维护。

这将安装 Tiller 到你使用`kubectl config current-context`看到的Kubernetes 集群中。

**TIP：**想安装到不同的集群中？使用`--kube-context`标签。

**TIP：**当你想升级 Tiller，只需要运行 `helm init --upgrade`。

默认情况下，安装Tiller时，它未启用身份验证。要了解有关为 Tiller 配置强 TLS 身份验证的更多信息，请参阅 ~~Tiller TLS 指南~~。

## 安装一个 Chart 例子

安装一个 chart，首先你要运行`helm install`命令。Helm 有多种方式去查找和安装一个 chart，但是最容易的方法是去使用官方稳定的（stable） charts 之一。

```text
$ helm repo update              # Make sure we get the latest list of charts
$ helm install stable/mysql
NAME:   wintering-rodent
LAST DEPLOYED: Thu Oct 18 14:21:18 2018
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Secret
NAME                    AGE
wintering-rodent-mysql  0s

==> v1/ConfigMap
wintering-rodent-mysql-test  0s

==> v1/PersistentVolumeClaim
wintering-rodent-mysql  0s

==> v1/Service
wintering-rodent-mysql  0s

==> v1beta1/Deployment
wintering-rodent-mysql  0s

==> v1/Pod(related)

NAME                                    READY  STATUS   RESTARTS  AGE
wintering-rodent-mysql-6986fd6fb-988x7  0/1    Pending  0         0s


NOTES:
MySQL can be accessed via port 3306 on the following DNS name from within your cluster:
wintering-rodent-mysql.default.svc.cluster.local

To get your root password run:

    MYSQL_ROOT_PASSWORD=$(kubectl get secret --namespace default wintering-rodent-mysql -o jsonpath="{.data.mysql-root-password}" | base64 --decode; echo)

To connect to your database:

1. Run an Ubuntu pod that you can use as a client:

    kubectl run -i --tty ubuntu --image=ubuntu:16.04 --restart=Never -- bash -il

2. Install the mysql client:

    $ apt-get update && apt-get install mysql-client -y

3. Connect using the mysql cli, then provide your password:
    $ mysql -h wintering-rodent-mysql -p

To connect to your database directly from outside the K8s cluster:
    MYSQL_HOST=127.0.0.1
    MYSQL_PORT=3306

    # Execute the following command to route the connection:
    kubectl port-forward svc/wintering-rodent-mysql 3306

    mysql -h ${MYSQL_HOST} -P${MYSQL_PORT} -u root -p${MYSQL_ROOT_PASSWORD}

```

在上面的例子中，`stable/mysql`chart 被发布，且发布的名字为`wintering-rodent`。通过运行`helm inspect stable / mysql`，你可以简单了解此MySQL图表的功能。

无论何时安装一个 chart，一个新版本会被创建。所以一个 chart 在同一个集群中能被安装多次。并且能独立地对它们进行管理和升级。

`helm install` 命令是一个非常强大的命令，拥有很多功能。想要了解更多，请参阅 ~~Helm的使用引导~~。

## 学习关于版本（releases）的概念

使用 Helm 可以很容易看到它发布的内容：

```text
$ helm ls
NAME            	REVISION	UPDATED                 	STATUS  	CHART       	APP VERSION	NAMESPACE
wintering-rodent	1       	Thu Oct 18 15:06:58 2018	DEPLOYED	mysql-0.10.1	5.7.14     	default
```

`helm list` 命令将向你列出所有被部署的版本。

## 卸载一个版本

要卸载一个版本，使用命令`helm delete`：

```text
$ helm delete wintering-rodent
release "wintering-rodent" deleted
```

这将从 Kubernetes 中卸载 wintering-rodent，但是你仍然可以请求关于该版本的信息：

```text
$ helm status wintering-rodent
LAST DEPLOYED: Thu Oct 18 14:21:18 2018
NAMESPACE: default
STATUS: DELETED

NOTES:
MySQL can be accessed via port 3306 on the following DNS name from within your cluster:
wintering-rodent-mysql.default.svc.cluster.local

To get your root password run:

    MYSQL_ROOT_PASSWORD=$(kubectl get secret --namespace default wintering-rodent-mysql -o jsonpath="{.data.mysql-root-password}" | base64 --decode; echo)

To connect to your database:

1. Run an Ubuntu pod that you can use as a client:

    kubectl run -i --tty ubuntu --image=ubuntu:16.04 --restart=Never -- bash -il

2. Install the mysql client:

    $ apt-get update && apt-get install mysql-client -y

3. Connect using the mysql cli, then provide your password:
    $ mysql -h wintering-rodent-mysql -p

To connect to your database directly from outside the K8s cluster:
    MYSQL_HOST=127.0.0.1
    MYSQL_PORT=3306

    # Execute the following command to route the connection:
    kubectl port-forward svc/wintering-rodent-mysql 3306

    mysql -h ${MYSQL_HOST} -P${MYSQL_PORT} -u root -p${MYSQL_ROOT_PASSWORD}
```

即使你已经删除了它们，Helm 仍然会追踪你的版本信息，你可以审计你的集群历史，和恢复已删除的版本（使用`helm rollback`）。

## 阅读帮助文本

要学习更多关于 Helm 的命令，使用`helm help`或者 在下列命令后添加`-h`标签：

```text
$ helm get -h
```



