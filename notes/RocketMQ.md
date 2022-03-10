# RocketMQ

## 为什么使用 MQ

- 应用解耦

请求方(生产者) --------> MQ -------> 响应方(消费者)

订单系统 ---------> MQ ---------> 库存系统、支付系统、物流系统、大数据系统

消费者存活与否不影响生产者，降低系统的耦合性，提高容错率、可维护性

- 异步提速

生产者发完消息，可以继续下一步业务逻辑，提升用户体验和系统的吞吐量

- 削峰填谷

用 MQ 限制消费的以一定速度进行，高峰期的数据会被积压在 MQ 中，高峰就被削掉了，高峰期过后，将积压消息在以一定速度消费，即填谷。提高系统稳定性

## MQ 的劣势

- 系统可用性降低

系统引入的外部依赖越多，系统稳定性越差。一旦 MQ 宕机，就会对业务造成影响。**如何保证 MQ 的高可用**

- 系统复杂度提高

MQ 的加入大大增加了系统的复杂度，以前系统间是同步的远程调用，现在是通过 MQ 进行异步调用。**如何保证消息没有被重复消费？怎么处理消息丢失情况？怎么保证消息传递的顺序性？**

- 一致性问题
A 系统处理完业务，通过 MQ 给 B、C、D 三个系统法消息数据，如果 B、C 系统处理成功，D 系统处理失败。**如何保证消息数据处理的一致性？**

## RocketMQ 部署

- Name Server

```java
docker pull apache/rocketmq

docker run -d apache/rocketmq:latest --name rmqnamesrv -p 9876:9876 --privileged=true --network rocketmq -v /docker/rocketmq/namesrv/logs:/root/logs -v /docker/rocketmq/namesrv/store:/root/store -e "MAX_POSSIBLE_HEAP=100000000" apache/rocketmq sh rmqnamesrv autoCreateTopicEnable=true


```

```java
mkdir /mydata/rocketmq/broker/conf -p
vi /mydata/rocketmq/broker/conf/broker.conf


brokerClusterName = DefaultCluster
brokerName=broker-a
brokerId=0
deleteWhen=04
fileReservedTime=48
brokerRole=ASYNC_MASTER
flushDiskType=ASYNC_FLUSH
brokerIP1=1.15.156.232

```