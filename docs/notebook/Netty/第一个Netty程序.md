### 第一个 Netty 程序

第一个程序：使用 `netty` 编写一个服务器，客户端（例如浏览器）发送一个请求，返回 `Hello World`

使用 IDEA（Eclipse）创建一个 Gradle（Maven）项目，引入 `netty` 依赖即可

> Netty 以全新的 API 封装了 NIO 编程，一个 "Hello World" 都显得这么复杂，不过先不用搞懂这段程序，先感受一下即可（还是做了详细的注释，其实还是很容易看懂的）。这个程序可以多读几遍
>
> 提示：千万不要专注于下面第一个程序的任何一个细节（可能会打击自信心），我们以后会一步步了解的

```java
public class TestServer {

    public static void main(String[] args) {
        // 事件循环线程组，NIO，boos 负责接收请求
        EventLoopGroup bossGroup = new NioEventLoopGroup();
        // worker 负责执行请求
        EventLoopGroup workerGroup = new NioEventLoopGroup();

        try {
            // 服务启动
            ServerBootstrap bootstrap = new ServerBootstrap();
            bootstrap.group(bossGroup, workerGroup)
                    .channel(NioServerSocketChannel.class)  // nio 类型的 channel
                    .childHandler(new TestInitializer());
            // 绑定端口，sync 表示同步方法阻塞直到绑定成功
            ChannelFuture channelFuture = bootstrap.bind(9999).sync();
            // 程序一直等待，直到 channel 关闭
            channelFuture.channel().closeFuture().sync();
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            // 释放资源
            bossGroup.shutdownGracefully();
            workerGroup.shutdownGracefully();
        }

    }

}

class TestInitializer extends ChannelInitializer<SocketChannel> {

    @Override
    protected void initChannel(SocketChannel ch) throws Exception {
        ChannelPipeline pipeline = ch.pipeline();
        pipeline.addLast("httpServerCodec", new HttpServerCodec());
        pipeline.addLast("httpServerHandler", new TestHandler());
    }

}

class TestHandler extends SimpleChannelInboundHandler<HttpObject> {

    @Override
    protected void channelRead0(ChannelHandlerContext ctx, HttpObject msg) throws Exception {
        if(msg instanceof HttpRequest) {
            // 向客户端响应的内容
            ByteBuf content = Unpooled.copiedBuffer("Hello World", CharsetUtil.UTF_8);
            FullHttpResponse response = new DefaultFullHttpResponse(HttpVersion.HTTP_1_1,
                    HttpResponseStatus.OK, content);
            // 响应头
            response.headers().set(HttpHeaderNames.CONTENT_TYPE, "text/plain");
            response.headers().set(HttpHeaderNames.CONTENT_LENGTH, content.readableBytes());
            // 写到缓冲区并推送到客户端
            ctx.writeAndFlush(response);
        }
    }

}
```

运行此程序，在浏览器中输入：http://localhost:9999 ，可以看到 `Hello World` 了。

### HttpObject

这个程序中与我们最相关的就是 `SimpleChannelInboundHandler<HttpObject>` 这个抽象类了，我们实现了他的抽象方法 `channelRead0()` ，在其中接受客户端请求，并定义返回给客户端的数据。我们看看 `HttpObject` 的作用。

```java
@Override
protected void channelRead0(ChannelHandlerContext ctx, HttpObject msg) throws Exception {
    if(msg instanceof HttpRequest) {
        HttpRequest request = (HttpRequest) msg;
        String method = request.method().name();
        String uri = request.uri();
        System.out.println("请求方法：" + method + ", 请求地址：" + uri);

        ......
    }
}
```

在浏览器中输入：http://localhost:9999/hello ：

```
请求方法：GET, 请求地址：/asd
请求方法：GET, 请求地址：/favicon.ico
```

### channel 执行流程

再来看看在我们 `nio` 中定义为管道的组件：`channel `。我们在 `channelRead0()` 这个方法中读写请求，其实有关 `channel` 还有几个方法，我们继续实现他们，看看 `channel` 的执行流程。

```java
@Override
protected void channelRead0(ChannelHandlerContext ctx, HttpObject msg) throws Exception {
    System.out.println("请求方法：" + method + ", 请求地址：" + uri);
}

@Override
public void handlerAdded(ChannelHandlerContext ctx) throws Exception {
    System.out.println("channel add");
    super.handlerAdded(ctx);
}

@Override
public void handlerRemoved(ChannelHandlerContext ctx) throws Exception {
    System.out.println("channel remove");
    super.handlerRemoved(ctx);
}

@Override
public void channelRegistered(ChannelHandlerContext ctx) throws Exception {
    System.out.println("channel register");
    super.channelRegistered(ctx);
}

@Override
public void channelUnregistered(ChannelHandlerContext ctx) throws Exception {
    System.out.println("channel unRegister");
    super.channelUnregistered(ctx);
}

@Override
public void channelActive(ChannelHandlerContext ctx) throws Exception {
    System.out.println("channel active");
    super.channelActive(ctx);
}

@Override
public void channelInactive(ChannelHandlerContext ctx) throws Exception {
    System.out.println("channel unActive");
    super.channelInactive(ctx);
}
```

运行结果：需要等一会才会出现 `unActive、UnRegister、remove`，这是由于 `HTTP/1.1` 的 `keep-alive` 特性

```
channel add
channel add
channel register
channel register
channel active
channel active
请求方法：GET, 请求地址：/
请求方法：GET, 请求地址：/favicon.ico
channel unActive
channel unRegister
channel remove
```

修改为 `HttpVersion.HTTP_1_0`，再执行：

```
channel add
channel add
channel register
channel register
channel active
channel active
请求方法：GET, 请求地址：/
channel unActive
channel unRegister
channel remove
请求方法：GET, 请求地址：/favicon.ico
channel unActive
channel unRegister
channel remove
```

这两者的就是由于 `HTTP/1.0` 和 `HTTP/1.1` 的区别了：[HTTP 1.0 vs 1.1](<https://stackoverflow.com/questions/246859/http-1-0-vs-1-1?r=SearchResults>)