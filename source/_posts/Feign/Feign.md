---
title: Feign
date: 2021-06-20 00:00:00
author: 神奇的荣荣
summary: ""
categories: HTTP 客户端
tags: Feign
---

# 简介

Feign使得 Java HTTP 客户端编写更方便。Feign 灵感来源于Retrofit、JAXRS-2.0和WebSocket。  
Feign最初是为了降低统一绑定Denominator到HTTP API的复杂度，不区分是否支持Restful。  
Feign旨在通过最少的资源和代码来实现和HTTP API的连接。通过可定制的解码器和错误处理，可以编写任意的HTTP API。

## 为什么选择Feign?

你可以使用 Jersey 和 CXF 这些来写一个 Rest 或 SOAP 服务的java客服端。  
你也可以直接使用 Apache HttpClient 来实现。  
但是 Feign 的目的是尽量的减少资源和代码来实现和 HTTP API 的连接。通过自定义的编码解码器以及错误处理，你可以编写任何基于文本的 HTTP API。

## Feign工作机制

Feign通过注解注入一个模板化请求进行工作。只需在发送之前关闭它，参数就可以被直接的运用到模板中。  
然而这也限制了Feign，只支持文本形式的API，它在响应请求等方面极大的简化了系统。同时，它也是十分容易进行单元测试的。

## 版本兼容性

Feign 10.x 及以上版本基于 Java 8 构建，应该适用于 Java 9、10 和 11。
对于需要兼容 JDK 6 的用户，请使用 Feign 9.x

## 功能概述

这是一张包含 feign 提供的当前关键功能的地图：  
![feign功能概述](https://rong0624.github.io/images/Feign/)

# Feign使用简介

## 