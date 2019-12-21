# Netty工作原理

多个服务消费者

一个服务提供者
ServerSocketChannel监听端口号
Selector监听网络事件(轮询) -> Acceptor


SocketChannel(建立好的连接) Selector -> Processor线程