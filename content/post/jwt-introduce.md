---
title: "JWT简介"
date: 2019-09-25T13:44:27+08:00
draft: true
tags: ["JWT","OAUTH"]
categories: "microservice"
toc: false
---
&emsp;JWT(JSON Web Token)是一种互联网开放标准(RFC7519)的访问令牌，主要基于JSON格式。这种TOKEN特色在于紧凑(compact)、URL安全(URL-safe)，它主要用于Web浏览器单点登录（SSO）。

### 1.0 用途
&emsp;目前,JWT主要用于鉴权。当用户通过用户名及密码进行登录并验证通过后，JWT 令牌将生成并返回。客户端保存令牌，通常保存在local storage或session storage或者cookies（推荐）。

&emsp;当客户端想要访问被保护的资源时，可以将保存的JWT令牌在http头部（header）的`Authorization` 传递过来。服务端验证TOKEN的有效性后，授权访问。

### 2.0 结构

&emsp;在JWT中，由符号`.`作为分割，分为三个部分。它们分别是:

- Header
- Payload
- Signature

&emsp;JWT还会对三个部分分别进行加密,JWT的格式如下:

```
aaaaa.bbbbb.ccccc
例如:
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJsb2dnZWRJbkFzIjoiYWRtaW4iLCJpYXQiOjE0MjI3Nzk2Mzh9.gzSraSYS8EXBxLN_oWnFSRgCzcmJmMjLiuyu5CSpyHI
```

#### 2.1 Header

&emsp;这部分表明了签名的生成算法,如下为第一部分Header的范例。`alg`主要申明加密的算法（通常直接使用HMAC SHA256），typ主要申明类型。

```json
{
 "alg" : "HS256",
 "typ" : "JWT"
}
```

&emsp;在对以上进行内容进行BASE64加密后的结果为eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9 。这就构成了第一部分。

#### 2.2 Payload

&emsp;Payload(载荷)包含了一系列的声明。他包含三个部分:

- 标准中注册的声明
- 公共声明
- 私有声明

&emsp;`标准中注册的声明`包含以下几个部分(建议但并不强制):

| 列名 | 名称          | 描述                                                         |
| ---- | ------------- | ------------------------------------------------------------ |
| iss  | 发行者        | 声明了该JWT令牌的发行者                                      |
| sub  | jwt面向的用户 | 声明了该JWT令牌面向的用户                                    |
| aud  | 接收jwt的一方 | 声明了该JWT令牌对应的接收方。                                |
| exp  | 过期时间      | 声明了什么时候开始该JWT不可以再有效。该值必须是数字型日期，例如:1422779638。 |
| nbf  | 生效时间      | 声明了什么时候开始该JWT可以生效。该值必须是数字型日期，例如:1422779638。 |
| iat  | 签发时间      | 声明了JWT的签发时间。该值必须是数字型日期，例如:1422779638。 |
| jti  | JWT的唯一标识 | JWT令牌的唯一标识符（区分大小写），即使不同的发行者之间也是如此。 |

&emsp;`公共声明`可以添加任何信息，一般是添加用户相关信息或其他业务需要的必要信息。但不建议添加敏感信息，因为在该部分在客户端可以解密。

&emsp;`私有声明`是提供者和消费者所共同定义的声明，同样不建议存放敏感信息。

&emsp;一般来说，Payload如下所示:

```json
{
 "loggedInAs" : "admin",
 "iat" : 1422779638
}
```

&emsp;经过BASE64加密后，结果如下:eyJsb2dnZWRJbkFzIjoiYWRtaW4iLCJpYXQiOjE0MjI3Nzk2Mzh9 。这就是JWT的第二部分。

#### 2.3 Signature

&emsp;第三部分用于验证发送者请求者身份，由前两部分加密形成。

&emsp;例如，按照文本Header中生命的HS256加密(即SHA-256)，加密结果如下:

```json
HMAC-SHA256(
 base64urlEncoding(header) + '.' +
 base64urlEncoding(payload),
 secret
)
```

&emsp;结果为gzSraSYS8EXBxLN_oWnFSRgCzcmJmMjLiuyu5CSpyHI 。

### 3.0 JWT认证模式

### 4.0 JWT问题

### 5.0 参考资料

**深入浅出JWT(JSON Web Token )** <https://www.cnblogs.com/mantoudev/p/8994341.html> 

**JWT生成Token做登录校验** <https://blog.csdn.net/sky_jiangcheng/article/details/80546370> 

**基于JWT的token身份认证方案** <https://www.cnblogs.com/xiangkejin/archive/2018/05/08/9011119.html> 