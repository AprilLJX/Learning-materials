### 基础概念

#### URI

URI

Uniform Resource Identifier 统一资源标识符
Uiform Resource Locator 统一资源定位符
Uniform Resource Name 统一资源名称

#### 请求和响应报文

1，请求报文
![image-20200218231032906](/Users/jacyn/Documents/study/Learning-materials/image/image-20200218231032906.png)

2.响应报文
![image-20200218231150784](/Users/jacyn/Documents/study/Learning-materials/image/image-20200218231150784.png)

#### HTTP方法

请求报文第一行 请求行 包含方法字段

1. GET
   获取资源

2. HEAD
   获取报文首部：确认URL有效性及资源更新的日期时间

3. POST

   传输数据

4. PUT

   上传文件，不带验证机制，任何人可传存在安全问题

5. PATCH

   对资源部分修改（PUT完全替代）

   ```
   PATCH /file.txt HTTP/1.1 
   Host: www.example.com 
   Content-Type: application/example 
   If-Match: "e0023aa4e" 
   Content-Length: 100
   
   [description of changes]
   ```

6. DELETE

   删除文件，不带验证机制

   ```
   DELETE /file.html HTTP/1.1
   ```

7. OPTIONS

   查询指定URL能支持的方法

   会返回 Allow: GET, POST, HEAD, OPTIONS 这样的内容。

8. CONNECT

   要求与代理服务器通信时建立隧道

   如使用SSL(Secure SOcket Layer，安全套接层)

   ​	TLS(Transport LAyer Security，传输层安全)协议

   把通信内容加密后经过网络隧道传输

9. TRACE

   追踪路径：服务器返回通信路径

   Max-Forwards 首部字段中填入数值，每经过一个服务器就会减 1

   容易收到XST(Cross-Site Tracing )跨站追踪

#### HTTP状态码

响应报文第一行为状态行，包含状态码以及原因短语

| 状态码 | 类别                             | 含义                       |
| ------ | -------------------------------- | -------------------------- |
| 1XX    | Informational(信息性状态码)      | 接收的请求正在处理         |
| 2XX    | Success（成功状态码）            | 请求正常处理完毕           |
| 3XX    | Redirection（重定向状态码）      | 需要进行附加操作以完成请求 |
| 4XX    | CLient Error（客户端错误状态码） | 服务器无法处理请求         |
| 5XX    | Server Error（服务器错误状态码） | 服务器处理请求出错         |

1. 1XX信息
   100 COntinue：到目前为止正常

2. 2XX 成功
   200 ok
   204 No Content：请求亿成功处理，返回效应报文不包含实体的主体部分；用于向服务器只需发送信息，不需返回数据

   206 Partial Content：客户端进行范围请求，响应报文包含由Content-Range指定范围的实体内容

3. 3XX 重定向
   301 Moved premanently 永久重定向
   302 Found 临时性重定向
   303 See Other 同上，要求客户端改成GET
   大多数都会在301，302，303时把POST改为GET
   304 Not Modified 请求报文若含有If-Match，If-Modiﬁed-Since，If-NoneMatch，If-Range，If-Unmodiﬁed-Since条件，若不满足则返回304
   307 Temporary Redirect 临时重定向，类似302，不把post改get

4. 4XX 客户端错误
   400 Bad Request 请求报文存在语法错误
   401 Unauthorized 发送请求中需要有认证信息（BASIC 认证、DIGEST 认证）；若之前已执行过一次请求，表示用户认证失败
   403 Forbidden 请求被拒绝
   404 Not Found

5. 5XX 服务器错误
   500 Internal Server Error 服务器正在执行请求时出错
   503 Server Unavailable 服务器处于超负载或正在停机维护，无法处理请求

#### HTTP首部

##### 通用首部字段

![image-20200219202629573](/Users/jacyn/Documents/study/Learning-materials/image/image-20200219202629573.png)

##### 请求首部字段

![image-20200219202659025](/Users/jacyn/Documents/study/Learning-materials/image/image-20200219202659025.png)

##### 响应首部字段

![image-20200219204612299](/Users/jacyn/Documents/study/Learning-materials/image/image-20200219204612299.png)

##### 实体首部字段

![image-20200219204628891](/Users/jacyn/Documents/study/Learning-materials/image/image-20200219204628891.png)

#### 具体应用

##### 连接管理

![image-20200219205330767](/Users/jacyn/Documents/study/Learning-materials/image/image-20200219205330767.png)

1. 短连接
   进行一次HTTp通信，新建一个TCP连接，开销大
2. 长连接
   建立一次TCP连接进行多次HTTP通信
   HTTP/1.1开始默认长连接，断开使用connection：close
   之前默认短连接，Connection：keep-Alive使用长连接
3. 流水线
   在同一条长连接上连续发出请求，buoying等待响应返回，减少延迟

##### Cookie

HTTP协议无状态(简单，可处理大量事务)--->HTTP/1.1后引入cookie保存状态信息
**Cookie**：服务器发送到用户浏览器并保存在本地的一小块数据，在浏览器向同一服务器再次发起i去那个i去时被携带--->告知服务端请求来自同一浏览器--->梅西请求需携带cookie，额外新能开销

###### 用途

1. 会话状态管理（用户登录状态，购物车。。）
2. 个性化设置（自定义主题）
3. 浏览器行为跟踪（跟踪分析用户行为）

###### 创建过程

1. 服务器响应报文包含Set-Cookie首部字段-->客户端把Cookie内容保存在浏览器

   ```
   HTTP/1.0 200 OK Content-type: text/html Set-Cookie: yummy_cookie=choco Set-Cookie: tasty_cookie=strawberry
   
   [page content]客户端
   ```

2. 客户端之后对同一服务器发请求时，先从浏览器去除Cookie信息并通过Cookie请求首部字段发送给服务器

   ```
   GET /sample_page.html HTTP/1.1 Host: www.example.org Cookie: yummy_cookie=choco; tasty_cookie=strawberry
   ```

###### 分类

会话期Cookie：浏览器关闭-->自动删除，尽在会话期有效
持久性Cookie：指定过期时间（Expires）或有效期（max-age）之后成为持久性的Cookie

```
Set-Cookie: id=a3fWa; Expires=Wed, 21 Oct 2015 07:28:00 GMT;
```

###### 作用域

Domain标识指定哪些主机可以接受Cookie
不指定：默认为当前文档主机，不包含子域名
指定：包含子域名
Path标识：指定主机下的哪些路径可接受(该URL路径必须存在于请求URL中)

###### JavaScript

浏览器通过document.cookie属性可创建新的Cookie，或访问非HttpOnly标记的Cookie

```
document.cookie = "yummy_cookie=choco"; document.cookie = "tasty_cookie=strawberry"; console.log(document.cookie);
```

标记为HttpOnly的cookie--->不能被JavaScript脚本调用，一定程度上避免XSS(跨站脚本攻击，常使用JavaScript的document.cookie API窃取用户Cookie信息
标记为Secure的Cookie只能通过被HTTPS协议加密过的请求发送

###### Session

用户信息存储在服务器端
1.用户登录---提交用户名密码的表单---HTTP请求报文
2.服务器验证，正确则存入Redis，其key称为Session ID
3.服务器返回响应报文，其Set-Cookie首部字段包含Session ID，客户端保存Cookie
4.客户端再次发送，服务器提取Session ID，从Redis中提取用户信息
安全性问题：要求不能被轻易获取
浏览器禁用Cookie时，只能使用Session，且使用URL重写技术，将Session ID作为URL的参数传递

###### Cookie 与Session

1.Cookie只能存储ASCII，Session可存储任意类型--->数据复杂性首选Session
2.Cookie存储在浏览器，易被恶意查看---隐私出局非要存在Cookie中，将Cookie值加木，在服务器解密
3.大型网站 ，不建议所有用户信息存储在Session，开销极大

##### 缓存

**优点**：1.缓解服务器压力 2.降低客户端获取资源的延迟（缓存通常位于内存中，读取速度快）
**实现方法**：1.代理服务器缓存 2.客户端浏览器缓存
**Cache-Control**首部字段控制缓存 HTTP/1.1

```
1.禁止进行缓存
Cache-Control：no-store
2.强制确认缓存
规定缓存服务器先向源服务器验证缓存资源的有效性，有效时可用于响应
Cache-Control：no-cache
3.私有缓存：单独用户使用，存于用户浏览器
	Cache-Control: private
	公共缓存：多个用户使用，代理服务器
	Cache-Control: public
4.缓存过期机制
max-age
请求报文，缓存资源的缓存时间小于该指令指定时间，可接受缓存；
响应报文，表示缓存资源在缓存服务器中保存的时间
Cache-Control: max-age=31536000
Expires 首部字段：告知缓存server资源什么时候过期
Expires: Wed, 04 Jul 2012 08:26:05 GMT

在 HTTP/1.1 中，会优先处理 max-age 指令； 在 HTTP/1.0 中，max-age 指令会被忽略掉。
```

**缓存验证**

ETag首部字段：资源的唯一标识
缓存资源ETag放入If-None-Match首部--->服务器验证ETag，一致则有效，返回304 Not Modiﬁed
Last-Modiﬁed 首部字段：包含在源服务器发送的响应报文中，白表示源服务器中对该资源的最后修改时间，弱校验器，作为ETag备用。
若响应报文有上字段，客户端在请求中带入If-Modiﬁed-Since 来验证缓存。
			有修改：服务器返回资源，200 OK；
			无修改：返回304 Not Modiﬁed，且无实体主体

```
Last-Modified: Wed, 21 Oct 2015 07:28:00 GMT

If-Modified-Since: Wed, 21 Oct 2015 07:28:00 GMT
```

##### 内容协商

返回最合适的内容，例如根据浏览器默认语言返回对应界面
