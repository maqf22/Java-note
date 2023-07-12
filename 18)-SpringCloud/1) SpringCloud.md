
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

@RequestMapping("/test04")  
public User test04(String name, Integer age) {  
    return new User(name, age);  
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
    return "服务消费者test03正在消费 => " + userList;  
}

@RequestMapping("/test04")  
public String test04(User user) {  
    // 方法一  
    /*String URL = "http://SC-05-PROVIDER/test04?name={0}&age={1}";  
    ResponseEntity<User> response = restTemplate.getForEntity(URL, User.class, user.getName(), user.getAge());*/  
    // 方法二  
    String URL = "http://SC-05-PROVIDER/test04?name={name}&age={age}";  
    HashMap<String, Object> args = new HashMap<>();  
    args.put("name", user.getName());  
    args.put("age", user.getAge());  
    ResponseEntity<User> response = restTemplate.getForEntity(URL, User.class, args);  
  
    User info = response.getBody();  
    return "服务消费者test04正在消费 => " + info;  
}

@RequestMapping("/test05")  
public String test05() {  
    String URL = "http://SC-05-PROVIDER/test02";  
    User user = restTemplate.getForObject(URL, User.class);  
    return "服务消费者test05正在消费 => " + user;  
}
```

## 2. RestTemplate的POST请求

> POST请求方式常用语 - 插入数据
> 注意：POST的请求效率要低于GET请求方式，除非服务提供者指定的POST提交方式，否则能不用尽量不用

POST与GET请求方式类似
```java
restTemplate.postForObject();
restTemplate.postForEntity();
restTemplate.postForLocation();
```

**案例**

- `ProviderController.java`
```java
@PostMapping("/test06")  
public User test06(String name, Integer age) {  
    return new User(name, age);  
}
```

- `ConsumerController.java`
```java
@RequestMapping("/test06")
    public String test06(User user) {
        /*String URL = "http://SC-05-PROVIDER/test06?name={name}&age={age}";
        HashMap<String, Object> args = new HashMap<>();
        args.put("name", user.getName());
        args.put("age", user.getAge());
        User resUser = restTemplate.postForObject(URL, Object.class, User.class, args);*/

        String URL = "http://SC-05-PROVIDER/test06";
        LinkedMultiValueMap<String, Object> lmvm = new LinkedMultiValueMap<>();
        lmvm.add("name", user.getName());
        lmvm.add("age", user.getAge());
        User resUser = restTemplate.postForObject(URL, lmvm, User.class);
        return "User => " + resUser;
    }
```

## 3. RestTemplate的PUT请求

> PUT请求方式常用语 - 修改数据

```java
restTemplate.put();
```

**案例**

- `ProviderController.java`
```java
@PutMapping("/test07")  
public void test07(String name, Integer age) {  
    System.out.println("name = " + name + ", age = " + age);  
}
```

- `ConsumerController.java`
```java
@RequestMapping("/test07")
public String test07(User user) {
	/*String URL = "http://SC-05-PROVIDER/test07?name={name}&age={age}";
	HashMap<String, Object> args = new HashMap<>();
	args.put("name", user.getName());
	args.put("age", user.getAge());*/

	String URL = "http://SC-05-PROVIDER/test07";
	LinkedMultiValueMap<String, Object> args = new LinkedMultiValueMap<>();
	args.add("name", user.getName());
	args.add("age", user.getAge());
	restTemplate.put(URL, args);
	return "Success put";
}
```

## 4. RestTemplate的DELETE请求

> DELETE请求常用语 -删除数据

```java
restTemplate.delete();
```

**案例**

- `ProviderController.java`
```java
@DeleteMapping("/test08")
public void test08(String name, Integer age) {
	System.out.println("name = " + name + ", age = " + age);
}
```

- `ConsumerController.java`
```java
@RequestMapping("/test08")
public String test08(User user) {
	/*String URL = "http://SC-05-PROVIDER/test08?name={0}&age={1}";
	restTemplate.delete(URL, user.getName(), user.getAge());*/

	String URL = "http://SC-05-PROVIDER/test08?name={name}&age={age}";
	HashMap<String, Object> args = new HashMap<>();
	args.put("name", user.getName());
	args.put("age", user.getAge());
	restTemplate.delete(URL, args);
	return "Success Delete";
}
```


# 八、服务熔断Hystrix

> 场景：假设A服务莫名其妙出现延迟了，由于A服务延迟，那么假设B服务调用了A服务，那么B服务也会跟着延迟，如果这时出现大量的访问，就会形成**请求堆积**卡住不动了。那么这个时候用户的访问就会是无响应的状态，如果这个时候还有其他服务，比如C、D服务都想访问A或者B服务中的某一个，依然是继续堆积，然后这些堆积的请求将会被挂起。这个时候就会出现**故障蔓延**最终导致整个分布式的系统全部瘫痪。
> 处理：服务熔断（保险丝）就是用来解决上述问题。

## 1. 快速上手

1. 服务消费者`pom.xml`中添加依赖
```xml
<dependency>
	<groupId>org.springframework.cloud</groupId>
	<artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
</dependency>
```

2. 引导类中添加注解
```java
package com.example;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.SpringCloudApplication;
import org.springframework.cloud.client.circuitbreaker.EnableCircuitBreaker;
import org.springframework.cloud.netflix.eureka.EnableEurekaClient;


// Generated by https://start.springboot.io
// 优质的 spring/boot/data/security/cloud 框架中文文档尽在 => https://springdoc.cn

@SpringBootApplication
@EnableEurekaClient
@EnableCircuitBreaker // 表示邀活hystrix熔断
// 该注解表示这是一个SpringCloud项目，所以同时包含以上三个注解
// @SpringCloudApplication
public class Sc06ConsumerApplication {

    public static void main(String[] args) {
        SpringApplication.run(Sc06ConsumerApplication.class, args);
    }

}
```

3. 服务消费者中，相关请求服务测试
```java
package com.example.controller;

import com.netflix.hystrix.contrib.javanica.annotation.HystrixCommand;
import com.netflix.hystrix.contrib.javanica.annotation.HystrixProperty;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.client.RestTemplate;

import javax.annotation.Resource;

@RestController
public class ConsumerController {
    @Resource
    private RestTemplate restTemplate;

    public String error() {
        return "Provider Error!";
    }
    // 设置请求方法的熔断，当该服务调用的其他服务出现异常或超时时，调用指定的回调函数
    // @HystrixProperty 中 name 为属性名，value 为属性值（默认延迟时间为1s）
    @HystrixCommand(fallbackMethod = "error", commandProperties = {
            @HystrixProperty(
                    name="execution.isolation.thread.timeoutInMilliseconds",
                    value="3000"
            )
    })
    /*如果消费远程服务的时候出现了问题，也会导致当前的服务调用出现问题
    就会执行fallbackMethod对应的毁掉方法，实现服务的降级
    用fallbackMethod指向的方法，来替换当前要访问的服务*/
    @RequestMapping("/test")
    public String test() {
        String URL = "http://SC-06-PROVIDER/test";
        String res = restTemplate.getForObject(URL, String.class);
        return "Consumer => " + res;
    }
}
```

4. 服务提供者中制造异常或者延迟
```java
package com.example.controller;

import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class ProviderController {
    @RequestMapping("/test")
    public String test() {
        // System.out.println(1/0);
        try {
            Thread.sleep(10000);
        } catch (InterruptedException e) {
            throw new RuntimeException(e);
        }
        return "Provider test() info~";
    }
}
```

5. 访问服务消费者test请求进行测试

## 2. Hystrix的异常处理

### 1) 捕获异常

```java
// 可以通过 throwable获取异常相关的信息
// 如果是远程的服务提供者出现了内部错误，捕获到的异常类型为 HttpServerErrorException$InternalServerError
// 如果是因为超时，得到的异常类型为 HystrixTimeoutException
// 如果是当前服务中有异常出现，将会得到具体的异常信息，如：空指针异常、除零错误等...
// 注意：远程服务提供者中的具体服务中的异常不能精确的获取到
public String error(Throwable throwable) {
	System.out.println(throwable.getClass());
	System.out.println(throwable.getMessage());
	return "Provider Error!";
}
```

### 2) 忽略异常信息

```java
/*ignoreExceptions 属性为表示为要忽略掉的异常类型，值为class类型的数组
其中要忽略掉的异常类型必须为Throwable 或其子类类型
如果忽回复了指定的某个类型的异常之后，如果出现了这个异常，将不触发熔断，直接抛出异常给用户
抛出异常的规则，遵循捕获异常的规则*/
@HystrixCommand(fallbackMethod = "error",ignoreExceptions = {NullPointerException.class})
@RequestMapping("/test")
public String test() {
	String str = null;
	System.out.println(str.length());
	String URL = "http://SC-06-PROVIDER/test";
	String res = restTemplate.getForObject(URL, String.class);
	return "Consumer => " + res;
}
```

### 3) 自定义异常熔断器

1. 自定义一个远程服务熔断器
```java
package com.example.hystrix;

import com.netflix.hystrix.HystrixCommand;
import com.netflix.hystrix.HystrixCommandGroupKey;
import org.springframework.web.client.RestTemplate;

public class MyHystrixCommand extends HystrixCommand<String> {

    private String url;
    private RestTemplate restTemplate;

    // 定义构造方法，通过参数初始化设置
    public MyHystrixCommand (String url, RestTemplate restTemplate) {
        this(HystrixCommand.Setter.withGroupKey(HystrixCommandGroupKey.Factory.asKey("")));
        this.url = url;
        this.restTemplate = restTemplate;
    }

    // 此构造方法的参数为熔断器的设置对象
    protected MyHystrixCommand(Setter setter) {
        super(setter);
    }

    // 这个方法来自于抽象父类，这个方法用于访问远程服务提供者，不支持手动调用
    @Override
    protected String run() throws Exception {
        // return null;
        return restTemplate.getForObject(url, String.class);
    }

    // 自定义服务降级方法，同样来自于抽象父类
    // 如果 run 方法执行的时候出现异常，将自动执行这个服务的降级方法进行熔断处理
    // return 返回值用于替代真实的返回信息
    @Override
    protected String getFallback() {
        return "something error~";
    }
}
```

2. 定义远程服务提供者
```java
@RestController
public class ProviderController {
    @RequestMapping("/test")
    public String test() {
        System.out.println(1/0);
        /*try {
            Thread.sleep(10000);
        } catch (InterruptedException e) {
            throw new RuntimeException(e);
        }*/
        return "Provider test() info~";
    }
}
```

3. 定义服务消费者
```java
@RequestMapping("/test01")
public Object test01() {
	String URL = "http://SC-06-PROVIDER/test";
	// 调用自定义熔断器的构造方法，设置熔断器
	MyHystrixCommand myHystrixCommand = new MyHystrixCommand(URL, restTemplate);
	String info = myHystrixCommand.execute();
	return "服务消费者 => " + info;
}
```

## 3. Hystrix仪表盘监控

> 用于试试监控拥有Hystrix服务的健康状态

1. 创建工程选择web起步依赖，之后在pom.xml中添加仪表盘坐标
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.3.12.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>

    <groupId>com.example</groupId>
    <artifactId>SC-06-HystrixDashboard</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>SC-06-HystrixDashboard</name>
    <description>SC-06-HystrixDashboard</description>
    <properties>
        <java.version>1.8</java.version>
        <spring-cloud.version>Hoxton.SR11</spring-cloud.version>
    </properties>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        
        <!-- 在依赖中添加仪表盘相关依赖 -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-hystrix-dashboard</artifactId>
        </dependency>
        
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <!-- 添加SpringCloud依赖管理 -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>${spring-cloud.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>
```

2. 在引导类中激活仪表盘功能
```java
@EnableHystrixDashboard
```

3. application.properties配置文件中设置端口号，以及Hystrix允许列表
```properties
server.port=9527
hystrix.dashboard.proxy-stream-allow-list=*
```

4. 启动工程并访问`localhost:9527/hystrix`
![[Pasted image 20230711142244.png]]

5. 在要监控的服务中添加健康检测依赖
```xml
<!-- 健康检查机制依赖 -->
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

6. 在要监控的服务中设置健康检测配置
```properties
management.endpoints.web.exposure.include=*  
#management.endpoints.web.exposure.include=hystrix.stream
```

7. 在要监控的服务中心打开健康检测中心 `localhost:8081/actuator`
![[Pasted image 20230711143807.png]]

8. 访问触发熔断查看效果
9. 仪表盘内容解读
![[Pasted image 20230711144057.png]]


# 九、Feign声明式消费

## 1. 快速上手

1. 准备工作

- 分别准备好一个注册中心，添加起步依赖：web / eureka-server
- 一个服务提供者，添加起步依赖：web / eureka-client
- 一个服务消费者，添加起步依赖：openfeign / web / eureka-client

```xml
<!-- feign依赖 -->
<dependency>
	<groupId>org.springframework.cloud</groupId>
	<artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
```

2. 在服务提供者中添加控制器代码
```java
package com.example.controller;

import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class ProviderController {
    @RequestMapping("/test")
    public String test() {
        return "Provider info~";
    }
}
```

3. 在服务消费者模块中添加接口类 `com.example.service.TestService.java`
```java
package com.example.service;

import org.springframework.cloud.openfeign.FeignClient;
import org.springframework.web.bind.annotation.RequestMapping;

// name/value属性用于指向某个需要访问的服务
@FeignClient(name = "SC-07-PROVIDER")
public interface TestService {
    /*这里的 /test 表示具体访问的远程服务的访问路径，也就是 provider 中的 test
    返回值是远程服务返回的数据封装对象，根据实际情况而定*/
    @RequestMapping("/test")
    String test();
}
```

4. 在服务消费者模块中添加控制器 `com.example.controller.ConsumerController.java`
```java
package com.example.controller;

import com.example.service.TestService;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import javax.annotation.Resource;

@RestController
public class ConsumerController {
    @Resource
    private TestService testService;
    @RequestMapping("/test")
    public String test() {
        String info = testService.test();
        return "服务消费者 test 正在消费 => " + info;
    }
}
```

5.  在服务消费者引导类中添加激活注解
```java
@EnableFeignClients // 激活FeignClients
```

6. 访问服务消费者链接进行测试

## 2. 返回实体类

1. 分别在服务消费者和提供者两端创建实体类User
- `com.example.domain.User.java`
```java
package com.example.domain;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;
import lombok.ToString;

@Data
@AllArgsConstructor
@NoArgsConstructor
@ToString
public class User {
    private String name;
    private Integer age;
}
```

2. 服务提供者
- `com.example.controller.ProviderController.java`
```java
@RequestMapping("/test02")  
public User test02() {  
    return new User("Jack", 29);  
}
```

3. 服务消费者
- `com.example.service.TestService.java`
```java
package com.example.service;  
  
import com.example.domain.User;  
import org.springframework.cloud.openfeign.FeignClient;  
import org.springframework.web.bind.annotation.RequestMapping;  
  
// name/value属性用于指向某个需要访问的服务  
@FeignClient(name = "SC-07-PROVIDER")  
public interface TestService {  
    /*这里的 /test 表示具体访问的远程服务的访问路径，也就是 provider 中的 test    返回值是远程服务返回的数据封装对象，根据实际情况而定*/  
    @RequestMapping("/test")  
    String test();  
  
    @RequestMapping("/test02")  
    User test02();  
}
```
- `com.example.controller.ConsumerController.java`
```java
@RequestMapping("/test02")
public String test02() {
	User user = testService.test02();
	return "服务消费者 test02 正在消费 => " + user;
}
```

4. 测试`localhost:8080/test02`

## 3. 返回List集合

1. 服务提供者
```java
@RequestMapping("/test03")
public List<User> test03() {
	ArrayList<User> userList = new ArrayList<>();
	userList.add(new User("Jack", 29));
	userList.add(new User("Rose", 28));
	return userList;
}
```

2. 服务消费者
- `com.example.service.TestService.java`
```java
package com.example.service;

import com.example.domain.User;
import org.springframework.cloud.openfeign.FeignClient;
import org.springframework.web.bind.annotation.RequestMapping;

import java.util.List;

// name/value属性用于指向某个需要访问的服务
@FeignClient(name = "SC-07-PROVIDER")
public interface TestService {
    /*这里的 /test 表示具体访问的远程服务的访问路径，也就是 provider 中的 test
    返回值是远程服务返回的数据封装对象，根据实际情况而定*/
    @RequestMapping("/test")
    String test();

    @RequestMapping("/test02")
    User test02();

    @RequestMapping("/test03")
    List<User> test03();
}
```
- `com.example.controller.ConsumerController.java`
```java
@RequestMapping("/test03")
public String test03() {
	List<User> userList = testService.test03();
	return "服务消费者 test02 正在消费 => " + userList;
}
```

3. 访问服务消费者测试 `localhost:8080/test03`

## 4. 传递参数

### 1) 普通参数传递

1. 服务提供者
```java
@RequestMapping("/test04")
public User test04(String name, Integer age) {
	return new User(name, age);
}
```

2. 服务消费者
- `com.example.service.TestService.java`
```java
@RequestMapping("/test04")
User test04(@RequestParam("name") String name, @RequestParam("age") Integer age);
```
- `com.example.controller.ConsumerController.java`
```java
@RequestMapping("/test04")
public String test04(String name, Integer age) {
	User user = testService.test04(name, age);
	return "服务消费者 test02 正在消费 => " + user;
}
```

3. 访问服务消费者测试 `http://localhost:8080/test04?name=Jack&age=22`

### 2) 实体类参数传递 

1. 服务提供者
```java
@RequestMapping("/test05")  
public User test05(@RequestBody User user) {  
    return user;  
}
```

2. 服务的消费者
- `com.example.service.TestService.java`
```java
/*@RequestBody 用于标记当前形参为请求体，也就是说这个参数是请求的数据封装对象
如果服务提供者也需要获取到这个数据
那么服务提供者的控制器方法也必须使用一个实体类或Map集合来接收数据
并且也要标记 @RequestBody*/
@RequestMapping("/test05")
User test05(@RequestBody User user);
```
- `com.example.controller.ConsumerController.java`
```java
@RequestMapping("/test05")
public String test05(User user) {
	User resUser = testService.test05(user);
	return "服务消费者 test02 正在消费 => " + resUser;
}
```

3. 访问测试 `http://localhost:8080/test05?name=Jack&age=22`

## 5. 负载均衡

> 参考Ribbon负载均衡策略的调整。

##  6. 服务熔断

1. 服务消费者application.properties
```properties
# 开启Feign对于Hystrix服务熔断的支持true表示开启，默认false表示关闭  
feign.hystrix.enabled=true
```

2. `com.example.service.TestService.java`类注解上添加fallback 属性
```java
// name/value属性用于指向某个需要访问的服务
// fallback用于指定熔器类的class，如果是远程调用服务时出现异常则触发熔断器
// 如果需要当前服务中的某个控制器方法进行熔断，仍然需要配置之前的Hystrix
@FeignClient(name = "SC-07-PROVIDER", fallback = MyHystrix.class)
// 或
@FeignClient(name = "SC-07-PROVIDER", fallbackFactory = MyHystrix.class)
```

3. 自定义`com.example.hystrix.MyHystrix.java`类并实现TestService接口
```java
package com.example.hystrix;

import com.example.domain.User;
import com.example.service.TestService;
import org.springframework.stereotype.Component;

import java.util.List;

@Component
public class MyHystrix implements TestService {
    @Override
    public String test() {
        return "test() is down~";
    }

    @Override
    public User test02() {
        return null;
    }

    @Override
    public List<User> test03() {
        return null;
    }

    @Override
    public User test04(String name, Integer age) {
        return null;
    }

    @Override
    public User test05(User user) {
        return null;
    }
}

// ==========

// fallbackFactory写法
@Component  
public class MyHystrixFactory implements FallbackFactory<TestService> {  
  
    @Override  
    public TestService create(Throwable throwable) {  
        return new TestService() {  
            @Override  
            public String test() {  
                return "test() is down~";  
            }  
  
            @Override  
            public User test02() {  
                return null;  
            }  
  
            @Override  
            public List<User> test03() {  
                return null;  
            }  
  
            @Override  
            public User test04(String name, Integer age) {  
                return null;  
            }  
  
            @Override  
            public User test05(User user) {  
                return null;  
            }  
        };  
    }  
}
```

4. 在服务提供者的test中制造异常测试熔断 `http://localhost:8080/test`


# 十、API网关Zuul

![[Pasted image 20230711180240.png]]

> 帮助我们过滤请求，帮助我们过滤服务，相当于是一个安全检查站，所有的外部请求都需要经过它调度和过滤，然后API网关来实现请求路由、负载均衡、权限验证等功能。

## 1. 快速上手

1. 创建：EurekaServer、EurekaClientProvider、EurekaClientConsumer三个模块（要求能通过EurekaClientConsumer访问EurekaClientProvider中的远程服务）

2. 创建一个EurekaClientZullGateway工程，并添加Web/EurekaClient/Zuul起步依赖
```xml
<!-- Zuul API 网关的起步依赖 -->  
<dependency>  
    <groupId>org.springframework.cloud</groupId>  
    <artifactId>spring-cloud-starter-netflix-zuul</artifactId>  
</dependency>
```

3. 引导类中添加激活注解
```java
package com.example;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.EnableEurekaClient;
import org.springframework.cloud.netflix.zuul.EnableZuulProxy;


// Generated by https://start.springboot.io
// 优质的 spring/boot/data/security/cloud 框架中文文档尽在 => https://springdoc.cn

@SpringBootApplication
@EnableEurekaClient
@EnableZuulProxy // 激活API网关功能
public class Sc08ZuulGatewayApplication {
    public static void main(String[] args) {
        SpringApplication.run(Sc08ZuulGatewayApplication.class, args);
    }
}
```

4. application.properties中添加配置
```properties
server.port=9527
spring.application.name=SC-08-ZuulGateway
eureka.client.service-url.defaultZone=http://localhost:9100/eureka

# 配置路由规则 斜体部分可以自定义
# /api-zuul/** 表示请求拦截规则
#zuul.routes.api-zull.path=/api-zuul/**
# 上面的规则对应的服务
#zuul.routes.api-zull.service-id=SC-08-Provider

# 等价于上面两行
zuul.routes.SC-08-Provider=/api-zuul/**
```

5. 启动服务并访问测试`localhost:9527/api-zuul/test`

6. 在服务消费者中测试
- `com.example.service.TestService.java`
```java
package com.example.service;

import org.springframework.cloud.openfeign.FeignClient;
import org.springframework.web.bind.annotation.RequestMapping;

// 使用API网关路由地址
@FeignClient(name = "SC-08-ZuulGateway/api-zuul")
public interface TestService {
    @RequestMapping("/test")
    String test();
}
```

7. 访问`localhost:8080/test`测试

## 2. 请求过滤

![[Pasted image 20230712111645.png]]

**权限认证操作**

1. 创建并编写自定义网关过滤器类`com.example.filter.AuthFilter.java`
```java
package com.example.filter;

import com.netflix.zuul.ZuulFilter;
import com.netflix.zuul.context.RequestContext;
import com.netflix.zuul.exception.ZuulException;
import org.springframework.stereotype.Component;

import javax.servlet.http.HttpServletRequest;

@Component // 将自定义过滤器类放到Spring容器中
public class AuthFilter extends ZuulFilter {
    @Override
    public String filterType() {
        // 返回过滤器类型
        /*
        pre: 前置过滤器
        routing: 将请求路由到微服输，用于构建发送给微服的请求
        post: 后置过滤器
        error: 错误过滤器
        */
        return "pre";
    }

    @Override
    public int filterOrder() {
        // 过滤器优先级
        // 返回的数值越小，表示优先级越高
        return 0;
    }

    @Override
    public boolean shouldFilter() {
        // 过滤器开关，true表示开启，false表示关闭
        return true;
    }

    @Override
    public Object run() throws ZuulException {
        // 过滤的具体动作，做权限的验证

        // 获取请求上下文对象
        RequestContext context = RequestContext.getCurrentContext();
        // 通过上下文对象获取请求对象
        HttpServletRequest request = context.getRequest();
        // 通过请求对象获取请求参数，假设 token 作为权限的认证
        String token = request.getParameter("token");

        if (!"9527".equals(token)) {
            // 不允许向下继续访问，拦截住了，立即返回
            context.setSendZuulResponse(false);
            // 设置响应头信息
            context.addZuulResponseHeader("Content-Type", "text/html;charset=UTF-8");
            // 设置响应数据
            context.setResponseBody("权限不足~");
        } else {
            System.out.println("Pass~");
        }

        return null;
    }
}
```

2. 测试访问
`localhost:9527/api-zuul/test`
`localhost:9527/api-zuul/test?token=9527`

## 3. 过滤规则

`application.properties`
```properties
# 忽略请求，相当于黑名单，不允许访问404  
# 请求字符中支持通配符  
# ? 表示单个字符  
# * 表示任意字符，但是不包含目录结构  
# ** 表示任意字符，同时包含目录结构  
zuul.ignored-patterns=/api-zuul/test02

# 添加统一的访问前缀  
zuul.prefix=/example

# 配置自我转发  
# 网关也是一个服务，它本身也可以出来一些请求  
# 如果需要网关工程助理请求，必须设置自我转发  
zuul.routes.gateway.path=/gateway/**  
zuul.routes.gateway.url=forward:/api/local
```

`controller.GatewayController.java`
```java
package com.example.controller;

import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class GatewayController {
    @RequestMapping("/api/local")
    public String local() {
        return "API网关的内置控制器方法";
    }
}
```

测试`http://localhost:9527/gateway`

## 4. 异常处理

### 1) 自定义异常过滤器

1. `com.example.filter.ErrorFilter.java`自定义一个异常过滤器类
```java
package com.example.filter;

import com.netflix.zuul.ZuulFilter;
import com.netflix.zuul.context.RequestContext;
import com.netflix.zuul.exception.ZuulException;
import org.springframework.http.RequestEntity;
import org.springframework.stereotype.Component;

import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.PrintWriter;

@Component
public class ErrorFilter extends ZuulFilter {
    @Override
    public String filterType() {
        return "error";
    }

    @Override
    public int filterOrder() {
        return 1;
    }

    @Override
    public boolean shouldFilter() {
        return true;
    }

    @Override
    public Object run() throws ZuulException {
        // 异常过滤片时
        // 获取请求上下文对象
        RequestContext context = RequestContext.getCurrentContext();
        // 获取响应对象
        HttpServletResponse response = context.getResponse();
        // 获取异常
        ZuulException exception = (ZuulException) context.getThrowable();
        // 设置响应头
        response.setHeader("Content-Type", "text/html;charset=UTF-8");
        // 设置响应状态
        response.setStatus(exception.nStatusCode);
        // 异常信息
        System.out.println(exception.getMessage());
        // 关联异常信息
        System.out.println(exception.getCause().getMessage());
        // 出输点内容
        try {
            PrintWriter writer = response.getWriter();
            writer.print("Gateway Error~");
        } catch (IOException e) {
            e.printStackTrace();
        }
        return null;
    }
}
```

2. 修改配置文件，屏敝系统自带异常处理器
```properties
# 禁用默认异常处理器  
# 全局错误页面需要默认异常处理器依赖，所以要使用全局页错误，就不能使用自定义异常类  
zuul.SendErrorFilter.error.disable=true
```

3. 在其他过滤器中制造一个异常
```java
System.out.println(1 /0 );
```

4. 访问任意控制器进行测试

### 2) 自定义全局错误页

1. 定义自定义错误控制器`com.example.controller.MyErrorHandlerController.java`
```java
package com.example.controller;  
  
import com.netflix.zuul.context.RequestContext;  
import com.netflix.zuul.exception.ZuulException;  
import org.springframework.boot.web.servlet.error.ErrorController;  
import org.springframework.web.bind.annotation.RequestMapping;  
import org.springframework.web.bind.annotation.RestController;  
  
@RestController  
public class MyErrorHandlerController implements ErrorController {  
    @Override  
    public String getErrorPath() {  
        return "/error";  
    }  
  
    @RequestMapping("/error")  
    public String error() {  
        ZuulException e = (ZuulException) RequestContext.getCurrentContext().getThrowable();  
        return "全局页错误 => " + e.getCause().getMessage();  
    }  
}
```

2. 启动默认异常处理器
```properties
# 禁用默认异常处理器  
# 全局错误页面需要默认异常处理器依赖，所以要使用全局页错误，就不能使用自定义异常类  
#zuul.SendErrorFilter.error.disable=true
```

3. 访问并测试 `http://localhost:9527/api-zuul/test`























