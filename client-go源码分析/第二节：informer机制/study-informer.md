overview
----
本小节主要学习informer机制，在k8s系统中，组件之间通过HTTP协议进行通信，在不依赖任何中间件的情况下，需要保证消息的实时性、可靠性、顺序性等。
k8s是如何做到的呢？就是informer机制，k8s的其他组件都是通过client-go的informer机制与k8s API server进行通信。
如果每次每个组件都直接与API server交互，去读取或写入到后端etcd中，这样会对API serve和etcd造成很大的负担。
