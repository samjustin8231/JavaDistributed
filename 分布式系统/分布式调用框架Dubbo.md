# 分布式调用框架-Dubbo


围绕Dubbo来讲解，现在主流的是SpringCloud。两种主流的技术栈。
Java工程师对Dubbo和SpringCloud，起码是基本原理，有一定了解。



## 注册中心挂了可以继续通信吗？
可以，因为刚开始初始化的时候，消费者会将提供者的地址等信息拉取到本地缓存，所以注册中心挂了可以继续通信。