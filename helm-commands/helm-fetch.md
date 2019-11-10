---
description: 从仓库中下载一个 chart 并解压到本地目录
---

# Helm Fetch

## 概要

从软件包仓库中检索软件包，然后在本地下载。

这对于获取软件包以进行检查，修改或重新打包非常有用。它也可以用于执行 chart 的密码验证，而无需安装 chart。

下载后，有一些用于解压缩 chart 的选项。这将为 chart 创建一个目录，并将其解压缩到该目录中。

如果指定了–verify 标志，则所请求的 chart 必须具有一个源文件，并且必须通过验证过程。任何这方面的失败都会导致错误，并且该 chart 将不会保存在本地。

```text
$ helm fetch [flags] [chart URL | repo/chartname] [...]
```

## 选项

```text
      --ca-file string       使用 CA 捆绑包验证开启 HTTPS 的服务器证书
      --cert-file string     使用此 SSL 证书文件识别 HTTPS 客户端
  -d, --destination string   写入 chart 的位置。 如果指定了目录和 tardir，则将 tardir 附加到此目录下（默认为“.”）。
      --devel                使用开发版本。 等效于版本 '> 0.0.0-0'。 如果设置了 --version，则将其忽略。
  -h, --help                 help for fetch
      --key-file string      使用 SSL 密钥文件识别 HTTPS 客户端
      --keyring string       包含公钥的密钥环（默认为“〜/.gnupg/pubring.gpg”）
      --password string      Chart 仓库的密码
      --prov                 获取源文件，但不执行验证
      --repo string          请求 chart 的仓库 url
      --untar                如果设置为 true，它将在下载完成后解压 chart
      --untardir string      如果 untar 被指定，此标志指定 chart 扩展进入到的目录的名称（默认为“.”）。
      --username string      Chart 仓库的用户名
      --verify               根据它的签名验证软件包
      --version string       指定 chart 版本。如果没有它，默认为最新版
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

