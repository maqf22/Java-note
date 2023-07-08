
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

**步骤**

1. 创建两个注册中心服务模块分别为
`SC-03-EurekaServier-9100`和`SC-03-EurekaServer-9200`

2. 修改两个模块的配置文件分别为
`SC-03-EurekaServer-9100.src.main.resource.application.properties`
```properties
server.port=9100

# 设置该服务注册中心hostname,使用域名eureka9100
eureka.instance.hostname=eureka9100
# 防止自己注册自己
eureka.client.register-with-eureka=false
# 不检索其他服务，因为服务注册中心本身的作用就是维护服务实例，不需要去检索其他服务
eureka.client.fetch-registry=false
# 指定服务注册中心的位置-在9100的注册服务中指向9200
eureka.client.service-url.defaultZone=http://eureka9200:9200/eureka
# 如果有第三个9300则再加一个指向9300
# eureka.client.service-url.defaultZone=http://eureka9300:9300/eureka
```
`SC-03-EurekaServer-9200.src.main.resource.application.properties`
```properties
server.port=9200

# 设置该服务注册中心hostname,使用域名eureka9100
eureka.instance.hostname=eureka9200
# 防止自己注册自己
eureka.client.register-with-eureka=false
# 不检索其他服务，因为服务注册中心本身的作用就是维护服务实例，不需要去检索其他服务
eureka.client.fetch-registry=false
# 指定服务注册中心的位置-在9100的注册服务中指向9200
eureka.client.service-url.defaultZone=http://eureka9100:9100/eureka
# 如果有第三个9300则再加一个指向9300
# eureka.client.service-url.defaultZone=http://eureka9300:9300/eureka
```

3. 修改本地DNS解析文件`C\Windows\System32\dirvers\etc\hosts`
```
127.0.0.1 eureka9100
127.0.0.1 eureka9200
```

4. 启动测试两个注册中心`eureka9100:9100`和`eureka9200:9200`

5. 修改服务提供者和消费者配置
```properties
指定Eureka的访问地址
eureka.client.service-url.defaultZone=http://localhost:9100/eureka,
http://localhost:9200/eureka
```

6. 测试服务的访问`localhost:8082/test`


# 五、自我保护模式

![[Pasted image 20230708140254.png]]

相关配置

```xml
# 注册中心添加配置

# 关闭自我保护，默认值是true表示开启，false表示关闭
# 如果某服务在指定时间内不能提供心跳信息，那Eureka就会将这个服务从注册中心清除
eureka.server.enable-self-preservation=false

## ==========

# 注册中心客户端添加配置

# 每间隔2s，向服务器发送一次心跳，证明自己还活着，默认值30
eureka.instance.lease-renewal-interval-in-seconds=2

# 告诉客户端，如果当前服务10s之内还有给你发送心路，就表示我已经挂了，将我从注册中心剔除掉，默认值为90
eureka.instance.lease-expiration-duration-in-seconds=10
```


# 六、Ribbon

软件实现的客户负载均衡

![[Pasted image 20230708141641.png]]

Ribbon提供了一系列的负载均衡算法，当统一服务提供者时，集群部署就会向注册中心注册多个访问地址，消费者将根据服务名来消费服务，也会获取到多个服务的访问地址，然后这些访问地址会传递给Ribbon, Ribbon就会根据自的算法来根据消费者传递的清单列表来访问指定的服务
- Nginx是服务端负载均衡，服务清单列表会存放在Nginx服务器中
- Ribbon是客户端负载均衡，服务清列表会存放在注册中心中

## 负载均衡略

策略 | 说明
:- | :-
RandomRule | 随机
RoundRobinRule | 轮询
AvailabilityFilteringRule | 先过滤掉由多次访问故障的服务，以及并发连接数超过阈值的服务，然后对剩下的服务按照轮询策略进行访问
WeightedResponseTimeRule | 根据平均响应时间计算所有服务的权重，响应时间越快服务权重就越大被选中的概率即越高，如果服务刚启动时统计信息不足，则使用RoundRobinRue策略，待统计信息足够会切换到该WeightedResponseTimeRule策略
RetryRule | 先按照RoundRobinRule策略分发，如果分发到的服务不能访问，则在指定时间内进行重试，分发其他可用的服务
BestAvailableRule | 先过滤掉由于多次访问故障的服务，然后选择一个并发量最小的服务
ZoneAvoidanceRule | 综合判断服务节点所在区域的性能和服务节点的可用性，来决定选择哪个服务

## 实现步骤

1. 创建EurekaServer模块
- `application.properties`
```properties
server.port=9100

# 设置该服务注册中心hostname
eureka.instance.hostname=localhost
# 防止自己注册自己
eureka.client.register-with-eureka=false
# 不检索其他服务，因为服务注册中心本身的作用就是维护服务实例，不需要去检索其他服务
eureka.client.fetch-registry=false
# 指定服务注册中心的位置-在9100的注册服务中指向9200
eureka.client.service-url.defaultZone=http://${eureka.instance.hostname}$:${server.port}/eureka
# 关闭自我保护，默认值是true表示开启，false表示关闭
# 如果某服务在指定时间内不能提供心跳信息，那Eureka就会将这个服务从注册中心清除
eureka.server.enable-self-preservation=false
```

2. 创建两个服务提供者模块分别设置为
- `Provider8081`中`application.properties`
```properties
server.port=8081
# server.port=8082

# 服务名称（建议使用POM文件中的模名），两个模块中的服务名需要相同，表示这是同一个服务
spring.application.name=SC-04-Provider  
eureka.client.service-url.defaultZone=http://localhost:9100/eureka
  
# 每间隔2s，向服务器发送一次心跳，证明自己还活着，默认值30  
eureka.instance.lease-renewal-interval-in-seconds=2  
# 告诉客户端，如果当前服务10s之内还有给你发送心路，就表示我已经挂了，将我从注册中心剔除掉，默认值为90  
eureka.instance.lease-expiration-duration-in-seconds=10
```
- `controller控制器代码`
```java
package com.example.controller;

import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class ProviderController {
    @RequestMapping("/test")
    public String test() {
	    return "使用Eureka-Ribbon注册中心实现的服务提供者-8081";
        // return "使用Eureka-Ribbon注册中心实现的服务提供者-8082";
    }
}
```

3. 创建一个服务消费者模块
- `application.properties`
```properties
server.port=8080

# 服务名称（建议使用POM文件中的模名）
spring.application.name=SC-04-Consumer
eureka.client.service-url.defaultZone=http://localhost:9100/eureka

# 每间隔2s，向服务器发送一次心跳，证明自己还活着，默认值30
eureka.instance.lease-renewal-interval-in-seconds=2  
# 告诉客户端，如果当前服务10s之内还有给你发送心路，就表示我已经挂了，将我从注册中心剔除掉，默认值为90
eureka.instance.lease-expiration-duration-in-seconds=10
```
- `RestTemplate.java`配置文件
```java
package com.example.conf;

import com.netflix.loadbalancer.IRule;
// import com.netflix.loadbalancer.RandomRule;
import com.netflix.loadbalancer.RoundRobinRule;
import org.springframework.cloud.client.loadbalancer.LoadBalanced;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.client.RestTemplate;

@Configuration
public class RestTemplateConf {
    // 如果通过注册中心访问服务，必须使用LoadBalanced注解，添加Ribbon负载均衡支持
    @LoadBalanced // 交集当前的restTemplate继承Ribbon的负载均衡，默认轮询
    @Bean
    public RestTemplate getRestTemplate() {
        return new RestTemplate();
    }

    @Bean
    public IRule iRule() {
        // return new RandomRule(); // 设置为随机策略
        return new RoundRobinRule(); // 设置为轮询策略
    }
}
```
- `ConsumerController.java`控制器
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
        // 根据注册中心的服务名进行访问
        // 注意：
        // 使用注册中心服务名访问时，服务名不区分大小写
        // 使用注册中心服务名访问时，必须在RestTemplate中添加Ribbon负载衡支持
        String URL = "http://SC-04-PROVIDER/test";
        // URL：请求地址
        // String.class：请求响应返回类型
        ResponseEntity<String> response = restTemplate.getForEntity(URL, String.class);
        System.out.println(response.getStatusCode());
        System.out.println(response.getStatusCodeValue());
        System.out.println(response.getHeaders());
        String body = response.getBody();
        return "使用Eureka注册中心SpringCloud的服务消费者 -> " + body;
    }
}
```

4. 测试 
`依次启动注册中心，两个服务提供者和一个消费者，通过消费者中的test请求测试负载均衡策略效果`


# 七、Rest请求模板

## 1.  RestTemplate的GET请求

> GET请求方式常用语 - 查询数据

```java
restTemplate.getForEntity();
restTemplate.getForObject();
```

**案例**

1. 创建注册中心`SC-01-Eurekaserver`
2. 创建服务提供者`SC-01-Provider`
- `com.example.controller.ProviderController.java`
```java
@RequestMapping("/test01")
public Object test01() {
	return "服务提供者返回的String";
}

@RequestMapping("/test02")  
public User test02() {  
    return new User("Rose", 19);  
}

@RequestMapping("/test03")  
public List test03() {  
    ArrayList<User> users = new ArrayList<>();  
    users.add(new User("Jack", 19));  
    users.add(new User("Rost", 17));  
    return users;  
}
```
3. 创建服务消费者`SC-01-Consumer`
- `com.example.controller.ConsumerController.java`
```java
@RequestMapping("/test01")
public String test01() {
	String URL = "http://SC-05-PROVIDER/test01";
	ResponseEntity<String> response = restTemplate.getForEntity(URL, String.class);
	String info = response.getBody();
	return "SpringCloud消费者消费服务提供者 => " + info;
}

@RequestMapping("/test02")  
public String test02() {  
    String URL = "http://SC-05-PROVIDER/test02";  
    /*ResponseEntity<Object> response = restTemplate.getForEntity(URL, Object.class);  
    Object info = response.getBody();*/    /*ResponseEntity<Map> response = restTemplate.getForEntity(URL, Map.class);    Map info = response.getBody();*/    ResponseEntity<User> response = restTemplate.getForEntity(URL, User.class);  
    User info = response.getBody();  
    return "服务消费者test02正在消费 => " + info;  
}

@RequestMapping("/test03")  
public String test03() {  
    String URL = "http://SC-05-PROVIDER/test03";  
    // 如果服务提供者返回的类型是List集合，实际上返回的是一个Json数组  
    // 消费端接收到的List集合中元素的类型并不是服务提供者中提供的User类型，而LinkedHashMap类型  
    ResponseEntity<List> response = restTemplate.getForEntity(URL, List.class);  
    List userList = response.getBody();  
    for (Object user : userList) {  
        System.out.println(user + " <=> " + user.getClass());  
    }  
    return "服务消费者test02正在消费 => " + userList;  
}
```













