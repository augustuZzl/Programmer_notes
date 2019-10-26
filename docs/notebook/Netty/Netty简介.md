> 摘自官网首页

### 概念

**Netty is *an asynchronous event-driven network application framework* for rapid development of maintainable high performance protocol servers & clients.**

`Netty`是***一个异步事件驱动的网络应用程序框架，***用于快速开发可维护的高性能协议服务器和客户端。

`Netty`是一个`NIO`客户端服务器框架，可以快速轻松地开发网络应用程序，例如协议服务器和客户端。它极大地简化和简化了`TCP`和`UDP`套接字服务器等网络编程。

**快速简便**并不意味着的应用程序将遭受可维护性或性能问题的困扰`Netty`经过精心设计，结合了许多协议（例如`FTP`，`SMTP`，`HTTP`以及各种基于二进制和文本的旧式协议）的实施经验。最终，`Netty`成功地找到了一种无需妥协即可轻松实现开发，性能，稳定性和灵活性的方法。

![](<https://netty.io/images/components.png>)

### 特征

#### 设计

- 适用于各种传输类型的统一 `API` - 阻塞和非阻塞套接字
- 基于灵活且可扩展的事件模型，可将关注点明确分离
- 高度可定制的线程模型-单线程，一个或多个线程池，例如 `SEDA`
- 真正的无连接数据报套接字支持（从 `3.1` 开始）

#### 使用方便

- 记录良好的 `Javadoc`，用户指南和示例
- 没有其他依赖关系，`JDK 5（Netty 3.x）`或 `6（Netty 4.x）`就足够了
  - 注意：某些组件（例如 `HTTP / 2`）可能有更多要求

#### 性能

- 更高的吞吐量，更低的延迟
- 减少资源消耗
- 减少不必要的内存复制

#### 安全

- 完整的 `SSL / TLS` 和 `StartTLS` 支持