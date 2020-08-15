    kuberbetes系统使用client-go作为go语言的官方编程式交互客户端库，提供对kubernetes API server服务的交互访问。<br>kubernetes源码中以及集成了client-go源码，路径为：vendor/k8s.io/client-go，另外，可以通过`go get k8s.io/client-go@kubernetes-1.14.10` 来安装client-go。<\br>开发者常使用client-go基于kubernetes做二次开发，因此，client-go是开发者应该熟练掌握的必备技能。
===
1）client-go源码结构
---
