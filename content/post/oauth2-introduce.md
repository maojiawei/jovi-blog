---
title: "OAUTH2介绍"
date: 2019-10-04T13:15:47+08:00
draft: false
tags: ["OAUTH"]
categories: "microservice"
toc: true
---

&emsp;OAUTH2是一中开放授权标准，允许用户让第三方应用访问某一网站上的私密资源，而无需将网站本身的用户名和密码提供给第三方应用。

&emsp;当第三方应用需要访问网站资源的时候，OAUTH2会为其提供一个令牌（而不是用户名密码）。令牌可以授权一个特定的资源（例如:视频、网页等），第三方应用可以在特定时间段（例如:1个小时内）内访问这一资源。

## 1.0 四个重要角色
&emsp;在OAUTH2中有四个重要的角色:

- **Resource Owner:**资源拥有者。
- **Resource Server:**资源服务器。
- **Client:**第三方应用客户端。
- **Authorization Server:**授权服务器，负责管理Resource Owner，Client和Resource Server三者。

## 2.0 授权流程
![OAUTH2授权流程](../images/oauth/OAUTH2授权流程.jpg)

## 3.0 授权许可
&emsp;OAUTH2有四种授权许可方式:

- **Authorization Code:**授权码。
- **Implicit:**隐式许可。
- **Password:**资源所有者密码凭据。
- **Client:**客户端凭据。

### 3.1 授权码模式
![授权码模式](../images/oauth/授权码模式.jpg)

### 3.2 简化模式
![简化模式](../images/oauth/简化模式.jpg)

### 3.3 密码模式
![密码模式](../images/oauth/密码模式.jpg)

### 3.4 客户端模式
![客户端模式](../images/oauth/客户端模式.jpg)

## 4.0 授权许可
&emsp;当用户获取访问令牌(access_token)的同时，会获得一个过期时间和刷新令牌。刷新令牌可以允许第三方应用在令牌过期后自动获取新的访问令牌，而不需要在重新认证一次。

&emsp;令牌刷新请求参数一般如下:

- **grant_type:**必填，值为refresh_token
- **refresh_token:**必填，值为获取访问令牌(access_token)同时获得的刷新令牌(refresh_token)。

&emsp;令牌刷新返回参数一般如下:

- **access_token:**新的访问令牌。
- **token_type:**令牌类型。
- **expires_in:**过期时间。
- **refresh_token:**刷新令牌。

## 5.0 相关资料
**OAuth2授权** <https://www.cnblogs.com/linianhui/p/oauth2-authorization.html#auto_id_0> 