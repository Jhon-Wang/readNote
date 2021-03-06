# 理解OAuth2.0
## 例子
有一个云冲印的网站，可以将用户存储在Google的照片，冲印出来。用户为了使用该服务，必须让“云冲印”读取自己存储在Google上的照片。
问题是只有得到用户的授权，Google才会同意“云冲印”读取这些照片。name云冲印如何或获取用户的授权呢？
传统方法是，用户将自己的Google用户和密码，告诉云重影，后者就可以读取用户的照片了，但是这样做会有以下几个严重的缺点。
- “云冲印”为了后续的服务，会保存用户的密码，这样很不安全
- Google 不得不部署棉麻登录，而我们知道，单纯的密码登录并不安全
- “云冲印”拥有了获取用户春初在Google所有资料的权利，用户没法限制“云冲印”获得授权的范围和有效期。
- 用户只有修改密码，才能收回赋予“云冲印”的权利，但是这样做是的其他所有获得用户授权的第三方应用全部失效。
- 只要有一个第三方陈旭被破解，就会导致用户密码泄露，以及所有被密码保护的数据泄露。

## 名词定义
- Third-part application ：第三方应用程序，文本中又称为客户端 ，即上面的“云冲印”
- HTTP service ：HTTP 服务提供商，文本中简称“服务提供商”，即上一例子中的Google
- Resource Owner：资源所有者，本文中称“用户”
- User Agent：用户代理，本文中指浏览器
- Authorization server：认证服务器，即服务提供商专门用来处理认证的服务器
- Resource Server ：资源服务器，及服务提供商存放用户生成的资源的服务器。他与认证服务器，可以是同一台服务器也可以是不同的服务器
OAuth 作用是让客户端安全可控的获取用户的授权，与服务提供商 进行互动
## OAuth的思路
Oauth 在客户端与服务提供商之间，设置了一个授权层。客户端不能直接登录服务提供商，只能登录授权层，一次将用户和客户端区分开来。客户端登录授权层所用的令牌，与用户的密码不同。用户可以在登录的时候指定授权令牌的权限范围和有效期。
客户端登录授权层之后，服务提供商根据令牌的权限范围和有效期，想客户端开放用户存储的资料。

## 运行流程
![](./pictrue/OAuth流程图.png)
- 用户打开客户端以后，客户端要求用户给予授权
- 用户同意给予客户端授权
- 客户端使用上一步获得授权，向认证服务器申请令牌
- 认证服务器对客户端进行认证之后确认无误，同意发放令牌
- 客户端使用令牌，向资源服务器申请获取资源
- 资源服务器曲儿令牌无误，同意向客户端开放资源
## 客户端的授权模式
客户端必须得到用户的授权，才能获取令牌，OAuth2定义了四种授权模式
- 授权码模式
- 简化模式
- 密码模式
- 客户端模式

## 授权码模式
授权码模式是功能最完整、流程最严谨的授权模式，他的特点是通过客户端的后台服务器，与“服务提供商”的认证服务器进行互动
![](./pictrue/授权码模式.png)
- A用户访问客户端后者将前者导向服务器
- B 用户选择是否给予客户端授权
- C 假设用户给予授权，认证服务器用户导线客户端事先指定的“重定向URI”，同时附上一个授权码
- D 客户端收到授权码，附上事先的“重定向URI”，向认证服务器申请令牌。这一步是在客户端的后台的服务器上完成的对用户不可见
- E 认证服务器核对了授权码和重定向URI，确认无误后，向客户端发送访问令牌和更新令牌
下面是这些步骤需要的参数：
- response_type：表示授权类型，必选项，此处的值固定为code
- client_id：表示客户端的ID，必选项
- redirect_uri:表示重定向URI，可选项
- scope：表示申请的权限范围，可选项
- state：表示客户端的当前状态，可以指定任意值，认证服务器会原封不动地返回这个值

```
GET /authorize?response_type=code&client_id=s6BhdRkqt3&state=xyz
        &redirect_uri=https%3A%2F%2Fclient%2Eexample%2Ecom%2Fcb HTTP/1.1
Host: server.example.com
```
C步骤中，服务器回应客户端的URI包含以下参数
- code：表示授权码，必选项。该码有效期应该何恩段，通常设为10分钟，客户端只能使用该码一次，否则会被授权服务器拒绝，该码与客户端ID和重定向URI是一一对应关系
- state：如果客户端的请求中包含这个参数，认证服务器的回应也必须一模一样包含这个参数
```
HTTP/1.1 302 Found
Location: https://client.example.com/cb?code=SplxlOBeZQQYbYS6WxSbIA
          &state=xyz
```
D步骤中，客户端向认证服务器申请令牌的HTTP请求，包含以下参数
- grant_type:表示使用的授权模式，必选项，此处的值固定为authorization_code
- code：表示上一步获得的授权码，必选项
- redirect_uri:表示重定向URI，必选项，且必须与A步骤中的该参数保持一致
- client_id:表示客户端ID必选项

	下面是一个🌰：
```
POST /token HTTP/1.1v
Host: server.example.com
Authorization: Basic czZCaGRSa3F0MzpnWDFmQmF0M2JW
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&code=SplxlOBeZQQYbYS6WxSbIA
&redirect_uri=https%3A%2F%2Fclient%2Eexample%2Ecom%2Fcb
```
E 步骤中，认证服务器发送的HTTP请求包含以下参数
- access_token：表示访问令牌，必选项
- token_type:表示令牌类型，该值大小写不敏感，必选项，可以是bearer类型或mac类型
- expires_in:表示过期时间，单位为秒。如果省略该参数，必须其它方式设置过期时间
- refresh_token:表示更新令牌，用来获取下一次的访问令牌，可选项
- scope：表示权限范围，如果与客户端申请的范围一致，此项可省略。

	下面是一个🌰：
```
     HTTP/1.1 200 OK
     Content-Type: application/json;charset=UTF-8
     Cache-Control: no-store
     Pragma: no-cache

     {
       "access_token":"2YotnFZFEjr1zCsicMWpAA",
       "token_type":"example",
       "expires_in":3600,
       "refresh_token":"tGzv3JOkF0XG5Qx2TlKWIA",
       "example_parameter":"example_value"
     }
```
从上面代码可以看到，相关参数使用JSON格式发送。此外，HTTP头明确指定不得缓存
