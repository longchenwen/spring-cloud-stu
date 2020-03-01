# SpringCloud 的学习

## 1.springlcoud-eureka 服务注册中心配置

```
spring:
  application:
    name: eureka-server

eureka:
  server:
    enable-self-preservation: false #关闭自我保护机制
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
