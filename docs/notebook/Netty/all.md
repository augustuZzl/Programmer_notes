## 第一个程序

第一个程序：使用 `netty` 编写一个服务器，客户端（例如浏览器）发送一个请求，返回 `Hello World`

使用 IDEA（Eclipse）创建一个 Gradle（Maven）项目，引入 `netty` 依赖即可

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
                    .childHandler(new ChannelInitializer<SocketChannel>() {

                        @Override
                        protected void initChannel(SocketChannel ch) throws Exception {
                            ChannelPipeline pipeline = ch.pipeline();
                            pipeline.addLast("httpServerCodec", new HttpServerCodec());
                            pipeline.addLast("httpServerHandler", new SimpleChannelInboundHandler<HttpObject>() {

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
                            });
                        }
                    });
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
```

