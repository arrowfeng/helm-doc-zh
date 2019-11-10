---
description: 获取指定 release 的 Notes
---

# Helm Get Notes

## 概要

此命令显示指定 release 的 chart 提供的注释。

```text
$ helm get notes [flags] RELEASE-NAME
```

## 选项

```text
-h,   --help                  help for get
      --revision int32        获取指定 release 的版本号
      --tls                   开启 TLS 请求
      --tls-ca-cert string    TLS CA 证书文件路径(默认 "$HELM-HOME/ca.pem")
      --tls-cert string       TLS 证书文件路径(默认 "$HELM-HOME/cert.pem")
      --tls-hostname string   用于验证从服务器返回的证书上的主机名的服务器名称
      --tls-key string        TLS 密钥文件路径 (默认 "$HELM-HOME/key.pem")
      --tls-verify            启用 TLS 进行请求并验证远程端
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



