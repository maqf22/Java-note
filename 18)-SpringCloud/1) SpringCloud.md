
# 一、应有架构简介

## 1. 程序架构演变过程

![[Pasted image 20230706181733.png]]

## 2. 单体应用架构

![[Pasted image 20230706181808.png]]

## 3. 垂直应用架构

![[Pasted image 20230706181855.png]]


## 4. 分布式应用架构

![[Pasted image 20230706181941.png]]

## 5. 微服务应用架构

![[Pasted image 20230706182028.png]]
![[Pasted image 20230706182058.png]]

微服务架构的优缺点：
- 优点：
	- 通过服务的原子拆分，以及微服务的独立打包、部署和升级，小团队的开发周期缩短，大幅降低运维成本
	- 微服务遵循单一原则，微服务之间采用RESTful等规范及传输协议
- 缺点：
	- 微服务过多，服务处理成本高，不利于系统维护
	- 分布式系统开发的技术成本高（容错、分布式、事务等）


# 二、SpringCloud概述

SpringCloud是一系列框架的**有序集合**
它利用SpringBoot的开发便利性巧妙地简化了分布式系统基础设施的开发
如服务发现注册、配置中心、消息总线、负载均衡、断路器、数据监控等
都可以用SpringBoot的开发风格做到一键启动和部署
SpringCloud并没有重复造轮子，它只是将各家公司开发的比较成熟，经得起实际考验的服务框架组合起来
通过SpringBoo风格进行再封装屏蔽掉了复杂的配置和实现原理
最终给开发者留出了一套简单易懂，、易部署和易维护的分布式系统开发工具包

![[Pasted image 20230706182759.png]]


# 三、快速上手

## 1. 直连方式访问

1. 创建服务提供者`SC-01-Provider`，勾选web起步依赖
2. 创建控制器
```java
package com.example.controller;

import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class ProviderController {
    @RequestMapping("/test")
    public String test() {
        return "SpringCloud服务提供者信息(Provider)";
    }
}
```
3. 修改`application.properties`服务端口号为8081
```properties
server.port=8081
```
4. 创建服务消费者`SC-01-Consumer`，并勾选web起步依赖
5. 创建`RestTemplate`对象到`Spring`容器中，`com/example/conf/RestTemplateConf.java`
```java
package com.example.conf;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.client.RestTemplate;

@Configuration
public class RestTemplateConf {
    @Bean
    public RestTemplate getRestRTemplate() {
        return new RestTemplate();
    }
}
```
6. 创建控制器，并注入`RestTemplate`对象，实现远程调用
```java
package com.example.controller;

import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.client.RestTemplate;

import javax.annotation.Resource;

@RestController
public class ConsumerController {
    @Resource
    private RestTemplate restTemplate;

    @RequestMapping("/test")
    public String test() {
        String URL = "http://localhost:8081/test";
        ResponseEntity<String> response = restTemplate.getForEntity(URL, String.class);
        String info = response.getBody();
        return "SpringCloud消费者消费服务 =>" + info;
    }
}
```

## 2. 使用注册中心访问

SpringCloud中推荐使用的注册中心是Eureka，它不需要额外安装任何服务，只需要在项目搭建的时候添加起步依赖即可
注意：此处需要使用特定的版本支持，新版本中很多功能还未能兼容
- SpringBoot版本采用2.3.12-RELEASE
- SpringCloud版本采用Hoxton.SR11

**步骤**
1. 创建`SC-02-EurekaServer`模块，并添加`Eureka Server` `web`起步依赖
2. 修改`applicat.properties`配置文件
```properties
server.port=9100
# 设置该服务注册中心的hostname
eureka.instance.hostname=localhost
# 防止自己注册自己
eureka.client.register-with-eureka=false
# 不检索其他服务，因为服务注册中心本身的作用就是维护服务实例，不需要去检索其他服务
eureka.client.fetch-registry=false
# 指定服务注册中心的位置
eureka.client.service-url.defaultZone=https://${eureka.instance.hostname}:${server.port}/eureka
```
3. 在引导类上添加注解，激活`Eureka Server`
```java
@SpringBootApplication
@EnableEurekaServer // 激活Eureka服务
public class Sc02EurekaServerApplication {
    public static void main(String[] args) {
        SpringApplication.run(Sc02EurekaServerApplication.class, args);
    }
}
```
4. 启动项目`http://localhost:9100/`

5. 创建服务提供者模块，勾选`web`起步依赖和`Eureka Client`起步依赖
6. 修改`application.properties`配置文件
```properties
server.port=8081
# 服务名称（建议使用POM文件中的模块名）
spring.application.name=SC-02-Provider
#指定Eureka的访问地址
eureka.client.service-url.defaultZone=http://localhost:9100/eureka
```
7. 在引导类中添加注解，激活注册中心客户端
```java
@SpringBootApplication
@EnableEurekaClient
public class Sc02ProviderApplication {
    public static void main(String[] args) {
        SpringApplication.run(Sc02ProviderApplication.class, args);
    }
}
```
8. 编写控制器`com/example.controller.ProviderController.java`
```java
package com.example.controller;

import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class ProviderController {
    @RequestMapping("/test")
    public String test() {
        return "使用了Eureka注册中心的服提供者提供的信息 Info~";
    }
}
```
9. 运行测试`localhost:8081`

10. 创建消费者模块，添加`web`和`Eureka Client`起步依赖
11. 编写`application.properties`配置文件
```properties
server.port=8082  
spring.application.name=SC-02-Consumer  
eureka.client.service-url.defaultZone=http://localhost:9100/eureka
```
12. 编写restTemplate配置`com.example.conf.ConsumerConf.java`
```java
package com.example.conf;  
  
import org.springframework.cloud.client.loadbalancer.LoadBalanced;  
import org.springframework.context.annotation.Bean;  
import org.springframework.context.annotation.Configuration;  
import org.springframework.web.client.RestTemplate;  
  
@Configuration  
public class RestTemplateConf {  
    @Bean  
    @LoadBalanced // restTemplate需要使用Ribbon的负载均衡作为支持  
    public RestTemplate getRestRTemplate() {  
        return new RestTemplate();  
    }  
}
```
13. 编写控制器`com.example.controller.ConsumerController.java`
```java
package com.example.controller;

import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.client.RestTemplate;

import javax.annotation.Resource;

@RestController
public class ConsumerController {
    @Resource
    private RestTemplate restTemplate;

    @RequestMapping("/test")
    public String test() {
        // 定义服务的消费地址（服务提供的地址） - 直连方式
        // String URL = "http://localhost:8081/test";
        // 定义服务的消费地址（服务提供的地址） - 使用注册中心发现的服务
        String URL = "http://SC-02-PROVIDER:8081/test";
        // 通过restTemplate对象发送一个get请求
        ResponseEntity<String> response = restTemplate.getForEntity(URL, String.class);
        String info = response.getBody();
        return "SpringCloud消费者使用Eureka注册中心发现消费服务 => " + info;
    }
}
```
14. 在引导类中添加注解，激活注册中心客户端
```java
@SpringBootApplication
@EnableEurekaClient
public class Sc02ConsumerApplication {
    public static void main(String[] args) {
        SpringApplication.run(Sc02ConsumerApplication.class, args);
    }
}
```
15. 运行测试`http://localhost:8082/test`
![[Pasted image 20230707174703.png]]


# 四、高可用注册中心

