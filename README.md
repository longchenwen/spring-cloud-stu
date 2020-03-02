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


