#### SpringBoot秒杀系统

> 系统介绍：模拟了秒杀商品在高并发场景下进行优化的过程，大量使用缓存来减轻数据库的压力、使用消息队列来进行异步下单并对秒杀接口进行限流优化等措施，并在优化前后使用 JMeter 压测工具测试整个秒杀过程的 QPS
>
> 并发控制：Java 单机锁、数据库乐观锁、悲观锁、分布式锁

- [01：项目框架搭建](notebook/SSM/【SpringBoot商城秒杀系统01】项目框架搭建)
- [02：数据层设计](notebook/SSM/【SpringBoot商城秒杀系统02】数据层设计)
- [03：登录功能实现](notebook/SSM/【SpringBoot商城秒杀系统03】登录功能实现)
- [04：分布式Session](notebook/SSM/【SpringBoot商城秒杀系统04】分布式Session)
- [05：秒杀功能实现](notebook/SSM/【SpringBoot商城秒杀系统05】秒杀功能实现)
- [06：Jmeter模拟用户秒杀](notebook/SSM/【SpringBoot商城秒杀系统06】Jmeter模拟用户秒杀)
- [07：秒杀优化](notebook/SSM/【SpringBoot商城秒杀系统07】秒杀优化)
