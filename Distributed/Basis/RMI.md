## 分布式通信框架RMI

- **什么是RPC**
  RPC（Remote Procedure Call,远程过程调用），一般用来实现部署在不同机器上的系统之间的方法调用，使得程序能够像访问本地系统资源一样，通过网络传输去访问远端系统资源；对于客户端来说， 传输层使用什么协议，序列化、反序列化都是透明的。

- **什么是RMI**

  RMI 全称是remote method invocation – 远程方法调用，一种用于远程过程调用的应用程序编程接口，是纯java 的网络分布式应用系统的核心解决方案之一。RMI 目前使用Java 远程消息交换协议JRMP（Java RemoteMessageing Protocol） 进行通信，由于JRMP 是专为Java对象制定的，是分布式应用系统的百分之百纯java 解决方案,用Java RMI 开发的应用系统可以部署在任何支持JRE的平台上，缺点是，由于JRMP 是专门为java 对象指定的，因此RMI 对于非JAVA 语言开发的应用系统的支持不足，不能与非JAVA 语言书写的对象进行通信。

- 

