# Cookies and Sessions

> Cookies和Sessions是密切联系的，是创建丰富交互式网络应用的基础。

## Cookies

> 当用户访问网站时，网站向用户发送一段数据，用户浏览器把数据存入用户本地电脑。这段数据被称为HTTP cookie。

### 为何需要cookie？
* http是无状态的，cookie可以提供一个可靠的状态信息

### 常见应用场景
1. 网上商城，记录用户购买商品信息
2. 记录用户的浏览行为（点击事件、访问路径等）
3. 记录用户表单信息（姓名，地址等）
4. 最常用的应用场景是session认证。服务器利用cookies存储session id来判断用户是否登录，是使用哪个账号进行登录。（认证安全取决于发布网站和用户浏览器，还有是否对cookies数据进行加密。）


### 分类
> session cookie，persistent cookie，secure cookie、HttpOnly cookie、SameSite cookie、Third-party cookie

#### Session cookie
会话cookie（内存cookie、瞬时cookie）由浏览器控制，当浏览器关闭后就会删除，没有过期时间。

#### Persistent cookie
持久cookie有过期时间，在生命周期内每一次向服务器发送请求都会发送cookie。

#### Secure cookie
安全cookie只能通过加密链接发送（例如 HTTPS）无法通过非加密链接发送（例如 HTTP）

#### HttpOnly cookie
HttpOnly cookie不能通过客户端APIs获取，比如Javascript。此方法可以避免跨站点脚本攻击（XSS），但是并无法避免跨站点跟踪（XST）和跨站点伪造请求（XSRF）攻击。

#### SameSite cookie
同源cookie是16年Google Chrome的51版本中引入的，只能在源和目标域相同的源的请求中发送。此方法可以避免跨站点伪造请求（XSRF）攻击。

#### Third-party cookie
第三方cookie...
主要用在网站广告，为追踪用户的浏览历史记录提供了可能性。

### 用途

#### Session管理和个性化
服务器发送唯一会话标识给客户端，之后客户端的每一次请求都会发送这个唯一标识，服务器就能判断请求是来自于哪一个会话（用户）。一方面可以让验证通过的用户访问服务器资源，一方面知道给用户展现哪些个人相关的数据。
#### 跟踪
cookie可以记录用户的浏览路径和时长等，可以对用户的浏览路径进行跟踪。

### 实现
> 通常是通过服务器来设置cookies，也可以通过JavaScript来设置（除了HttpOnly标记的cookie）   

一般设置cookie的服务器响应头信息：
```
HTTP/1.0 200 OK
Content-type: text/html
Set-Cookie: theme=light
Set-Cookie: sessionToken=abc123; Expires=Wed, 09 Jun 2021 10:18:14 GMT
…
```
"theme"为session cookie，将会浏览器关闭后删除
"sessionToken"为持久化cookie，将在过期后删除
在接收到以上响应后，之后对服务器发起请求时，就会自动带上cookie：
```
GET /spec.html HTTP/1.1
Host: www.example.org
Cookie: theme=light; sessionToken=abc123
…
```