overview
----
kuberbetes系统使用client-go作为go语言的官方编程式交互客户端库，提供对kubernetes API server服务的交互访问。kubernetes源码中已经集成了client-go源码，
路径为：`vendor/k8s.io/client-go`，另外，可以通过`go get k8s.io/client-go@kubernetes-1.14.10` 来安装client-go。开发者常使用client-go基于kubernetes做二次开发，
因此，client-go是开发者应该熟练掌握的必备技能。

1）client-go源码结构
----
|源码目录|说明|
|--------|----|
|distory |提供DiscoveryClient发现客户端，用于发现API server服务支持的group、version、resource|
|dynamic |提供DynamicClient动态客户端，可对k8s内置资源和自定义资源（CRD）操作|
|informers|每种k8s资源的informer实现|
|kubernetes|提供ClientSet客户端，只能处理内置资源，在RESTClient基础上封装了Resource和Group|
|listers |为每一个k8s资源提供lister功能，该功能对Get和List请求提供只读的缓存数据|
|plugin  |提供OpenStack、GCP、和Azure等云服务商授权插件|
|rest    |提供RESTClient客户端，对k8s API server执行RESTful操作|
|scale   |提供ScaleClient客户端，用于扩容或缩容Deployment、ReplicaSet、Replication Controller等资源对象|
|tools   |提供常用工具，例如：SharedInformer、Reflector、DealtFIFO及Indexers。提供client查询和缓存机制，以减少向kube-apiserver发起的请求数等|
|transport|提供安全的TCP连接，支持Http Stream，某些操作需要在客户端和容器之间传输二进制流，例如：exec、attach等操作；该功能由内部的spdy包提供支持。|
|util    |提供常用方法，例如：WorkQueue工作队列、Certificate证书管理等|

2）Client客户端
----
![client交互对象](https://github.com/wmy-one/k8sStudy.gihub.io/blob/master/client-go%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%90/%E7%AC%AC%E4%B8%80%E8%8A%82%EF%BC%9Aclient-go%E5%AE%A2%E6%88%B7%E7%AB%AF/client%E4%BA%A4%E4%BA%92%E5%AF%B9%E8%B1%A1.png)

client-go提供了4种客户端，简单描述如下：
|客户端名称|源码目录|简单描述|
|---------|--------|--------|
|RESTClient|client-go/rest/|基础客户端，对HTTP Request进行了封装，实现了RESTful风格的API；其他三种客户端都是基于它实现的|
|ClientSet|client-go/kubernetes/|在RESTClient的基础上封装了对Resource和Version的管理方法，每个Resource和Version都以函数的方式暴露给开发者，只能处理k8s内置资源，是由client-gen代码生产器自动生成；另外，我们使用ClientSet的话是必须要知道Resource和Version， 例如AppsV1().Deployments或者CoreV1.Pods，缺点是不能访问CRD自定义资源|
|DynamicClient|client-go/dynamic/|包含一组动态的客户端，可以对任意的K8S API对象执行通用操作，包括CRD自定义资源|
|DiscoveryClient|client-go/discovery/|提供一个发现客户端，用于发现kube-apiserver所支持的资源组（Group）、资源版本(Version）和资源信息（Resources）|

以上4种客户端都可通过kubeconfig配置信息连接到指定的k8s API server。

3）kubeconfig配置管理
----
kubeconfig用于管理访问kube-apiserver的配置信息，同时支持访问kube-apiserver的配置管理，另外，k8s其他组件都是用kubeconfig配置信息来连接kube-apiserver组件。kubeconfig中存储了集群、用户、命名空间和身份验证等信息；kubeconfig配置信息如下：
```
$ cat $HOME/.kube/config
apiversion: v1
kind: Config
preferences: {}

clusters: // 定义k8s集群信息，例如：kube-apiserver的服务地址及集群的证书信息等
-  cluster:
   name: dev-cluster
   
users:  // 定义k8s集群用户身份验证的客户端凭据，例如：token、client-key、username/password等
-  name: dev-user

contexts:  // 定义k8s集群用户信息和命名空间等，用于将请求发送到指定的集群。
-  context:
   name: dev-context
```
client-go会读取kubeconfig配置信息并生成config对象，用于和kube-apiserver通信，示例代码如下：
```
package main

import (
    "k8s.io/client-go/tools/clientcmd"
)

func main() {
    config, err := clientcmd.BuildConfigFromFlags("", "/root/.kube/config")
    if err != nil {
        panic(err)
    }
}
```
