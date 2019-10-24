---
description: 本文章介绍有关在特定 Kubernetes 环境中使用 Helm 的情况。
---

# Kubernetes 发行指南

## MINIKUBE

Helm 经过测试并且已知可与 [minikube ](https://github.com/kubernetes/minikube)配合使用。它不需要额外的配置。

## SCRIPTS/LOCAL-CLUSTER AND HYPERKUBE

通过脚本`scripts/local-cluster.sh`配置的 Hyperkube 已知可行。对于原始Hyperkube，你可能需要进行一些手动配置。

### GKE <a id="gke"></a>

谷歌 GKE 托管的 Kubernetes 平台默认启用 RBAC。尼需要为 tiller 创建一个服service account，并在初始化 helm server 时使用 --service-account 标志。

详情请参阅[Tiller 和 基于角色的访问控制](ji-yu-jiao-se-de-fang-wen-kong-zhi.md)获取更多信息。

###  AKS <a id="aks"></a>

Helm 适用于 [Azure Kubernetes 服务](https://docs.microsoft.com/en-us/azure/aks/kubernetes-helm)。如果使用支持 RBAC 的 AKS 群集，则[需要 service account 和角色绑定到 Tiller 服务](https://docs.microsoft.com/en-us/azure/aks/kubernetes-helm#create-a-service-account)。

## 使用 ‘KUBEADM’ 的 UBUNTU 

使用`kubeadm`引导安装的 Kubernetes 已知可以很好地工作在下列的 Linux 发行版：

* Ubuntu 16.04
* Fedora release 25

Helm（v2.0.0-beta2）的一些版本要求你 `export KUBECONFIG=/etc/kubernetes/admin.conf`或者创建  `~/.kube/config`。

## COREOS 的 CONTAINER LINUX

Helm 要求 kubelet 可以访问`socat`程序的副本以代理连接到 Tiller API。在 Container Linux上，Kubelet 在具有`socat`的 [hyperkube](https://github.com/kubernetes/kubernetes/tree/master/cluster/images/hyperkube) 容器镜像内运行。因此，即使 Container Linux 没有承载`socat`，运行 kubelet 的容器​​文件系统也有`socat`。想要了解更多请参阅 [Kubelet Wrapper](https://coreos.com/tectonic/docs/latest/tutorials/sandbox/index.html) 文档。

## OPENSHIFT

Helm 在 OpenShift Online，OpenShift Dedicated，OpenShift Container Platform（版本&gt; = 3.6）或OpenShift Origin（版本&gt; = 3.6）上直接工作。要了解更多信息，请阅读此[博文](https://blog.openshift.com/getting-started-helm-openshift/)。

## PLATFORM9

[Platform9 Managed Kubernetes](https://platform9.com/managed-kubernetes/?utm_source=helm_distro_notes) 预装了 Helm Client 和 Helm Server（Tiller）。Platform9 通过 App Catalog UI 和本地 Kubernetes CLI 提供对所有官方 Helm charts 的访问。可以手动添加其他存储库。[此Platform9 应用程序目录文章](https://docs.platform9.com/kubernetes/deploying-kubernetes-apps-platform9-managed-kubernetes/)中提供了更多详细信息。

## DC/OS

Helm（客户端和服务器）已经过测试，可正常运行在 Mesospheres DC / OS 1.11 Kubernetes 平台，无需额外配置。

