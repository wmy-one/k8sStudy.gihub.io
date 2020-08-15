overview
----
kuberbetes系统使用client-go作为go语言的官方编程式交互客户端库，提供对kubernetes API server服务的交互访问。kubernetes源码中已经集成了client-go源码，
路径为：vendor/k8s.io/client-go，另外，可以通过`go get k8s.io/client-go@kubernetes-1.14.10` 来安装client-go。开发者常使用client-go基于kubernetes做二次开发，
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
client-go提供了
