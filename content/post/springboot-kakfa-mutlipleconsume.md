---
title: "Spring Boot 集成kafka并实现批量消费"
date: 2018-05-25T15:05:56+08:00
draft: true
tags: ["spring"]
categories: "microservice"
---
# 简介
Kafka是由Apache软件基金会开发的一个开源流处理平台，由Scala和Java编写。Kafka是一种高吞吐量的分布式发布订阅消息系统，它可以处理消费者规模的网站中的所有动作流数据。 
Kafka的目的是通过Hadoop的并行加载机制来统一线上和离线的消息处理，也是为了通过集群来提供实时的消息。  
本文的目的是为了讲解如何使用springboot来向kafka发送并消费数据，并实现批量消费。
# kafka简易安装
本文采用的docker安装的方式，采用的镜像为`spotify/kafka`。  
在安装docker的电脑上运行以下命令:  
```
docker run --name=kafka -p 2181:2181 -p 9092:9092 spotify/kafka
```
```
docker run 表示docker启动容器。
--name 表示容器名称。
-p 表示端口，2181是zookeeper的端口，因为kafka默认采用zookeeper来做分布式协调。9092为kafka的应用端口。
spotify/kafka 为镜像名称。
```
# spring boot 项目
## 项目依赖
项目继承  
```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>1.4.2.RELEASE</version>
</parent>
```
项目依赖  
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
 <!--kafka start-->
<dependency>
    <groupId>org.springframework.kafka</groupId>
    <artifactId>spring-kafka</artifactId>
    <version>1.1.7.RELEASE</version>
</dependency>
<!--kafka end-->
```
## 项目配置
在src/main/resources中创建application.yml文件，内容如下
```yaml
# 项目启动端口
server:
  port: 8092
spring:
  kafka:
    # kafka服务器地址(可以多个)
    bootstrap-servers: 47.96.9.221:9092
    consumer:
      # 指定一个默认的组名
      group-id: testkafka
      # earliest:当各分区下有已提交的offset时，从提交的offset开始消费；无提交的offset时，从头开始消费
      # latest:当各分区下有已提交的offset时，从提交的offset开始消费；无提交的offset时，消费新产生的该分区下的数据
      # none:topic各分区都存在已提交的offset时，从offset后开始消费；只要有一个分区不存在已提交的offset，则抛出异常
      auto-offset-reset: earliest
      # 每次批量处理的项目数量 目前每次处理五条数据
      max-poll-records: 5
      # 每次批量处理的毫秒 目前没三秒读取一次
      max-poll-interval-ms: 3000
```
## kafka配置
创建`KafkaConfig.java`文件来对kafka的消息发送端与消息接收端进行配置。
```java
@EnableKafka
@Configuration
public class KafkaConfig {
    /**
     * kafka地址
     */
    @Value("${spring.kafka.bootstrap-servers}")
    private String bootstrapServers;
    /**
     * 默认组
     */
    @Value("${spring.kafka.consumer.group-id}")
    private String groupId;
    /**
     * 自动消费设定
     */
    @Value("${spring.kafka.consumer.auto-offset-reset}")
    private String autoOffsetReset;
    /**
     * 一次批量处理的数据量
     */
    @Value("${spring.kafka.consumer.max-poll-records}")
    private int maxPollRecords;
    /**
     * 一次批量处理的时间间隔
     */
    @Value("${spring.kafka.consumer.max-poll-interval-ms}")
    private int maxPollIntervalMs;

    @Bean
    public Map<String, Object> consumerConfig() {
        Map<String, Object> props = new HashMap<>();
        props.put(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG, bootstrapServers);
        props.put(ConsumerConfig.ENABLE_AUTO_COMMIT_CONFIG, false);
        props.put(ConsumerConfig.AUTO_COMMIT_INTERVAL_MS_CONFIG, "100");
        props.put(ConsumerConfig.SESSION_TIMEOUT_MS_CONFIG, "15000");
        props.put(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class);
        props.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class);
        props.put(ConsumerConfig.GROUP_ID_CONFIG, groupId);
        /**
         * maxPollRecords 与 maxPollIntervalMs 都是批量消费数据
         * maxPollIntervalMs为间隔时间 maxPollRecords为最大处理的数据量
         * 在间隔时间（maxPollIntervalMs参数）内（目前为3秒）kafka会根据最大处理的数据量(maxPollRecords参数，目前为5条)一次性获取对应数量，若大于5条，按照5条来消费;若小于5条，按实际消费。
         */
        //每一批数
        props.put(ConsumerConfig.MAX_POLL_RECORDS_CONFIG, maxPollRecords);
        //每一批读取间隔时间
        props.put(ConsumerConfig.MAX_POLL_INTERVAL_MS_CONFIG, maxPollIntervalMs);
        props.put(ConsumerConfig.AUTO_OFFSET_RESET_CONFIG, autoOffsetReset);
        return props;
    }

    @Bean
    ConcurrentKafkaListenerContainerFactory<String, String> kafkaListenerContainerFactory() {
        ConcurrentKafkaListenerContainerFactory<String, String> factory =
                new ConcurrentKafkaListenerContainerFactory<>();
        factory.setConsumerFactory(consumerFactory());
        factory.setBatchListener(true);
        return factory;
    }

    @Bean
    public ConsumerFactory<String, String> consumerFactory() {
        return new DefaultKafkaConsumerFactory<>(consumerConfig());
    }


    @Bean
    public Map<String, Object> producerConfigs() {
        Map<String, Object> props = new HashMap<>();
        props.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, bootstrapServers);
        props.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class);
        props.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class);
        return props;
    }

    @Bean
    public ProducerFactory<String, String> producerFactory() {
        return new DefaultKafkaProducerFactory<>(producerConfigs());
    }

    @Bean
    public KafkaTemplate<String, String> kafkaTemplate() {
        KafkaTemplate<String,String> kafkaTemplate = new KafkaTemplate<>(producerFactory());
        return kafkaTemplate;
    }
}
```
## kafka消息提供端
```java
@Slf4j
@RestController
@RequestMapping("/nergigante/")
public class KafkaMessageProducer {

    @Autowired
    private KafkaTemplate<String,String> kafkaTemplate;

    @GetMapping("send")
    public String send(){
        log.info("开始执行发送操作");
        for(int i=0;i<4;i++){
            kafkaTemplate.send("realTimeTest","realTimeTest=:"+i);
        }
        log.info("结束执行发送操作");
        return "SUCCESS";
    }

}
```
## kafka消息消费端
```java
@Slf4j
@Service
public class KafkaMessageListener {

    /**
     * kafka监听消息 并进行保单的实时处理
     * @param list
     */
    @KafkaListener(topics = "realTimeTest")
    public void realTimeTest(List<String> list) throws InterruptedException{
        log.info("线程{}正在运行",Thread.currentThread().getId());
        Thread.currentThread().sleep(10000L);
        long time = System.currentTimeMillis();
        for(String message:list) {
            log.info("批次号:{},消息列表:{}",time,message);
        }
    }
}
```
## springboot的项目启动
```java
@ComponentScan(value = {"com.qingware.nergigante"})
@SpringBootApplication
public class KafkaApplication {

    public static void main(String[] args) {
        SpringApplication.run(KafkaApplication.class, args);
    }

}
```
## 启动
启动`KafkaApplication`项目，并在浏览器输入`localhost:8092/nergigante/send`即可发送消息。  
消息消费端每3秒(spring.kafka.consumer.max-poll-interval-ms配置参数)获取一次。  
如果小于5条(spring.kafka.consumer.max-poll-records配置参数),则按照当前实际数量获取。  
如果大于5条(spring.kafka.consumer.max-poll-records配置参数),则按照5条分批获取处理。  
在消费过程中，永远只有一个线程在监听处理消息。