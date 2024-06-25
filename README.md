# interview

## go 语言基础

书本

- Go 语言设计与实现

视频

- bilibili - **[幼麟实验室的个人空间-幼麟实验室个人主页-哔哩哔哩视频 (bilibili.com)](https://space.bilibili.com/567195437?spm_id_from=333.788.0.0)**

常见面试题

- 谈一下 Go 的 GC 机制
- 说下三色标记法算法的原理
- rpc 的具体实现
- go 的 mutx 怎么使用，乐观锁和悲观锁怎么实现，使用场景是什么？
- channel/ map/ slice 底层实现 / pool/ context
- [GMP 模型](https://kiosk007.top/post/golang-gmp/)
- GO 启动顺序

博客：

- [与日俱进，在 Go 1.20 中这种高效转换的方式又变了 (colobu.com)](https://colobu.com/2022/09/06/string-byte-convertion/)

## mysql

课程

- 极客时间 - [01 | 基础架构：一条 SQL 查询语句是如何执行的？-MySQL 实战 45 讲-极客时间 (geekbang.org)](https://time.geekbang.org/column/article/68319)

## redis

课程

- 极客时间 - [Redis 核心技术与实战 (geekbang.org)](https://time.geekbang.org/column/intro/100056701)

## MQ

博客

- [Docs (feishu.cn)](https://w8f48xv1h6.feishu.cn/wiki/wikcnAJFKENsXWuBOgvgicTOEhc)

### RabbitMQ 架构设计

```plaintext

             Connection             Broker(RabbitMQ Server)                       Connection
                                        Virtural Host
                Channel                       Binding                               Channel
                Channel ----------> Exchange----------->  Queue   <-----------      Channel
Producer ->     Channel                       Binding                               Channel  <------ Consumer
                Channel             Exchange----------->  Queue   <------------     Channel
                                                |
                                                ------>   Queue

                                    Virtual     Virtual
                                    Host        Host

每个 Virtual Host 之间的数据是隔离的
```

### RabbitMQ 如何保证消息不丢失

三个地方可以出现消息丢失

- Producer -> Broker
  - why: 可能网络波动，或者服务器宕机
  - how: confirm 消息确认机制
    - 消息正确投递到 queue 时，会返回 ack
    - 消息没有正确投递到 queue 时，会返回 nack。如果 exchange 没有绑定 queue，也会出现消息丢失
  - 使用方法
    - 生产者通过 confirm.select 方法将 Channel 设置为 Confirm 模式
    - 发送消息后，通过添加 add_confirm_listerner 方法，监听消息的确认状态
- Broker -> 磁盘存储
  - why: 未能及时存储完成持久化，服务器出现宕机重启
  - how: 消息持久化机制
    - 持久化机制是指将消息存储到磁盘，以保证在 RabbitMQ 服务器宕机或重启时，消息不会丢失
  - 使用方法
    - 生产者通过将消息的 delivery_mode 属性设置为 2,将消息标记为持久化
    - 队列也需要进行持久化设置，durable 属性设置为 true
  - 注意事项：
    - 持久化机制会影响性能，因此在需要确保消息不丢失的场景下使用
- Consumer <- Broker
  - why: 网络波动/未能正常消费
  - how: ACK 事务机制
    - 消费者成功处理后，发送 ack，告知 RabbitMQ 消息可以被移除
  - 使用方法
    - 默认开启，当消息被消费者接收后(除非消费者发生异常)，会立即从队列中删除
    - 可以手动开启 ACK 机制，通过将 auto_ack 参数设置为 False，手动控制消息的 ACK

### RabbitMQ 如何保证消息不被重复消费

什么情况下会导致消息被重复消费呢？

1. 生产者：生产者可能会重复推送一条数据到 MQ 中，接口被重复调用了 2 次，没有做接口幂等性导致
2. MQ: 在消费者消费完准备响应 ack 消息消费成功后，MQ 突然挂了，导致 MQ 以为消费者还未消费该条数据，MQ 恢复后再次推送了该条消息，导致了重复消费
3. 消费者：消费者已经消费完消息，正准备但是还未响应给 ack 消息到时，此时消费者挂了，服务重启后，MQ 以为消费者还没有消费该消息，再次推送了该条消息

解决方案：

1. 业务层做处理

### RabbitMQ 如何解决消息堆积问题

什么情况会出现

1. 消费者的消费速度远小于生产者的生产速度
2. queue 队列的大小，队列存满后导致队列溢出
3. 开启手动 ack 后，broker 要收到 consumer 的 ack 才移除消息，性能下降

解决方案

1. 消费者处理消息的速度太慢

- 增加消费者数量
- 优化消费者性能
- 消息预取限制,避免一次处理过多消息而导致处理缓慢

2. 队列的容量太小

- 增加队列的容量：调整队列设置以允许更多消息存储

3. 网络故障

- 监控和高级
- 持久化可高可用性

## 网络

博客

- [小林 coding (xiaolincoding.com)](https://www.xiaolincoding.com/)

书本

- 计算机网络 - 第七版
- 图解 HTTP
- 计算机网络 -自顶向下方法

思维导图

- 计算机网络.emmx

## 算法

[代码随想录](https://programmercarl.com/)

## 个人面试前需要去看到

- leaf 框架
- gin 框架
- go-zero 框架
- 红与黑小游戏开发流程
- mysql \ redis \ rabbitmq
- 项目经历
- go 基础
