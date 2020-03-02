# SpringCloud 的学习

## 1.springlcoud-eureka 服务注册中心配置

```
spring:
  application:
    name: eureka-server
    

eureka:
  server:
    enable-self-preservation: false #关闭自我保护机制,一般本地关闭,线上开始
    eviction-interval-timer-in-ms: 2000 # 失效2s剔除服务
  client:
    service-url:
      defaultZone: http://localhost:8080/eureka,http://localhost:8082/eureka
     # defaultZone: http://${eureka.instance.hostname}:8080/eureka
    register-with-eureka: true #不把自己作为一个客户端注册到自己身上 集群是ture
    fetch-registry: true # 因为自己是注册中心,不需要检索服务
  instance:
    #服务注册中心Ip地址
    hostname: 127.0.0.1
    prefer-ip-address: false
```

## 2.springcloud-eureka 消费中心配置

```
spring:
  application:
    name: eureka-client
eureka:
  client:
    service-url:
      defaultZone: http://localhost:8081/eureka, http://localhost:8080/eureka
    # 把自己注册 到euraka服务上去
    register-with-eureka: true
    # 检索服务,就是找服务
    fetch-registry: true
  instance:
    hostname: locahost
server:
  port: 8090
```
## 3.服务间的相互调用
* 1.所有的服务都是注册到eureka服务注册器中
* 2.服务间的调用才用的是RestTemplate这个类
    ```
        需要注意几点:
        1.RestTemplate这个类要注册时spring容器中:
        
        @SpringBootApplication
        @EnableEurekaClient
        public class EurekaClient1Application {

            public static void main(String[] args) {
                SpringApplication.run(EurekaClient1Application.class, args);
            }

            @Bean
            @LoadBalanced //使用的意义是:在访问注册到eureka的服务,可以使用别名访问,还用负载均衡的意义
            public RestTemplate restTemplate(){
                return new RestTemplate();
            }
    ```
## 4.eureka的自我保护机制

1. server端自我保护机制的设置
```
eureka:
  server:
    enable-self-preservation: false # 关闭自我保护的机制
    eviction-interval-timer-in-ms: 2000 # 失效2s剔除服务
  instance:
    #注册的ip地址 强调是ip书写格式
    hostname: 127.0.0.1
    prefer-ip-address: false
  client:
    fetch-registry: true
    register-with-eureka: false #不把自己作为一个客户端注册到自己身上 集群是ture
    service-url:
      defaultZone: http://localhost:8081/eureka,http://localhost:8082/eureka``

```
2. client端自我保护机制的设置

```
eureka:
  client:
    service-url:
      defaultZone: http://localhost:8081/eureka, http://localhost:8080/eureka, http://localhost:8082/eureka
    # 把自己注册 到euraka服务上去
    register-with-eureka: true
    # 检索服务,就是找服务
    fetch-registry: true

  instance:
    #心跳检测检测与续约时间,Eureka客户端向eureka server服务端发送心跳的时间间隔，单位为秒（客户端告诉服务端自己会按照该规则）
    lease-renewal-interval-in-seconds: 1
    #Eureka客户端向服务端发送心跳的时间间隔，单位为秒（客户端告诉服务端自己会按照该规则）
    lease-expiration-duration-in-seconds: 2
```

## 5.zookeeper服务注册服务(待)

## 6.Consul服务注册中心(待)

## 7.Ribbon负载均衡

1. ribbon负载均衡和nginx负载均衡的区别:
   * nginx是客户端所有请求统一交给nginx，由nginx进行实现负载均衡请求转发，属于服务器端负载均衡。
 既请求有nginx服务器端进行转发。
   * Ribbon是从eureka注册中心服务器端上获取服务注册信息列表，缓存到本地，让后在本地实现轮训负载均衡策略。
 既在客户端实现负载均衡
 
 2. Ribbon超时时间(开启feign 就的默认开始ribbon)配置:
 ```
  ###设置feign客户端超时时间
  ribbon:
  ###指的是建立连接所用的时间，适用于网络状况正常的情况下，两端连接所用的时间。
   ReadTimeout: 5000
  ###指的是建立连接后从服务器读取到可用资源所用的时间。 
   ConnectTimeout: 5000

 ```

## 8.Feign客户端调用工具(相当springmvc,***Feign是对Ribbon和Hystrix的整合***)

1. Feign介绍:Feign客户端是一个web声明式http远程调用工具，提供了接口和注解方式进行调用。

2.Feign超时设置:

## 9.SpringCloud之熔断器Hystrix

1.Hystrix介绍:</br>
  SpringCloud 是微服务中的翘楚，最佳的落地方案。

  在微服务架构中多层服务之间会相互调用，如果其中有一层服务故障了，可能会导致一层服务或者多层服务

  故障，从而导致整个系统故障。这种现象被称为服务雪崩效应。

  SpringCloud 中的 Hystrix 组件就可以解决此类问题，Hystrix 负责监控服务之间的调用情况，连续多次失败的

  情况进行熔断保护。保护的方法就是使用 Fallback，当调用的服务出现故障时，就可以使用 Fallback 方法的

  返回值；Hystrix 间隔时间会再次检查故障的服务，如果故障服务恢复，将继续使用服务。
