---
title: Dubbo-整合Hystrix
date: 2021-08-05 00:00:00
author: 神奇的荣荣
summary: ""
categories: Dubbo
tags: 
    - Dubbo
    - 分布式
---

# Hystrix 断路器

## 服务降级

服务降级概念：  
当服务器压力剧增的情况下，根据当前业务情况及流量对一些服务和页面有策略的降级（返回一个友好提示给客户端，不去执行主业务逻辑，调用fallBack本地方法），以此释放服务器资源以保证核心任务的正常运行。

<!-- more -->

## 服务熔断

服务熔断概念：  
我们在各种场景下都会接触到熔断这两个字。高压电路中，如果某个地方的电压过高，熔断器就会熔断，对电路进行保护。股票交易中，如果股票指数过高，也会采用熔断机制，暂停股票的交易。

同样，在微服务架构中，熔断机制也是起着类似的作用。当扇出链路的某个微服务调用慢或者有大量超时，会进行服务的降级，进而熔断该节点微服务的调用，快速返回错误的响应信息。当检测到该节点微服务调用响应正常后，恢复调用链路。

服务熔断机制和服务降级是一起使用。

## Hystrix简介

Hystrix 旨在通过控制那些访问远程系统、服务和第三方库的节点，从而对延迟和故障提供更强大的容错能力。Hystrix具备拥有回退机制和断路器功能的线程和信号隔离，请求缓存和请求打包，以及监控和配置等功能。

## 整合Hystrix

当前整合是基于 spring boot + dubbo的案例；（详情看Dubbo-基础）

### 提供者改造

1）导入hystrix依赖（spring boot官方提供了对hystrix的集成）  
直接在pom.xml里加入依赖：  
```xml
    <!-- hystrix -->
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
        <version>2.2.9.RELEASE</version>
    </dependency>
```
注意：需要找到版本合适的netflix导入（springboot2.2.4.RELEASE & hystrix2.2.9.RELEASE & dubbo2.7.7）

2）修改代码  
修改userServiceImpl  
```java
@DubboService
public class UserServiceImpl implements UserService {

    public final static Map<String, List<UserAddressDto>> map;

    static {
        List<UserAddressDto> list1 = Stream.of(new UserAddressDto(1, "1", "江西赣州", "欧阳", "152*****", false),
                new UserAddressDto(2, "1", "广东深圳", "欧阳", "152*****", true),
                new UserAddressDto(1, "1", "浙江上海", "欧阳", "152*****", false))
                .collect(Collectors.toList());
        List<UserAddressDto> list2 = Stream.of(new UserAddressDto(3, "2", "江西赣州", "东华", "152*****", false),
                new UserAddressDto(4, "2", "广东深圳", "东华", "152*****", true),
                new UserAddressDto(5, "2", "浙江上海", "东华", "152*****", false))
                .collect(Collectors.toList());

        List<UserAddressDto> list3 = Stream.of(new UserAddressDto(6, "3", "江西赣州", "异常用户", "152*****", false))
                .collect(Collectors.toList());
        map = new HashMap<>();
        map.put("1", list1);
        map.put("2", list2);
        map.put("3", list3);
    }

    // 方法出现异常后，调用其他方法进行返回
    @HystrixCommand(fallbackMethod = "getUserAddressError")
    @Override
    public List<UserAddressDto> getUserAddress(String userId) {
        if (Math.random() > 0.5) {
            throw new RuntimeException("错误的参数");
        }
        return map.get(userId);
    }

    private List<UserAddressDto> getUserAddressError(String userId) {
        return map.get("3");
    }
}
```

3）开启hystrix  
在Application类上增加@EnableHystrix来开启：
```java
// 开启Hystrix，进行代理
@EnableHystrix
// 开启Dubbo，扫描Dubbo注解（扫描当前包和子包内容）
@EnableDubbo
@SpringBootApplication
public class Provider {

    public static void main(String[] args) {
        SpringApplication.run(Provider.class, args);
    }

}
```

### 消费者改造

当前是在服务提供者做熔断，所以消费者不需要改造。
（如果需要在消费者中做熔断，改造步骤和提供者一致）

### 启动查看效果

启动提供者和消费者，访问：http://localhost:8080/dubbo

正常访问：  
![服务熔断正常访问](https://rong0624.gitee.io/images/Dubbo/1627894884676.jpg)

异常访问：  
![服务熔断异常访问](https://rong0624.gitee.io/images/Dubbo/1627894974462.jpg)

结论：Hystrix有强大的容错能力，无论是超时还是错误，都可以调用备用方法返回。