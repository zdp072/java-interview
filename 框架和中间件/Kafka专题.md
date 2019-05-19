# Kafka

## kafka架构中包含哪些角色
1. 生产者(producer): 消息生产
2. 代理(broker): 数据缓存和分发
3. 消费者(consumer): 消息消费

kafka给producer和consumer提供注册和订阅的接口，数据从producer发送到broker，broker承担数据缓存和分发的作用，consumer监听订阅的主题并消费数据。

## kafka如何保证数据的不丢失
#### 生产者数据不丢失
ack机制：在kafka发送数据的时候，每次发送消息都会有一个确认反馈机制，确保消息能被正常的接受到。

#### 消费者数据不丢失
通过offset commit来保证数据被消费，kafka自己记录了每次消费的offset数值，下次继续消费的时候，接着上次的offset进行消费即可。

## Kafka如何实现高可用?
Kafka由多个broker组成，每个broker就是一个节点；我们创建一个topic，这个topic可以划分多个partition，每个partition可以存在于不同的broker上，每个partition放一部分数据。

Kafka提供HA高可用机制，采用数据冗余的方式，每个partition的数据会同步到其他机器上，形成自己的多个副本，这些broker节点会选举出一个leader出来，那么数据生产和消费都跟这个leader打交道，其他节点就是follower。如果某个broker宕机了，那么将会从follower中重新选举出一个新的leader出来，大家继续读写那个新的leader即可。

写数据的时候，生产者就写leader，然后leader将数据落地写本地磁盘，接着其他foller自己主动从leader拉数据，一旦所有follower同步好数据了，就会发送ack给leader，leader收到所有follower的ack之后，就会返回写成功的消息给生产者。

消费的时候，只会从leader中去读，当消息被所有follower都同步成功并返回ack的时候，这个消息才会被消费者读到。

## Kafka如何保证消息消费的幂等性
#### 为何会出现重复消费？
Kafka里面有个offset的概念，就是每个消息写进去，都有一个offset，代表消息的序号，consumer消费了数据之后，会把自己消费过的消息的offset提交一下，表示我已经消费过了，但如果你kill进程了，但还没来得及提交offset，重启之后，少数消息会被再次消费一次。

#### 如何保证系统幂等？
业务数据设计一个全局唯一的id，消费的数据之后先判断是否已经处理过，如果处理过则丢弃。设计数据库唯一索引，确保不会插入重复数据。

## Kafka如何保证消息的可靠性传输
#### 消费者弄丢数据
当你消费到消息后，然后消费者自动提交了offset，让Kafka以为你已经消费好了这个消息，但其实你才刚准备处理这个消息你就挂了，那么这个消息就丢失了。

一般来说Kafka是自动提交offset，那么只要关闭自动提交offset，在处理完之后自己手动提交offset，就可以保证消息不会丢。但此时还是有可能会重复消费，比如你刚处理完，还没提交offset你就挂了，重启后肯定会重复消费，需要我们保证幂等。

#### Kafka弄丢数据
当kafka某个broker宕机，刚好这个broker是leader，然后重新选举partition的leader，假设follower的数据还没同步完就被选中为leader，这个时候就会丢失一部分数据

#### 生产者会不会弄丢数据
假设设置acks=all，所有节点都写入副本数据，才认为写成功。即leader接受到消息后，所有的follower都同步到了消息之后，才认为本次写成功了，那么数据就不会丢失。

## 如何保证消息的顺序性？
kafka缺省不保证消息的有序消费。它不保证消息的全局有序，但可以保证消息的局部有序。在kafka中发送一条消息的时候，可以指定topic、partition、key等3个参数。其中partition和key是可选的。如果你指定了partition，那所有的消息会发往同一个partition，一个队列肯定是有序的。在消费端，kafka保证1个partition只能被1个consumer消费。如果你指定key，那具有同一个key的所有消息会发往同一个partition，跟上面一样，也会是有序的。